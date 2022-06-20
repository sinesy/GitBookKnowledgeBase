# How to synchronize data from Datastore to Google Spanner

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

**Important note:** it would be better to opt for the "Sync data to db" approach, rather than the export, since it synchronize data in real time.
