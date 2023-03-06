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