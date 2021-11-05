# How to synchronize multiple Form panels in the same window

In case you have a window containing many Form panels, all having the same data model and business component, used to show different parts of data in different panels, it is likely to have performance problems, due to multiple HTTP requests to the server, all requesting the same data, since they share the same data model and business component. Moreover, when editing data, after saving changes to one of these Forms, all others must be reloaded, otherwise hey would have  an inconsistent (old) state. Again, this dues to a performance problem.

In general, it is not a smart idea to show the same record in multiple panel, because of the performance problems and inconsistency issues hey can easily lead to.

In order to reduce the performance problem, it is possible to "link" all these Form panels together, so that one only (the "main panel") is allowed to load/save data and all the other (linked) panels are simply synchronized with the main panel.

Requirements:

* all panels must be Form panels
* all Form panels must be included in he same window
* Form panels must be contained in a "complex" container (not a border layout container), such as an Horizontal container, a Table container, etc.
* Form panels must have the same business component

Constraints:

* the "main panel" must be the one shown first (typically the left most top panel)
* it does not have any sense to edit multiple forms at the same time  (as said before, it can easily lead to inconsistencies due to presentation logic)
* the auto load data is disabled for linked forms; if the main form is set to auto load data, then the others will be filled in with data loaded from the first one

Behavior:

* when loading data in any of the forms, data is loaded once and reported to all forms
* when saving data in any of the forms, data is saved once and the result (refreshed data) coming form the server reported on all forms

In order to activate the linking between a form panel and the "main form", you have to fill in the property named "main form" available on the left of the Panels tree (in the window definition); this property must be set with the panel id of the main form; the main form must no be filled.



