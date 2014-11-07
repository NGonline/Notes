[TOC]

----------
# MapReduce
- 关系型数据库与MapReduce的比较：
| |关系型数据库|MapReduce|
| - | - | - |
|数据大小|GB|PB|
|访问|交互式和批处理|批处理|
|更新|多次读写|一次写入多次读取|
|结构|静态模式|动态模式|
|完整性|高|低|
|横向扩展|非线性|线性|

- MapReduce的核心假设之一就是，它可以进行（高速的）流式读写操作。
- 与普通的高性能计算不同，MapReduce的核心特性是**数据本地化**。它认为网络带宽是数据中心环境最珍贵的资源，并通过显示网络拓扑结构尽力保留网络带宽。
- MapReduce在更高层次上执行任务，即程序员仅从键值对函数的教徒考虑任务的置信，这样数据流是隐含的，不需要显式控制。
- 由于采用了无共享（shared-nothing）框架，所以系统实现能够检测到部分失效。相比之下，MPI（Message Passing Interface）程序必须显式地管理自身的检查点和恢复机制。

# Hadoop
- Hadoop将MapReduce的输入数据划分成等长的小数据块，成为输入分片(input split)。Hadoop为每个分片构建一个map任务，并在存储有输入数据的节点上运行map任务以获得最佳性能（data locality optimization）。分片越小负载平衡的质量会更好。
- HDFS的一个块的大小默认为64MB。最佳分片大小应当与块大小相同，因为它是确保可以存储在单个节点上的最大输入块大小。
- map任务将其输出写入本地硬盘，而非HDFS，以减小开销。如果该节点上运行的map任务在将中间结果传送给reduce任务之前失败，则将在另一个节点上重新运行这个map任务。
-reduce任务并不具备数据本地化的优势。如有多个reduce任务，则每个map都会对其输出进行分区（partition），即每个reduce建一个分区。每个分区有许多键，但每个键对应的键值对记录都在同一个分区中。分区由用户定义的分区函数控制，通常采用默认的分区器通过哈希函数来进行。
- map和reduce之间的数据流成为shuffle。调整shuffle的参数对作业总执行时间会有非常大的影响。
-为了尽量避免map和reduce间的数据传输，可以用combiner合并mapper的输出结果。必须保证不经过多少次合并，reducer的输出结果保持一致（f(x1,x2,x3,x4)=f(f(x1,x2),f(x3,x4))），具有该属性的函数成为是“分布式的”）。合并函数是通过reducer接口来定义的。
- Streaming和Java MapReduce API之间存在设计差异。Java API控制的map函数一次只能处理一条记录。然而在Streaming中，map程序可以自己决定如何处理输入数据。
- Streaming中可以将mapper或reducer改成流水线：
```
hadoop jar hadoop-*-streaming.jar \
    -input input/all \
    -output output \
    -mapper "mapper.py | sort | combiner.py" \
    -reducer reducer.py \
    -file mapper.py \
    -file combiner.py \
    -file reducer.py
```
- Hadoop的Pipes是C++接口的代称，Pipes使用套接字作为tasktracker与C++版本的map或reduce函数的进程之间的通道，而未使用JNI。我们可以用重载模板factory设置mapper、reducer、combiner、partitioner、record reader或record writer。

