# Modifying file entities

assume `files` is a pandas DataFrame that contains a set of file entities and their 
synapse IDs (e.g., column header `id`) that you wish to perform some operations on.

```python
# load necessary packages
import synapseclient
import pandas as pd
import synapseutils

# read in your file manifest as a pandas DataFrame
files = pd....

# loop through file entity synID and modify entity metadata
for synID in files.id:
  # get entity metadata but don't download the file contents
  entity = syn.get(synID, downloadFile = False)
  
  # move entity location in synapse
  entity.parentId = "folder synID"
  
  # rename file
  entity.name = "new name"
  
  # push changes to synapse, disable automatic entity version bump
  entity = syn.store(entity, forceVersion = False)
  # new metadata saved to entity object
  
  # if entity if renamed then perform this step after syn.store
  # ensure file downloads with new name by also updating downloadAs name
  synapseutils.changeFileMetaData(syn=syn, entity=entity.id, 
  downloadAs=entity.name, forceVersion=False)
```

the above examples explicitly do not force a version bump on the entity. If a 
version bump is desired it is best practice to also include a version comment to 
inform others of what changes have been applied, for example:

```python
# add version comment
entity.properties["versionComment"] = "this is my version comment"
  
# save changes to synapse, allow default version bump
entity = syn.store(entity)
```
