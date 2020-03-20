# Filtering a Lookup

It is possible to filter a lookup whether it is in a grid or in a filter. To do so you have to intercept the action of the opening of the lookup, then you have to filter its grid.

Here an example:

* Create a new action js. This will be the action that will filter the lookup.                      

```javascript
lkuStore.baseParams.filterNames = 'mobileIdTrip,deviceId,';   /*field to filter, always put a , at the end*/
lkuStore.baseParams.filterOps = '=,=,';  /* filter operator */
lkuStore.baseParams.filterValues = args.mobileId + ',' + args.deviceId + ','; /* filter value */
/* optional parameter = lkuStore.baseParams.caseSensitives = true/false */
```

1. Go to the filter panel and then on Event Components and add a new event:

* Field of the database: field where the lookup is hooked.
* Event: Before grid opening.
* Action: our action \(previously created\)

1. If you want to validate filtering what the user write in the lookup, you have to hook the same action to the action Before validation
2. Now your lookup will be filtered as described in the JS action in the first point.

