# How to export data from BigQuery in streaming

In case of a grid panel reading paginated data, it is possible to export data in streaming, if the export format is csv.

In order to do it, all these conditions must be true:

* the grid is connected to a business component for list with js
* within the b.c. either the utils.**getPartialResultOnBigQueryWithSettings** method or the **getPartialResultOnBigQuery** method must be used
* no additional business logic should be added AFTER the getPartialResultOnBigQueryxxx method
* on the grid panel, an "**before load**" event must be added, bound to a client-side js action containing the following filtering condition (enabling exporting in streaming)

> gridxxx.store.baseParams.**streamExport="Y";**

* the export format must be csv comma separated, so it would be better to limit the number of export formats to one only
* the columns declared as exportable must ALL be included in the SELECT, otherwise a field not found error would be returned by BigQuery when attempting to export data.





