# Dynamically copy files and rename
This document shows the ADF steps that can implement following requirements
1. a source Azure blob storage account `adfsource05` and container `sourcecontainer`
2. a target Azure blob storage acount `adfstarget06` and container `targetcontainer`
3. a MySQL table defines two string columns `sourcefile` `targetname`.
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/bbf25e73-a21d-4eb8-85b9-1f6e560ffbed)
4. Column `sourcefile` indicates which files (with path) need to be copied from source blob storage container (`sourcecontainer` in above step 1) to target blob storage container (`targetcontainer` in above step 2), column `targetname` indicates the renamed file name once copied to target container. 

Following shows how to implement it with Azure Data Factory
