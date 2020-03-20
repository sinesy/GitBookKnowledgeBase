# How to manage folder panels

A folder container contains a set of folders, where a folder can be any kind of panel. The user can show any of these folders by clicking on the folder title. Additional javascript methods are available in order to programmatically show/disable any folder, through the following methods:

```javascript
setActiveTab(‘folderContainerIdentifier’,panelIndex);
```

where folderContainerIdentifier represents the folder container \(e.g. customPanel123\) and panelIndex represents the position of the folder to show \(starting from 0\). Moreover, a specific folder can be showed/hidden through the following method:

```javascript
setVisibleTab(‘folderContainerIdentifier’,panelIndex,true|false);
```

Finally, a specific folder can be enabled/disabled through the following method:

```javascript
setEnableTab(‘folderContainerIdentifier’,panelIndex,true|false);
```

