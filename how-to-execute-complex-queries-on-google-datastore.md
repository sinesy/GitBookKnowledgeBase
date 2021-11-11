# How to execute complex queries on Google Datastore

Google Datastore is a NoSQL database. Consequently, it is not possible to execute JOIN conditions to retrieve data stored in multiple tables with a single query. Moreover, the SQL language is not supported, therefore no IN, EXISTS, nested SELECTs or complex comparison operators are supported.

A good design practice with NOSQL database is always to denormalize a table, i.e. save data coming from other tables in a unique table and in this way redundating data. In this way, no JOINS are needed, since all data is already available on a single denormalized table.

Unfortunately, Datastore supports only very simple queries in terms of comparison operators:

* \= operator on a single field
* AND operator (OR and NOT are not allowed)
* <=, <, >, >= operators are supported, but only for a single field; it is not possible to apply multiple times this operator on multiple fields
* IN is not supported, neither nested SELECTs, not EXISTS

It comes without saying that a NoSQL database is not suitable for very complex queries.

The amount of data stored in Datastore should then be moved (duplicated) to other repositories, where more complex queries are supported. However, it is important not to duplicate the whole content of a NoSQL table, otherwise the same issues a NoSQL should overpass (big data) will be raised again.

A few alternatives are available:

* **CloudSQL database**, i.e. a relational database; this is not a good idea, since a relational database is not able to manage big data (neither TB of data nor millions of records per table, combined with JOINs or other complex operators). **Trying to use a relational database to execute very complex queries on a large amount of data can only lead to (i) very poor performance on executing queries (in the best case) or (ii) it is likely to crash the database sooner or later**
* **Google BigQuery **- this is a data warehouse, i.e. a "read only" database optimized to store a very large amount of data and  enquiry/analyze it; it supports the SQL language
* **Google Spanner **- this is a powerful (and very costly) database, able to manage virtually an infinite amount of data (as for a NoSQL database) and supporting the SQL language

Platform supports all of them and provides a few features to duplicate data from Datastore to any of them.

## Executing SQL query on BigQuery

In case of a large number of data to read and analyze (complex queries), it is possible to synchronize data initially stored in an entity of Google Datastore and duplicate it on a corresponding table on BigQuery. Such a destination table must have exactly the same structure (field names and types) of the original one (entity on Datastore).

In order to do it:

1. First, a BigQuery instance must be configured and a dataset defined. This will be used to host all BigQuery tables to create and enquiry.
2. The application parameter GOOGLE -> "**Synchronize Datastore data with**" must be filled in with the BigQuery destination type. That means it is possible to synchronize data from Datastore entities only to this media.
3. Use the "**Duplicate Datastore object to BigQuery**" command, available on the Data model list; this feature creates a new data model (having "big query" type) starting from the selected Datastore type model; the new object has the same name and field names/types. If you need to align structural changes applied to the Datastore original object to the new one, you can add additional fields to the destination object.
4. Use the "**Sync data to db**" checkbox in the data model definition window for the Datastore object: once checked it, every insert/update/delete operation applied to the Datastore entity will be duplicated in the target relational table; there is not a latency on the Datastore side: the duplication operation is always performed on a separated thread.
5. Optionally, you can also refine the sync rule and limit data to duplicate, using a filtering condition (e.g. companyId==00000)

An alternative to the steps 4-5 is using the "**Export from Datastore to BigQuery**" feature, where you can export the whole content (either as insert or update operations) and schedule this job to be execute on a regular basis, using a scheduled process.

**Important note: **it would be better to opt for the "Sync data to db" approach, rather than the export, since it synchronize data in real time (i.e. data will be available in terms of seconds on BigQuery).

However, in case of a large amount of data created on Datastore (e.g. tens of thousands records per day), the latency due to BigQuery writing (2-3 seconds per record) could represent a limitation. In such a scenario, it can be helpful to add a scheduled process using the "Export from Datastore to BigQuery" feature as well, to duplicate a lot of data in a faster way: inserting multiple records as an export is faster than inserting single records through a real time synchronization.

### **Aligning data structure between Datastore (master repository) and BigQuery**

In the BigQuery data model definition window, there is an "Align model from Datastore" button, which can be used to copy the Datastore entity definition (fields) to BigQuery and all related objects connected to it (business components, panels). Use it to quickly align the two models.

It also ALTER BigQuery table and add new fields coming from Datastore. **This is helpful also when you export->import metadata from one environment to the other.**

### **Executing queries with aggregated functions**

