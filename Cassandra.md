## Cassandra

Cassandra is a distibuted NoSQL Database folowing the key:value format that is usual with NoSQL.

Cqlsh - Is Cassandra's query language.

## CLI Commands

- HELP - Displays help topics for all cqlsh commands.
- CAPTURE - Captures the output of a command and adds it to a file.
- CONSISTENCY - Shows the current consistency level, or sets a new consistency level.
- COPY - Copies data to and from Cassandra.
- DESCRIBE - Describes the current cluster of Cassandra and its objects.
- EXPAND - Expands the output of a query vertically.
- EXIT - Using this command, you can terminate cqlsh.
- PAGING - Enables or disables query paging.
- SHOW - Displays the details of current cqlsh session such as Cassandra version, host, or data type assumptions.
- SOURCE - Executes a file that contains CQL statements.
- TRACING - Enables or disables request tracing.


## Data Definition Commands

- CREATE KEYSPACE - Creates a KeySpace in Cassandra.
- USE - Connects to a created KeySpace.
- ALTER KEYSPACE - Changes the properties of a KeySpace.
- DROP KEYSPACE - Removes a KeySpace
- CREATE TABLE - Creates a table in a KeySpace.
- ALTER TABLE - Modifies the column properties of a table.
- DROP TABLE - Removes a table.
- TRUNCATE - Removes all the data from a table.
- CREATE INDEX - Defines a new index on a single column of a table.
- DROP INDEX - Deletes a named index.

## Data Manipulation

- INSERT - Adds columns for a row in a table.
- UPDATE - Updates a column of a row.
- DELETE - Deletes data from a table.
- BATCH - Executes multiple DML statements at once.

## CQL clauses

- SELECT - This clause reads data from a table
- WHERE - The where clause is used along with select to read a specific data.
- ORDERBY - The orderby clause is used along with select to read a specific data in a specific order.

## How to access Cassandra DB's

To connect to a cqlsh shell in a WH environment:

`cqlsh -u basketball -p AKDbfh18 pp1xwtc03dbn001.pp1.williamhill.plc`

This launches a Cassnadra shell that is connected to the Cassandra instance running on pp1xwtc03dbn001.pp1.williamhill.plc with username basketball.

From here we can runn teh CLI commands given above.

## Command Reference:

###### CAPTURE

To capture the output of subseqent commnads in an Outputfile.

`cqlsh> CAPTURE '/home/hadoop/CassandraProgs/Outputfile'` 

This can be turned back off with:

`capture off;`

###### COPY

To copy the contents of a table to a file:

`COPY emp (emp_id, emp_city, emp_name, emp_phone,emp_sal) TO ‘myfile’;`

###### DESCRIBE

This command can be used to gain details of various Casssandra Entities and should be used as:
describe <entity> 

Where <entity> includes: 

- cluster, 
- keyspace, 
- tables, 
- table,
- types
- type - For user defined types.

###### EXPAND

Like a -v for Cassandra - This gives a more verbose output.To use this we need to turn on expand with `expand on;`.

###### EXIT

Terminates the cqlsh shell.

###### SHOW

This gives details of the current cqlsh session:

Example calls would be : `show host;` and `show version;` 


###### SOURCE

This acts as source would in BASH:

`source '/home/hadoop/CassandraProgs/inputfile';`

###### CREATE KEYSPACE

A keyspace is a namespace that defines data replication on nodes. A cluster contains one KeySpace per node.

The below command creates a keyspace named `newSpace` defining the Replication Strategy as `SimpleStrategy` and a repication factor of `3`

	cqlsh.> CREATE KEYSPACE newspace
	WITH replication = {'class':'SimpleStrategy', 'replication_factor' : 3};


Further reading is required to fully understand the Replication Strategy and the Replication Factor attributes.

Once a keyspace is created we can use `DESCRIBE keyspace` as a bove to verify that it has been created.

We can alo add DURABLE_WRITES as a propety of the query:

	CREATE KEYSPACE test
	... WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 3 }
	... AND DURABLE_WRITES = false;


To Check that DURABLE_WRITES is set correctly we can use:

`SELECT * FROM system.schema_keyspaces;`

This query returns all keyyspaces and their properties.

###### Using A keyspace

A keyspace can be used with the USE command.

`USE mykeyspace;` - would allow us to query `mykeyspace`.

For details on the JAVA API - see [Tutorial] (https://www.tutorialspoint.com/cassandra/cassandra_create_keyspace.htm)



## CRUD Operations

#### Create

#### Read

###### SELECT

Using the SELECT clause, you can read a whole table, a single column, or a particular cell.

Imagine we have:

| emp_id | emp_city   | emp_name | emp_phone    | emp_sal    |
|--------|------------|----------|--------------|------------|
| 1      | Leeds      | Jamie    | 01254 784567 | £1,000,000 |
| 2      | Manchester | Sally    | 09374 675848 | £120,000   |
| 3      | Blackpool  | Jordan   | 12346 746579 | £45,000    |
| 4      | Glasgow    | George   | 09867 123456 | £2,000,000 |

[Table Generator](https://www.tablesgenerator.com/markdown_tables#)

To view the whole table we do:

`select * from emp;`

To view a sub-set of columns from a table we do:

`select emp_name, emp_sal from emp;`

###### WHERE

Similar to SQL the WHERE clause this allows us to apply a constraint to our resuts.

So we might do:

`SELECT * FROM emp WHERE emp_sal=1000000;`

It is advised that before we do the above we create a secondary index on `emp_sal` ie: `CREATE INDEX ON emp(emp_sal);`


The Java API for reading data is explained at: [Tutorial](https://www.tutorialspoint.com/cassandra/cassandra_read_data.htm)


#### Update

#### Delete



### The Partition State Cache





