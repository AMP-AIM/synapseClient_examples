# Get list of files and associated synapse IDs

There are several options. For the examples below, assume the files of 
interest exist in a folder with synapse ID `syn12345678`.

A. Use an existing file manifest generated from upload process

B. Use an existing file view or make a file view that includes the files of interest

For AMP AIM, the file view [All Files (Schematic View)](https://www.synapse.org/Synapse:syn64332440/tables/) 
is scoped to include all files in the AMP AIM Pre-Analytic Staging Workspace project (syn57431202). 
Note - when user access this file view, only files they have >= view permissions will 
render in the view.

```python
# load necessary packages
import synapseclient
import pandas as pd
import synapseutils

# log into synapse
syn = synapseclient.login()

# query All Files (Schematic View) for files in folder syn12345678
query = syn.tableQuery("select * FROM syn64332440 WHERE parentId = 'syn12345678'", downloadLocation = ".")

# load downloaded csv into pandas DataFrame
files = pd.read_csv(query.filepath)
```

if there are multiple folders of interest:

```python
query = syn.tableQuery("select * FROM syn64332440 WHERE parentId IN ('syn12345678', 'syn23456789')", downloadLocation = ".")
```

C. Programmatically build file "catalog"

```python
# load necessary packages
import synapseclient
import pandas as pd
import synapseutils

# log into synapse
syn = synapseclient.login()

with open("file_catalog.txt", "w") as f:
    a = f.write("synID\tfilename\n")
    # get all files in folder of interest
    files = syn.getChildren("syn12345678", includeTypes=['file'])
    files_list = list(files)
    for file in files_list:
      name = file['name']
      id = file['id']
      a = f.write('%s\t%s\n' % (id, name))
f.close()

# load files table into pandas DataFrame
files = pd.read_table("file_catalog.txt")
```

