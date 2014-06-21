[TOC]

----------
## Introduction

SQL only knows tables, and every operation produces tables. It either "produces" a table by modifying an existing one, or it returns a new temporary table as your data set.

One of the reasons Object Oriented languages are mismatched with SQL databases is that OOP languages are organized around graphs, but SQL wants to only return tables. Since it's possible to map nearly any graph to a table and vice-versa this works, but it places a lot of work on the OOP language to do the translation. If SQL returned a nested data structure then this wouldn't be a problem.

Another place that causes a mismatch is in SQL concepts such as ternary relationships and attributed relationships, which OOP just completely does not understand. In SQL I can make 3 tables related to each other using a 4th table, and that 4th table is a cohesive relationship. To do the same thing in an OOP language I have to make a whole intermediary class that encodes this relationship, which is kind of weird in OOP.

----------
## Exercise 1: Creating Tables

```sql
CREATE TABLE person (
    id INTEGER PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    age INTEGER
);
```
Use `sqlite3 ex1.db < ex1.sql` to run the source code.
Note that SQL is *mostly* a case-insensitive language.

----------
## Exercise 2: Creating A Multi-Table Database

```sql
CREATE TABLE person (
    id INTEGER PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    age INTEGER
);

CREATE TABLE pet (
    id INTEGER PRIMARY KEY,
    name TEXT,
    breed TEXT,
    age INTEGER,
    dead INTEGER
);

CREATE TABLE person_pet (
    person_id INTEGER,
    pet_id INTEGER
);
```
`.schema` prints the commands which are used to create this table. So it should output things match what you typed in.

----------
## Exercise 3: Inserting Data

```sql
INSERT INTO person (id, first_name, last_name, age)
    VALUES (0, "Zed", "Shaw", 37);

INSERT INTO pet (id, name, breed, age, dead)
    VALUES (0, "Fluffy", "Unicorn", 1000, 0);

INSERT INTO pet VALUES (1, "Gigantor", "Robot", 1, 1);
```
The second form is dangerous since some databases don't have reliable ordering for the columns.

`-echo` argument prints out what **sqlite3** is doing (code followed by result).

----------
## Exercise 5: Selecting Data

```sql
SELECT * FROM person;
SELECT name, age FROM pet;
SELECT name, age FROM pet WHERE dead = 0;
SELECT * FROM person WHERE first_name != "Zed";
```

----------
## Exercise 6: Select Across Many Tables

```
SELECT pet.id, pet.name, pet.age, pet.dead
    FROM pet, person_pet, person
    WHERE
    pet.id = person_pet.pet_id AND
    person_pet.person_id = person.id AND
    person.first_name = "Zed";
```

----------
## Exercise 7: Deleting Data

`DELETE FROM pet WHERE dead = 1;` is used to delete rows.
`DROP TABLE pet` is used to delete the whole table.

----------
##Exercise 8: Deleting Using Other Tables

`DELETE` is like `SELECT` but it removes rows from the table. The limitation is you can only delete from one table at a time.
```
DELETE FROM pet WHERE id IN (
    SELECT pet.id
    FROM pet, person_pet, person
    WHERE
    person.id = person_pet.person_id AND
    pet.id = person_pet.pet_id AND
    person.first_name = "Zed"
);

SELECT * FROM pet;
SELECT * FROM person_pet;

DELETE FROM person_pet
    WHERE pet_id NOT IN (
        SELECT id FROM pet
    );

SELECT * FROM person_pet;
```

----------
## Exercise 9: Updating Data

```
UPDATE person SET first_name = "Hilarious Guy"
    WHERE first_name = "Zed";
UPDATE pet SET name = "Fancy Pants"
    WHERE id=0;
```
`SET` indicates the columes that should be updated (can have multiple columes using `,`), followed by a subquery selecting the lines that should be updated.

----------
## Exercise 10: Updating Complex Data