In case you need to execute a SQL query not only involving fields of the synchronized table but also aggregated functions like SUM, COUNT, etc. combined with GROUP BY keyword, you can di it using the already existing Platform features:

* add a virtual field (e.g MY\_SUM) to the BigQuery type model, one for each aggregated function; these fields with not be removed in case you "Align model with Datastore" (which only adds fields and never removes them)
* change the SQL query in your server-side javascript business component and include the corresponding aggregated function and alias (SELECT SUM(\*) AS MY\_SUM from ...)
* enable columns in your grid accordingly

### **Involved costs**

BigQuery has a pricing based on (i) occupied space and (ii) number of records analyzed in a query per month. Whereas the first fee is relatively low for a few Gbytes (less than 2 euros per 100Gb), the second fee is proportional to

* the number of queries per month
* the amount of records analyzed for each query (analyzed and retrieved is different!)
* the record size

Consequently, the larger is the record and the number of records to analyze, the higher are the involved costs: it is important to pay attention to these costs.



## Executing SQL query on Google Spanner

In case of a large number of data to read and analyze (complex queries), it is possible to synchronize data initially stored in an entity of Google Datastore and duplicate it on a corresponding table on Google Spanner. Such a destination table must have exactly the same structure (field names and types) of the original one (entity on Datastore).

In order to do it:

1. First, a Spanner instance must be configured . This will be used to host all Spanner tables to create and enquiry.
2. The application parameter GOOGLE -> "**Synchronize Datastore data with**" must be filled in with the Spanner destination type. That means it is possible to synchronize data from Datastore entities only to this media.
3. Use the "**Duplicate Datastore object to BigQuery**" command, available on the Data model list; this feature creates a new data model (having "spanner" type) starting from the selected Datastore type model; the new object has the same name and field names/types. If you need to align structural changes applied to the Datastore original object to the new one, you can add additional fields to the destination object.
4. Use the "**Sync data to db**" checkbox in the data model definition window for the Datastore object: once checked it, every insert/update/delete operation applied to the Datastore entity will be duplicated in the target relational table; there is not a latency on the Datastore side: the duplication operation is always performed on a separated thread.
5. Optionally, you can also refine the sync rule and limit data to duplicate, using a filtering condition (e.g. companyId==00000)

An alternative to the steps 4-5 is using the "**Export from Datastore to Spanner**" feature, where you can export the whole content (either as insert or update operations) and schedule this job to be execute on a regular basis, using a scheduled process.

**Aligning data structure between Datastore (master repository) and Spanner**

In the Spanner data model definition window, there is a "Align model from Datastore" button, which can be used to copy the Datastore entity definition (fields) to Spanner and all related objects connected to it (business components, panels). Use it to quickly align the two models.

It also ALTER Spanner table and add new fields coming from Datastore. **This is helpful also when you export->import metadata from one environment to the other.**

**Important note: **it would be better to opt for the "Sync data to db" approach, rather than the export, since it synchronize data in real time.

## Executing SQL query on CloudSQL

It is not recommended to use this choice, since a relational database is not the best solution to execute complex queries (joins, nested selects, exists, IN, etc.) on a very large table content. In the worst case, the database service could crash.

In case of a relatively low number of data to read, it is possible to synchronize data stored in an entity of Google Datastore and duplicate it on a corresponding table on CloudSQL. Such destination table must have exactly the same structure (field names and types) of the original one (entity on Datastore).

In order to do it:

1. The application parameter GOOGLE -> "**Synchronize Datastore data with**" bust be filled with the Database destination type. That means that it is possible to synchronize data from Datastore entities only to this media.
2. Use the "**Duplicate Datastore object to CloudSQL**" command, available on the Data model list; this feature creates a new data model (having "database" type) starting from the selected Datastore type model; the new object has the same name and field names/types. If you need to align structural changes applied to the Datastore original object to the new one, you can add additional fields to the destination object.
3. Use the "**Sync data to db**" checkbox in the data model definition window for the Datastore object: once checked it, every insert/update/delete operation applied to the Datastore entity will be duplicated in the target relational table; there is not a latency on the Datastore side: the duplication operation is always performed on a separated thread.
4. Optionally, you can also refine the sync rule and limit data to duplicate, using a filtering condition (e.g. companyId==00000)

An alternative to the steps 3-4 is using the "**Export from Datastore to CloudSQL**" feature, where you can export the whole content (either as insert or update operations) and schedule this job to be execute on a regular basis, using a scheduled process.









