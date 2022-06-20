# How to synchronize data from Datastore to CloudSQL

It is not recommended to use this choice, since a relational database is not the best solution to execute complex queries (joins, nested selects, exists, IN, etc.) on a very large table content. In the worst case, the database service could crash.

In case of a relatively low number of data to read, it is possible to synchronize data stored in an entity of Google Datastore and duplicate it on a corresponding table on CloudSQL. Such destination table must have exactly the same structure (field names and types) of the original one (entity on Datastore).

In order to do it:

1. The application parameter GOOGLE -> "**Synchronize Datastore data with**" bust be filled with the Database destination type. That means that it is possible to synchronize data from Datastore entities only to this media.
2. Use the "**Duplicate Datastore object to CloudSQL**" command, available on the Data model list; this feature creates a new data model (having "database" type) starting from the selected Datastore type model; the new object has the same name and field names/types. If you need to align structural changes applied to the Datastore original object to the new one, you can add additional fields to the destination object.
3. Use the "**Sync data to db**" checkbox in the data model definition window for the Datastore object: once checked it, every insert/update/delete operation applied to the Datastore entity will be duplicated in the target relational table; there is not a latency on the Datastore side: the duplication operation is always performed on a separated thread.
4. Optionally, you can also refine the sync rule and limit data to duplicate, using a filtering condition (e.g. companyId==00000)

An alternative to the steps 3-4 is using the "**Export from Datastore to CloudSQL**" feature, where you can export the whole content (either as insert or update operations) and schedule this job to be execute on a regular basis, using a scheduled process.