```
UPDATE pet SET name = "Zed's Pet" WHERE id IN (
    SELECT pet.id
    FROM pet, person_pet, person
    WHERE
    person.id = person_pet.person_id AND
    pet.id = person_pet.pet_id AND
    person.first_name = "Zed"
);
```
SQL As Understood By SQLite: http://www.sqlite.org/lang.html

----------
## Exercise 11: Replacing Data

```
/* This should fail because 0 is already taken. */
INSERT INTO person (id, first_name, last_name, age)
    VALUES (0, 'Frank', 'Smith', 100);
/* We can force it by doing an INSERT OR REPLACE. */
INSERT OR REPLACE INTO person (id, first_name, last_name, age)
    VALUES (0, 'Frank', 'Smith', 100);
SELECT * FROM person;
/* And shorthand for that is just REPLACE. */
REPLACE INTO person (id, first_name, last_name, age)
    VALUES (0, 'Zed', 'Shaw', 37);
/* Now you can see I'm back. */
SELECT * FROM person;
```
In this situation, I want to replace my record with another guy but keep the unique id. Problem is I'd have to either do a DELETE/INSERT in a transaction to make it atomic, or I'd need to do a full UPDATE.
Some database may not have `REPLACE` command.

----------
## Exercise 12: Destroying And Altering Tables

```
/* Only drop table if it exists. */
DROP TABLE IF EXISTS person;
/* Create again to work with it. */
CREATE TABLE person (
    id INTEGER PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    age INTEGER
);
/* Rename the table to peoples. */
ALTER TABLE person RENAME TO peoples;
/* Add a hatred column to peoples. */
ALTER TABLE peoples ADD COLUMN hatred INTEGER;
/* Rename peoples back to person. */
ALTER TABLE peoples RENAME TO person;
.schema person
/* We don't need that. */
DROP TABLE person;
```
where `.schema person` will print:
```
CREATE TABLE "person" (
    id INTEGER PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    age INTEGER
, hatred INTEGER);
```
Typically `ALTER TABLE` is a mashup of just about everything a database vendor couldn't put into their SQL syntax

----------
## Exercise 14: Basic Transactions

In a real situation you can't trash your whole database when you mess up.
What you need to make your script safer is the `BEGIN`, `COMMIT`, and `ROLLBACK` commands. These start a transaction, which creates a "boundary" around a group of SQL statements so you can abort them if they have an error.
You start the transaction with `BEGIN`, do your SQL, and then when everything's good end the transaction with `COMMIT`. If you have an error then you just issue `ROLLBACK` to abort what you did.
```
sqlite> .read code.sql
sqlite> BEGIN;
sqlite> .read ex14.sql
Error: near line 5: near "person_pet": syntax error
sqlite> ROLLBACK;
sqlite> .schema
CREATE TABLE person (
    id INTEGER PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    age INTEGER
);
CREATE TABLE person_pet (
    person_id INTEGER,
    pet_id INTEGER
);
CREATE TABLE pet (
    id INTEGER PRIMARY KEY,
    name TEXT,
    breed TEXT,
    age INTEGER,
    dead INTEGER
);
```
The alternative syntax is `BEGIN TRANSACTION`, `COMMIT TRANSACTION`, and `ROLLBACK TRANSACTION`.
Some databases don't have the same rollback and commit semantics as other databases. Some also don't understand the syntax with the word TRANSACTION.

----------
## Exercise 15: Data Modeling

By normalizing a database you take repeated data in columns and place them in other tables where they are only mentioned once. In database design this is called Normal Form and usually abbreviated with NF.

There are a few more similarities between DRY and NF:
 - You can take DRY and NF too far until you have removed all the repetition but in the process make the system almost impossible to understand. This is similar to "compressing" the code with gzip. Sure there's no repetition, but then nobody can read it either.
 - You can add horrible inefficiency to the system by creating so many interlocking pieces that the weight of communication crushes any activity. In software this comes from continually moving every "repetition" into more indirect pieces of the code until doing simple things takes 10 function calls with 20 objects. In databases you see this when doing simple queries requires 10 tables and hundreds of rows and foreign keys.
 - You can remove repetition instead of redesigning the system with a new simpler design. You then end up with a system that is cleaner, but still flawed and is most likely more convoluted but nobody remembers the old system so there's no history to explain why it is what it is.

