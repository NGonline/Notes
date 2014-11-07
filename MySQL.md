[TOC]

# MySQL

# Overview
- For all intents and purposes, the MySQL Server spawns one thread per connection and that thread alone performs everything needed to service that connection. Thus, parallelism in the MySQL Server is gotten from executing many concurrent queries rather than one query concurrently. (Multiplex connections over a set of threads are not commonly deployed and we can largely ignore them.)
- The MySQL Server will cache threads (the amount is configurable) so that it doesn¡¯t have to have the overhead of pthread_create() for each new connection.
- Because the MySQL Server is a collection of threads, there¡¯s going to be thread local data (e.g. connection specific) and shared data (e.g. cache of on disk data). This means mutexes and atomic variables. Most of the more advanced ways of doing concurrency haven¡¯t yet made it into MySQL.
- The query is sent to the server where it is parsed, optimized, executed and the result returns to the client.
- On modern systems, enabling the query cache can drop server performance by an order of magnitude. A single global lock is a really bad idea.
- Due to the previously mentioned not-quite-well-separated parse, optimize, execute steps, unless you¡¯re executing the same query many times in a single connection, server side prepared statements aren¡¯t worth the network round trips.
- The optimizer looks at a data structure describing the query and works out how to execute it. Remember: SQL is declarative, not procedural. The optimizer will access various table and index statistics in order to work out an execution plan. It may not be the best execution plan, but it¡¯s one that can be found within reasonable time. You can find out the query plan for a SELECT statement by prepending it with EXPLAIN.
- A lot of MySQL performance problems are due to complex SQL queries that don¡¯t play well with the optimizer. If there¡¯s one thing the MySQL optimizer does well it¡¯s making quick, pretty good decisions about simple queries. This is why MySQL is so popular ¨C fast execution of simple queries.
- To get table and index statistics, the optimizer has to ask the Storage Engine(s). In MySQL, the actual storage of tables (and thus the rows in tables) is (mostly) abstracted away from the upper layers.
- MyISAM was non-transactional but relatively fast, especially for read heavy workloads. It only allowed one writer although there could be many concurrent readers. The current default storage engine is called InnoDB. It¡¯s all the buzzwords like ACID and MVCC. Just about every production environment is going to be using InnoDB. MyISAM is effectively deprecated. However, it also has its own scalability issues.
- Once the optimizer has worked out how to execute the query, MySQL will start executing it. MySQL (server side) will open the tables, looking up the MySQL Server table definition cache and creating a MySQL Server side table share object which is shared amongst the open table instances for that table. In MySQL 5.6, there is table_open_cache_instances, which splits the table_open_cache mutex into table_open_cache_instances mutexes to help reduce lock contention on machines with many CPU cores (> 8 or >16 cores, depending on workload).
- Once tables are opened, there are various access methods that can be employed. Table scans are the worst (start at the start and examine every row). There¡¯s also index scans (often seeking to part of the index first) and key lookups.
- You have a number of shared data structures about tables and then a data structure for each open table. To actually read/write things to/from tables, you¡¯re going to have to get some data to/from disk.
- InnoDB tables can be stored either in one giant table space or file-per-table. (Even though it¡¯s now configurable), InnoDB database pages are 16kb. Database pages are cached in the InnoDB Buffer Pool, and the buffer-pool-size should typically be about 80% of system memory. InnoDB will use a (configurable) method to flush.
- InnoDB will do some of its IO in the thread that is performing the query and some of it in helper threads using native linux async IO (again, that¡¯s configurable). There exists innodb_buffer_pool_instances configuration option which will split the buffer pool into several instances to help reduce lock contention on the InnoDB buffer pool mutex.
- All InnoDB tables have a clustered index. This is the index by which the rows are physically sorted by.
- Every page in InnoDB has a checksum. There was an original algorithm, then there was a ¡°fast¡± algorithm in some forks and now we¡¯re converging on crc32, mainly because Intel implemented CPU instructions to make that fast.
- InnoDB has both REDO and UNDO logging to keep both crash consistency and provide consistent read views to transactions. These are also stored on disk, the redo logs being in their own files (size and number are configurable). The larger the redo logs, the longer it may take to run recovery after a crash. The smaller the redo logs, the more trouble you¡¯re likely to run into with large or many concurrent transactions.
- If your query performs writes to database tables, those changes are written to the REDO log and then, in the background, written back into the table space files. There exists configuration parameters for how much of the InnoDB buffer pool can be filled with dirty pages before they have to be flushed out to the table space files.
- In order to maintain Isolation (I in ACID), InnoDB needs to assign a consistent read view to a new transaction. Transactions are either started explicitly (e.g. with BEGIN) or implicitly (e.g. just running a SELECT statement).
- Fancy things that make InnoDB generally faster than you¡¯d expect are the Adaptive Hash Index and change buffering. There are, of course, scalability challenges with these too.
- If you end up reading or writing rows (quite likely) there will also be a translation between the InnoDB row format(s) and the MySQL Server row format(s).
- Query execution may need to get many rows from many tables, join them together, sum things together or even sort things. If there¡¯s an index with the sort order, it¡¯s better to use that. MySQL may also need to do a filesort (sort rows, possibly using files on disk) or construct a temporary table in order to execute the query. Temporary tables are either using the MEMORY (formerly HEAP) storage engine or the MyISAM storage engine. Generally, you want to avoid having to use temporary tables ¨C doing IO is never good.
- Once you have the results of a query coming through, you may think that¡¯s it. However, you may also be part of a replication hierarchy. If so, any changes made as part of that transaction will be written to the binary log. This is a log file maintained by the MySQL Server (not the storage engines) of all the changes to tables that have occured. This log can then be pulled by other MySQL servers and applied, making them replication slaves of the master MySQL Server.
- The binary log (binlog for short) is a file on disk that is appended to until it reaches a certain size and is then rotated. Writes to this file vary in size (along with the size of transactions being executed).
- If your MySQL Server is a replication slave, then you have a thread reading the binary log files from another MySQL Server and then another thread (or, in newer versions, threads) applying the changes.
- If the slow query log or general query log is enabled, they¡¯ll also be written to at various points ¨C and the current code for this is not optimal, there be (yes, you guess it) global mutexes.
- Once the results of a query have been sent back to the client, the MySQL Server cleans things up (frees some memory) and prepares itself for the next query. You probably have many queries being executed simultaneously, and this is (naturally) a good thing.

# Syntax

## INSERT
```
INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name [(col_name,...)]
    VALUES ({expr | DEFAULT},...),(...),...
    [ ON DUPLICATE KEY UPDATE col_name=expr, ... ]

INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name
    SET col_name={expr | DEFAULT}, ...
    [ ON DUPLICATE KEY UPDATE col_name=expr, ... ]

INSERT [LOW_PRIORITY | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name [(col_name,...)]
    SELECT ...
    [ ON DUPLICATE KEY UPDATE col_name=expr, ... ]
```

## UPDATE
```
UPDATE [LOW_PRIORITY] [IGNORE] table_reference
    SET col_name1={expr1|DEFAULT} [, col_name2={expr2|DEFAULT}] ...
    [WHERE where_condition]
    [ORDER BY ...]
    [LIMIT row_count]
    
UPDATE [LOW_PRIORITY] [IGNORE] table_references
    SET col_name1={expr1|DEFAULT} [, col_name2={expr2|DEFAULT}] ...
    [WHERE where_condition]
```