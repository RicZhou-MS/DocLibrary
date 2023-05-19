# Dynamically copy files and rename
This document shows the ADF steps that can implement following requirements
1. a source Azure blob storage account `adfsource05` and container `sourcecontainer`
2. a target Azure blob storage acount `adfstarget06` and container `targetcontainer`
3. a MySQL table `filetable` defines two string columns `sourcefile` `targetname`.
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/bbf25e73-a21d-4eb8-85b9-1f6e560ffbed)
4. Column `sourcefile` indicates which files (with path) need to be copied from source blob storage container (`adfsource05/sourcecontainer` in above step 1) to target blob storage container (`adfstarget06/targetcontainer` in above step 2), column `targetname` indicates the renamed file name once copied to target container. 

Following shows how to implement it by Azure Data Factory.
1. Go to ADF and create a pipleline with name `DynamicCopyAndRename`, and drag the **Lookup** activity onto the canvas, and give the name as `LookupMySQLTable`.
2. click the **settings** tab of the Lookup acitivity and add a new Azure MySQL dataset with corresponding MySQL link service like following
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/c8fa9365-f231-408e-8850-ae8be3d7a862)
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/023020f3-884b-41d6-9352-7b0739ea5a22)
3. Set the User query as **Query** and give the query statement as `select * from filetable`, remember clear the check of **First row only**, and then click **Preview data** to check the if the query result is expected
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/0a89aa85-0993-4d9f-8d81-389b591e1fa5)
4. Add a **ForEach** activity and connect behind the lookup activity, give the name as `ForEachQueryRow`. at the **settings** tab, check **Sequential** and click **Add dynamic content** at **Items** field, add dynamic content as following capture.
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/b2af4e61-b628-49bf-a90e-f9da9126bf46)
5. Click add **Activities** icon inside ForEach activity, and select **Copy Data** activity to add into ForEach. 
6. Click this copy data activity and go to **Sources** tab, add **Azure Blob Storage** dataset with **Binary** file format (NOTE: you can choose other file format which aligns with your scenario), and create associated blob linked service which pointing to the source blob storage account `adfsource05` and save.
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/eee80fad-58ab-4165-936f-ecdcb92c3b5f)
7. Click the **Open** to reopen the dataset setting page
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/6c92f628-1204-436e-913c-8fc2a23a9824)
8. Click **Parameters** tab to add parameters
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/ff27c04f-1e89-495f-ba55-b92809c847b5)
9. Add one string type parameter with name `ParamSoureFileWithPath` and click **Connection** tab.
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/28504333-b521-4f18-aa20-1b67960d59f2)
10. At Connection tab, put your source container name `sourcecontainer` at container field, and click **Add dynamic content** at **Directory** field, give dynamic string as `@substring(dataset().ParamSoureFileWithPath,0,lastindexof(dataset().ParamSoureFileWithPath,'/'))`
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/74aa34b7-3dfc-40b0-8576-9fd3399e61e0)
11. Similarly give following dynamic content at **File name** field
`@substring(dataset().ParamSoureFileWithPath,add(lastindexof(dataset().ParamSoureFileWithPath,'/'),1),sub(length(dataset().ParamSoureFileWithPath),add(lastindexof(dataset().ParamSoureFileWithPath,'/'),1)))`
![image](https://github.com/RicZhou-MS/DocLibrary/assets/75886466/0f18383c-6ef0-4974-917a-9099b3aa9d28)