- **conceptualize**
Map out all "concepts" or objects in the system and their cardinality.
 - If you can work out the user interface on paper and how people flow between them, then you can pull out all the major concepts fairly easily.

- **identify**
Give the concepts all names and create unique integer primary keys.
 - Once you've identified the concepts, give them simple names that are singular. Think of the tables as being like classes, and the rows as being like instances of that class or objects.
 - Once you have their identities, then remember that the very first column (attribute) you'll make is a unique primary key integer. Do not be tempted by the dark side and try to use a composite key. These fail all the time because there's always a probability that the data is not unique, so it can't be relied on.

- **relate**
Draw a simple diagram showing how each concept is related.
 - First step is to create a diagram that has boxes for each concept, and then a line between them if they are connected somehow. When you have all the connections listed, give them names that are either the two tables (like person_pet, or some descriptive name if that makes sense like "owns".

- **itemize**
Write down the cardinality of the relationships as many-to-many or one-to-one, rarely one-to-many.
 - To do this you have to fill in the following statement for each relation and the tables it refers to: `Y has (one|many) X, and X has (one|many) Y.`
 - Don't make the mistake of attributing some "parent child" form to these relationships. They might be in your software, but in the database they are just tables connected by other tables and columns. There is no hierarchy.
 - Find any "asymmetric" relationships that are one-to-many or many-to-one (same thing) and consider changing them to many-to-many.
 - Look for any three tables that have "triangle" connections that seem to always exist, and consider making a single "ternary relation". A ternary relation is a single table that connects three other tables together, with the idea being that these three tables can only be connected all at once or not at all.

- **atomize**
Break out each concept into its attributes and parts.
 - Pick the loosest biggest types you can and worry about efficiency later when you have a need.

- **reduce**
Get rid of anything you don't actually need. It's easy to add later, harder to remove.
 - In software it's hard to take features away, so you try to remove as much as possible.
 - Keep notes about what you original designed, and then only add it later when you do need it.

- **normalize**
Move duplication around to improve consistency and reduce repetition.
 - Your job is to now go through all of the attributes and attempt to remove any duplication that makes sense.
 - Find any group of attributes (columns) that might be concepts on their own, or are possible "parent child" relationships in disguise.
 - Take any columns that do not change often and consider making a table to store them as a list then reference them by id.

- **denormalize**
Denormalize tables if you must.
 - If there's data that is better as a big flat blob then I either don't use a SQL database, use a view, or denormalize the table.
 - The place to look for is any information that is heavily read.
 - You should first run some benchmarks, then try to tune the database and your queries, then try to denormalize the tables in question.
 - Sometimes doing some of the work in your code is a huge time savings over doing it in the database.

Implementing the SQL:
1. Create all the tables and just their primary key ids. No other attributes. Get that working.
2. Now create all the relation tables, sticking to the convention of TABLE_TABLE for each of them. Use columns named TABLE_id for each of the relation tables columns.
3. Once you have this basic structure working, create a set of test queries that exercise each relation you've written down in your table of how tables are related. Take the time to cover as much of the connections as possible while you can shape the database.
4. After you get the relations solid, you go back through and add all the other attributes as columns and then rerun your sample queries to make sure they still work. If you've done everything right your queries will keep working after you add the attributes. If they break then your attributes need to be redesigned or corrected.
5. Finally, write some sample queries and data to test out that it works correctly.

Implementing With Object-Relational Mappers:
1. Create all the classes for your model and make sure it generates the right tables and only the ids.
2. Add all the relationships to the classes you've made.
3. Write unit tests for the basic relationships and make sure they make sense.
4. Add the attributes and make sure the unit tests keep working as expected. Remember your attributes shouldn't break any of your relations.
5. Finally, write higher level unit tests that validate the attributes and queries for them.