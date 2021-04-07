# How to execute complex queries on Google Datastore

Google Datastore is a NoSQL database. Consequently, it is not possible to execute JOIN conditions to retrieve data stored in multiple tables with a single query. Moreover, the SQL language is not supported, therefore no IN, EXISTS, nested SELECTs or complex comparison operators are supported.

A good design practice is always to denormalize a table, i.e. save in a table data normally available in other tables. In this way, no JOINS are needed, since all data is already available on a single denormalized table.

Unfortunately, Datastore supports only very simple queries in terms of comparison operators:

* = operator on a single field
* AND operator \(OR and NOT are not allowed\)
* &lt;=, &lt;, &gt;, &gt;= operators are supported, but only for a single field; it is not possible to apply multiple times this operator on multiple fields
* IN is not supported, neither nested SELECTs, not EXISTS

It comes without saying that a NoSQL database is not suitable for very complex queries.

The amount of data stored in Datastore should then be moved \(duplicated\) to other repositories, where more complex queries are supported. However, it is important not to duplicate the whole content of a NoSQL table, otherwise the same issues a NoSQL should overpass \(big data\) will be rised again.

A few alternatives are available:

* **CloudSQL database**, i.e. a relational database; this is not a good idea, since a relational database is not able to manage big data \(neither TB of data nor millions of records per table, combined with JOINs or other complex operators\). **Trying to use a relational database to execute very complex queries on a large amount of data can only lead to \(i\) very poor performance query executions \(in th best case\) or \(ii\) it is likely to crash the database**
* **Google BigQuery** - this is a data warehouse, i.e. a "read only" database optimized to store very large amount of data to enquiry it; it supports the SQL language
* **Google Spanner** - this is a powerful \(and very costly\) database, able to manage virtually an infinite amount of data \(as for a NoSQL database\) and supporting the SQL language

Platform supports all of them and provides a few features to duplicate data from Datastore to any of them.

## Executing SQL query on CloudSQL

It is not recommended to use this choice, since a relational database is not the best solution to execute complex queries \(joins, nested selects, exists, IN, etc.\) on a very large table content. In the worst case, the database service could crash.

In case of a relatively low number of data to read, it is possible to synchronize data stored in an entity of Google Datastore and duplicate it on a corresponding table on CloudSQL. Such destination table must have exactly the same structure \(field names and types\) of the original one \(entity on Datastore\).

In order to do it:

1. Use the "**Duplicate Datastore object to CloudSQL**" command, available on the Data model list; this feature creates a new data model \(having "database" type\) starting from the selected Datastore type model; the new object has the same name and field names/types. If you need to align structural changes applied to the Datastore original object to the new one, you can add additional fields to the destination object
2. Use the "**Sync data to db**" checkbox in the data model definition window for the Datastore object: once checked it, every insert/update/delete operation applied to the Datastore entity will be duplicated in the target relational table; there is not a latency on the Datastore side: the duplication operation is always performed on a separated thread

An alternative to the step 2 is using the "**Export from Datastore to CloudSQL**".









