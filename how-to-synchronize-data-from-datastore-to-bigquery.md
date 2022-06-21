# How to synchronize data from Datastore to BigQuery

In case of a large number of data to read and analyze (complex queries), it is possible to synchronize data initially stored in an entity of Google Datastore and duplicate it on a corresponding table on BigQuery. Such a destination table must have exactly the same structure (field names and types) of the original one (entity on Datastore).

In order to do it:

1. First, a BigQuery instance must be configured and a dataset defined. This will be used to host all BigQuery tables to create and enquiry.
2. The application parameter GOOGLE -> "**Synchronize Datastore data with**" must be filled in with the BigQuery destination type. That means it is possible to synchronize data from Datastore entities only to this media.
3. Use the "**Duplicate Datastore object to BigQuery**" command, available on the Data model list; this feature creates a new data model (having "big query" type) starting from the selected Datastore type model; the new object has the same name and field names/types. If you need to align structural changes applied to the Datastore original object to the new one, you can add additional fields to the destination object.
4. Use the "**Sync data to db**" checkbox in the data model definition window for the Datastore object: once checked it, every insert/update/delete operation applied to the Datastore entity will be duplicated in the target relational table; there is not a latency on the Datastore side: the duplication operation is always performed on a separated thread.
5. Optionally, you can also refine the sync rule and limit data to duplicate, using a filtering condition (e.g. companyId==00000)
6. Optionally, you can change the default settings for the synchronization process, i.e. **Standard** and set it to **Streaming**.

![](<.gitbook/assets/image (11).png>)

In **Standard** mode, when inserting/updating records on Datastore, every operation is sent to GAE and enqueued. Operations are then processed sequentially using the DML BigQuery API. Such approach has the limitation of 5 records every 10 seconds and it has the perk of managing insert/update/delete operations in "near real time".

See [Quotas and limits](https://cloud.google.com/bigquery/quotas#dataset\_limits) for more details.

An alternative approach is setting the **Streaming** mode, where insert/update Datastore operations are changed to insert operations in the BigQuery table and these records cannot be changed (neither updated nor deleted) in real time. Delete operations on Datastore are ignored on BigQuery (not synchronized).&#x20;

Operations in BigQuery are very fast and they do not require any more a GAE queue to process them.

Only after waiting 30 minutes these records can be removed.

See [Quotas and limits](https://cloud.google.com/bigquery/quotas#streaming\_inserts) for more details.



An alternative to the steps 4-5 is using the "**Export from Datastore to BigQuery**" feature, where you can export the whole content (either as insert or update operations) and schedule this job to be execute on a regular basis, using a scheduled process.

**Important note:** it would be better to opt for the "Sync data to db" approach, rather than the export, since it synchronize data in real time (i.e. data will be available in terms of seconds on BigQuery).

However, in case of a large amount of data created on Datastore (e.g. tens of thousands records per day), the latency due to BigQuery writing (2-3 seconds per record with the **Standard** mode) could represent a limitation. In such a scenario, it can be helpful to add a scheduled process using the "Export from Datastore to BigQuery" feature as well, to duplicate a lot of data in a faster way: inserting multiple records as an export is faster than inserting single records through a real time synchronization.

A faster alternative is the **Streaming** mode described above.



#### Example of table management with Streaming mode

Let's have a table having 3 fields: ITEM\_ID a text field used as a primary key, a QTY numeric field _and a_ datetime __ field LAST\_UPDATE used to store the most recent version of the ITEM\_ID.

An insert operation on Datastore would lead to a similar insert operation on the BigQuery table.

After that, the current qty value would be retrieved through a SELECT QTY from the table having the most recent LAST\_UPDATE, grouped by ITEM\_ID and it would return the only record stored at that time.

Next, an update operation on Datastore would be converted in an insert operation on the BigQuery table.

After that, the current qty value would be retrieved through a SELECT QTY from the table having the most recent LAST\_UPDATE, grouped by ITEM\_ID and it would return the last inserted record, i.e. the one generated when updating Datastore.

Finally, a delete operation on Datastore would be ignored on the BigQuery side. It is up to the developer to include an additional operation to insert in BigQuery a new record having a qty set to 0. This can be accomplished using the method:

```
utils.importBigQueryThroughStreaming(
  datasetName,
  tableName,
  [{ itemCode: "...", qty: 0, lastUpdate: utils.getcurrentDateAndTime() }]
);
```

After that, the current qty value would be retrieved through a SELECT QTY from the table having the most recent LAST\_UPDATE, grouped by ITEM\_ID and it would return the last inserted record, i.e. the one with a qty set to 0.

For a more detailed example, see [Performing large scale mutations in bigquery](https://cloud.google.com/blog/products/bigquery/performing-large-scale-mutations-in-bigquery)



### **Aligning data structure between Datastore (master repository) and BigQuery**

In the BigQuery data model definition window, there is an "Align model from Datastore" button, which can be used to copy the Datastore entity definition (fields) to BigQuery and all related objects connected to it (business components, panels). Use it to quickly align the two models.

It also ALTER BigQuery table and add new fields coming from Datastore. **This is helpful also when you export->import metadata from one environment to the other.**

****

### **Waiting for updated data commited on BigQuery**

When synchronizing data from Datastore to BigQuery, the data alignment is asynchrounous, since writing single records to BigQuery is a slow operation (it can takes several seconds).

In exceptional cases (which cannot become the daily rule), it is possible to force a synchronous writing of data on BigQuery, when saving data (in insert/update) on Datastore starting from a grid or form. In such a scenario, you have to pass forward to the standard Platform web services (setlist & setdetail) an additional parameter: "\&realTime=true" to append to the URL generated when saving data from a grid/form. In order to do it, link a client-side js action to the "before saving data on insert/update" events. The bound action should contain something like:

```javascript
additionalParams = "&realTime=true";
```

In this way, the sync operation on BigQuery would be synchronous and retrieved data on grid/form is fresh. Anyway, additional seconds are needed when saving data.

It would be way better to _avoid automatic grid/form reloading when saving data on grid/form_: this behavior is not needed, since grid/form are already updated behind the scenes with the fresh data coming from the server, through the JSON response.

****

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