# HDFS
- HDFS假设每次分析都将涉及数据集的大部分数据甚至全部，因此读取整个数据集的时间延迟比读取第一条记录的时间延迟更重要。
- 要求低时间延迟数据访问的应用，例如几十毫秒范围，不适合在HDFS上运行，而很适合HBase。HDFS为高数据吞吐量应用优化的，这可能会以高时间延迟为代价。
- 文件系统所能存储的文件总数量受限于namenode的内存容量。根据经验，每个文件、目录和数据块的存储信息大约占150字节。
- HDFS中的文件可能只有一个writer，而且写操作总是将数据添加在文件的末尾。它不支持具有多个写入者的操作，也不支持在文件任意位置进行修改。
- HDFS的块默认为64MB。HDFS上的文件划分为块大小的多个分块（chunk）。但与其他文件系统不同的是，HDFS中小于一个块大小的文件不会占据整个块的空间。
- HDFS的块远大于磁盘块的目的是最小化寻址开销，使磁盘传输数据的时间可以明显大于定位这个块开始位置所需的时间。很多情况下HDFS使用128MB的块设置。随着磁盘驱动器传输速率的提升，块大小将被设置得更大。注意该参数如果设置得过大，由于map任务通常一次处理一个块中的数据，负载将不平衡。
- 块抽象的最明显好处就是一个文件的大小可以大于网络中任意一个磁盘的容量。另一个好处是简化了存储子系统的设计，因为块的大小固定计算方便，同时消除了元数据的顾虑（让其他系统单独地管理这些元数据）。与此同时，块也非常适合用于数据备份进而提供容错能力和可用性。`fsck`指令可以显示块信息，例如`hadoop fsck / -files -blocks`列出各个文件由哪些块构成。
- HDFS集群有两类节点，并以管理者-工作者模式运行，即一个namenode和多个datanode。namenode维护着文件系统树以及整棵树内所有的文件和目录。这些信息以两个文件形式永久保存在本地磁盘上：命名空间镜像文件和编辑日志文件。namenode也记录每个文件中各个块所在的数据节点信息，但它不会永久保存块的位置信息，因为这些信息会在系统启动时由数据结点重建。
- datanode根据需要存储并检索数据块，并定期向namenode发送它们所存储的块的列表。
- Hadoop为namenode提供了两种机制：
 - 备份那些组成文件系统元数据持久状态的文件，这些写操作是实时同步的，是原子操作。一般将持久状态写入本地磁盘的同时，写入一个远程挂载的网络文件系统（NFS）
 - 运行一个辅助namenode，定期通过比恩及日志合并命名空间镜像，防止编辑日志过大，并在namenode发生故障时启用。辅助namenode保存的状态总是滞后于主节点，所以难免会丢失部分数据，这种情况下一般把存储在NFS上的namenode元数据复制到辅助namenode
- 设置伪分布式配置时，有两个属性需要注意。fs.default.name设置为hdfs://localhost/，用于设置Hadoop的默认文件系统，HDFS的守护程序将通过该属性来确定namenode的主机及端口；dfs.replication应当设为1，这样HDFS不会按默认设置将文件系统块拷贝数设为3。
- ls命令返回的第二列是这个文件的备份数，目录作为元数据保存在namenode中，所以没有备份数。
- Hadoop有一个抽象的文件系统概念，HDFS只是其中的一个实现。Java抽象类org.apache.hadoop.hs.FileSystem定义了Hadoop中的一个文件系统接口，并且该抽象类有几个具体实现。
- 尽管运行的MapReduce程序可以访问任何文件系统，但在处理大数据集时，你仍然需要选择一个具有数据本地优化的分布式文件系统，如HDFS或KFS。
- Hadoop为执行通配提供了两个FileSystem方法：
```
public FileStatus[] globStatus(Path pathPattern) throws IOException
public FileStatus[] globStatus(Path pathPattern, PathFilter filter) throws IOException
```
支持的通配符与bash相同：`*` `?` `[ab]` `[^ab]` `[a-b]` `[^a-b]` `{a,b}`(a或b中的一个表达式) `\c`(转义字符)
- FileStatus[]可以通过FileUtil.stat2Paths(status)转化为Path[]
- `hadoop ListStatus`可以展示一组路径集目录列表的并集

