# calling a SQL action from a client side js action

After the creation of the SQL action, paying attention to the datasource and to the parameters name needed for the call.

```javascript
var vo_par = new Object()

/* I'll use this variables in SQL with :COD_SOCIETA_FILTER, :COD_DIVISIONE_FILTER, :COD_ENTE, :ANNO */

vo_par['codSocietaFilter'] = vo['codSocieta'];
vo_par['codDivisioneFilter'] = ...;
vo_par['codEnte'] = ...;
vo_par['anno'] = ...;
var json = Ext.encode(vo_par);

/* EXECUTEQUERY is used just because the action contains a SELECT */
var url = contextPath +'/executesql/executequery?appId=' +applicationId+ '&amp;applicationId=' + applicationId + '&amp;actionId=2749&amp;datastoreId=9';

/* Execute the call */ 
var response = new SyncRequest().send(url, 'POST', json, 'application/json');

/* Parse the response */
var parsed = JSON.parse(jsonrowsconfNegozio);

if (response != null &amp;&amp; response != '') {
    risp = Ext.decode(response);
    if (risp['success'] == false) {
        showMessageDialog("dialog.title.error", risp['message'], function() {}, false);
        return false;
    }
}

//the response will be a valueobject
if(risp.valueObjectList[0].GESTIONE_A_TEAM == 'F') {
    ...        
}
```

Where actionId=XXX is the ID of the SQL Action that must be executed and datastoreId=YYY is the ID of the additional datastore \(omit to use the main Database\)

The SQL action will be composed like this.

```javascript
SELECT GESTIONE_A_TEAM FROM INC007_MATRICE_ENTE
WHERE COMPANY_ID=:COMPANY_ID
AND SITE_ID=:SITE_ID
AND COD_SOCIETA=:COD_SOCIETA_FILTER
AND COD_DIVISIONE=:COD_DIVISIONE_FILTER
AND COD_ENTE=:COD_ENTE
AND ANNO=:ANNO
AND MESE=:MESE
```

NOTE:

If the SQL action is a SELECT the following method must be used: /executesql/executequery?appId=… while for all the other SQL operation \(UPDATE, INSERT, DELETE\) just use /executesql?appId=…

