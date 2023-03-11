# Developing a Batch Processing Solution with Azure Services

## Introduction
In this hands-on lab scenario you are a data engineer for Awesome Company. You have been tasked with creating a batch processing solution in Azure that will analyze crowdsourced weather information. You have previously provisioned an Azure Data Lake Storage Gen2 account and containers for holding the uploaded files. Now you need to continue building upon that in order to analyze the data on a nightly basis.

Performing the actions of this hands-on lab will help you become familiar with building a complete batch processing solution using Azure services.

## Prepare the Environment for Loading

1. Review the lab-provided resources from the Azure portal overview.
2. Click the storage account that starts with acweather.
3. From the left-side menu, under Data storage, click Containers.
4. Click uk1.
5. Click Upload.
6. Open a new browser tab, and navigate to the GitHub repo for the lab.
7. Click the Code button, and select Download ZIP.
8. Unzip the folder on your local machine.
9. Go back to the Azure portal, and in the Upload blob pop-up, click the folder icon.
10. Find the folder for the code you just downloaded from GitHub, and select both .json files in the UK subfolder (found under course-dp203-data-engineering-on-microsoft-azure > Hands-on Labs > Weather Data) to add to the uk1 container.
11. Click Open, then Upload.
12. Using the navigation links at the top of the portal, navigate back to the acweather folder, and click usa.
13. Click Upload.
14. Upload both .json files in the USA weather data folder to the usa container.
15. Click Open, then Upload.
16. Return to the Azure portal overview, and from the list of resources, open the Synapse workspace (the resource prefixed with acanalyze).
17. In the left-hand menu, under Security, click Networking.
18. Click + Add client IP.
19. Click the checkbox next to Allow Azure services and resources to access this workspace.
20. Click Save.
21. In the left-hand menu, under Analytics pools, click SQL pools.
22. Click acsqlpool.
23. Next to Connection strings, click Show database connection strings.
24. Under ADO.NET (SQL authentication), copy the section of the string that begins with acanalyze, up to azuresynapse.net.
25. Using a separate client, such as Azure Data Studio, connect to the database.
26. If you are using Azure Data Studio, from the dashboard, click New, and click New connection.
27. For the Server name, paste in the recently copied string, and use the following credentials:
* User name: delmar
* Password: AwesomePassword!
28. Click Connect.
29. Click New Query.
30. Change the database connection to acsqlpool.
31. Copy and paste the following script and click Run:
```
CREATE TABLE [dbo].[Weather](
    [country] [varchar](25),
    [state] [varchar](25),
    [city] [varchar](25),
    [high] [int],
    [low] [int],
    [conditions] [varchar](25),
    [source] [varchar](50),
    [processed] [datetime]
 )
```
32. Return to the Azure portal overview.

## Copy the Weather Data Using Azure Data Factory

1. Using the navigation at the top of the Azure portal, click the link next to Home to go back to the resource group.
2. Select the link for the Azure Data Factory, which should begin with acadf.
3. Click Open Azure Data Factory Studio.
4. Click New, then Pipeline.
5. Name the pipeline "Weather Load".
6. Under Move & transform, drag and drop a Copy data task to the pipeline.
7. Rename the task to "Copy Files".
8. Switch to the Source tab, and click + New.
9. Select Azure Data Lake Storage Gen2, and click Continue.
10. Select JSON, and click Continue.
11. Name the dataset "weather_files", and under Linked service, click + New.
12. Name the linked service "weather_data_lake".
13. For the Azure subscription, select the lab-provided subscription.
14. For the Storage account name, select the acweather storage account.
15. Click Test connection.
16. Click Create.
17. Click Ok.
18. Next to File path type, click Wildcard file path.
19. For the Wildcard paths {source container}, in the first entry field, enter *. In the second entry field, enter weather_*.json.
20. Next to Additional columns, click + New.
21. Add a column with the name source and the value &&FILEPATH.
22. Add a second column with the name processed. For the value, click Add dynamic content.
23. In the large entry field, enter **@pipeline().TriggerTime**, and click OK.
24. Switch to the Sink tab, and click + New.
25. Select Azure Synapse Analytics, and click Continue.
26. Name the dataset "weather_table", and under Linked service, click + New.
27. Name the linked service "weather_analytics".
28. For the Azure subscription, select the lab-provided subscription.
29. For the Server name, select the Synapse workspace.
30. For the Database name, select the acsqlpool.
31. For the User name and Password use the following credentials:
* User name: delmar
* Password: AwesomePassword!
32. Click Test connection.
33. Click Create.
34. For the Table name, select dbo.Weather, and click Ok.
35. Still on the Sink tab, ensure the Copy method is set to PolyBase.
36. Switch to the Mapping tab, and click Import schemas.
37. Click Ok.
38. Switch to the Settings tab, and click Enable staging.
39. Next to Staging account linked service, click + New.
40. Name the linked service "admin_data_lake".
41. For the Azure subscription, select the lab-provided subscription.
42. For the Storage account name, select the acadmin storage account.
43. Click Test connection.
44. Click Create.
45. Next to Storage Path, click Browse.
46. Select stage, and click Ok.

## Delete the Weather Files

1. In the Activities list, click General, and drag and drop a Delete activity to the pipeline.
2. On the bottom, under General, name the activity "Delete USA Files".
3. Switch to the Source tab, and click + New.
4. Select Azure Data Lake Storage Gen2, and click Continue.
5. Name the dataset "usa_path", and for the Linked service, select weather_data_lake.
6. Under File path, click the folder icon, and select usa.
7. Click Ok, and then Ok again.
8. Change the File path type to Wildcard file path, and enter weather_*.json for the Wildcard file name.
9. Switch to the Logging settings tab, and disable logging.
10. Drag and drop a second Delete activity to the pipeline.
11. Name the activity "Delete UK Files".
12. Switch to the Source tab, and click + New.
13. Select Azure Data Lake Storage Gen2, and click Continue.
14. Name the dataset "uk_path", and for the Linked service, select weather_data_lake.
15. Under File path, click the folder icon, and select uk1.
16. Click Ok, and then Ok again.
17. Change the File path type to Wildcard file path, and enter weather_*.json for the Wildcard file name.
18. Switch to the Logging settings tab, and disable logging.
19. Connect the Copy Files task to each Delete activity (dragging the arrows from each box), and click Debug.
20. Once it has completed, and all tasks display a Succeeded status, go back to Azure Data Studio, and run the following query:
21. select * from [dbo].[Weather]
To verify that the files have been deleted, return to the Azure portal overview resource group, and open the acweather storage account.
22. Click Containers and then select either of the 2 containers.
23. Return to the Azure Data Factory tab, and click Publish all.
24. Click Publish to make this your active pipeline.