## 读取文件
- `DistributedFileSystem`通过使用RPC来调用namenode，以确定文件起始块的位置。对于每一个块，namenode返回存有该块副本的datanode地址。此外，这些datanode根据它们与客户端的距离来排序（根据集群的网络拓扑）。如果该客户端本身就是一个datanode，并存有相应数据块的一个副本时，则该节点将从本地读取数据。
- 通过对数据流反复调用`read()`方法，可以将数据从datanode传输到客户端。到达块末时，`DFSInputStream`会关闭与该datanode的连接，然后寻找下一个块的最佳datanode，客户端只需要读取连续的流。
- 读取时，块是按照打开`DFSInputStream`与datanode新建连接的顺序读取的，它也需要询问namenode来检索下一批所需块的datanode的位置。一旦客户端完成读取，就对`FSDataInputStream`调用`close()`方法。
- 如果`DFSInputStream`在与datanode通行时遇到错误，它会尝试从另一个最邻近datanode读取数据。它也会记住那个故障datanode，以保证以后不会反复读取该节点上后续的块。`DFSInputStream`也会通过校验和确认从datanode发来的数据是否完整。
- namenode告知客户端每个块中最佳的datanode，并让客户端直接联系该datanode且检索数据。namenode仅需要响应块位置的请求（信息存储在内存中，非常高效），而无需响应数据请求，否则随着客户端数量的增长，namenode很快会成为瓶颈。
- 衡量节点之间的带宽实际上很难实现，Hadoop采用一个简单的方法，把网络看成一棵树，两个节点之间的距离是它们到最近的公共祖先的距离总和。树的层次没有预先设点，但通常可以设定等级。具体的想法是按以下顺序，可用带宽依次递减：
 - 同一节点中的进程
 - 同一机架上的不同节点
 - 同一数据中心中不同机架上的节点
 - 不同数据中心中的节点
- 在默认情况下，假设网络是平铺的，仅有单一层次，即所有节点都在同一机架上。小的集群可能如此，所以不需要进一步配置。

## 写入文件
- 客户端调用`DistributedFileSystem`对象的`create()`方法创建文件。先对namenode创建一个RPC调用，在文件系统的命名空间中创建一个文件。
- namenode执行各种检查确保这个文件不存在，并保证客户端有创建文件的权限。如果检查通过，namenode会创建新文件的一条记录。`DistributedFileSystem`则向客户端返回`FSDataOutputStream`对象开始写入
- 写入是分为数据包写入data queue。`DataStreamer`根据datanode列表要求datanode分配合适的新块存储数据备份。一组datanode构成一个管线（个数等于副本数），前一个写入数据包并发送到下一个。
- `DFSOutputStream`维护ack queue等待datanode的确认回执。如果写入期间datanode发生故障，则先关闭管线，将队列中的所有数据包都添加回data queue的最前端，以确保下游的datanode不会漏掉任何一个数据包；然后为存储在另一个正常datanode的当前数据块指定一个新的标识，一遍故障datanode回复后删除存储的部分数据块；从管线删除故障结点并将余下的数据块写入管线中正常的datanode；namenode发现复本不足时，会在另一个节点上创建新的复本，后续数据块继续正常接受处理。
- 一个块写入期间只要成功写入了`dfs.replication.min`的复本数，写操作就会成功，并且这个块可以在集群中异步复制
- 客户端完成写入后，会调用`close()`方法，将剩余所有数据包写入管线中，并在确认后联系namenode并发送完成信号。
- datanode的选择权衡了可靠性、写入带宽和读取带宽。默认第一个复本在运行客户端的节点上，第二个在不同且随机的机架上，第三个与第二个在同一机架但不同节点，后续的在整个集群的随机节点上。节点和机架的选择会避免存储太慢或太忙的节点：
 - 稳定性&负载均衡：两个机架
 - 写入带宽：写入只需要遍历一个交换机
 - 读取性能：两个机架中选择读取
 - 块的均匀分布：只在本地机架写入一个块

## 一致模型
- HDFS为了性能牺牲了一些POSIX要求。在创建一个文件之后，其在文件系统的命名空间中立即可见。但是，写入文件的内容并不保证立即可见，即使数据流已经刷新并存储。当写入的数据超过一个块后，新的reader就能看见第一个块，其他reader无法看见当前正在写入的块。
- `sync()`方法强制所有缓存与数据结点同步，当其返回成功后，所有新的reader都可以看见到目前为止写入的数据
```
Path p = new Path("p")
FSDataOutputStream out = fs.create(p);
out.write("content".getBytes("UTF-8"));
out.flush();
out.sync();
```
- 在HDFS中关闭文件其实还隐含执行了`sync()`方法。
- 如果不调用`sync()`方法，则需要准备好在故障时可能会丢失一个数据块。但该操作有较多的额外开销，需要在鲁棒性和吞吐量之间进行权衡。