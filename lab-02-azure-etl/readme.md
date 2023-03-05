# Moving and Transforming Data with Azure Data Factory


## Introduction

In this hands-on lab scenario you are a data engineer for Awesome Company. They would like to move some sales data from one Azure SQL database to another for archiving and analysis. You have been tasked with creating an Azure Data Factory pipeline to accomplish this task and to perform data transformations along the way. Performing the actions of this hands-on lab will help you become familiar with using Azure Data Factory to perform data migrations and transformations.

## Create a Destination Table for the Archived Data
1. From the Azure Portal overview, open the lab-provided SQL server.
2. Under the Security > Networking section in the Public access tab, go under Firewall rules and click Add your client IPv4 address ([Your public IP shown]).
3. Click Save.
4. In the left-hand menu, click SQL databases.
5. Click acweb_arch.
6. In the left-hand menu, click Query editor (preview).
6. Logi n using the following credentials:
* Login: Delmar
* Password: AwesomePassword!
8. Click Ok.
9. Copy and paste the following code in to the query box and click Run

```
CREATE TABLE [dbo].[ProductArchive](
	[ProductID] [int],
	[Name] [nvarchar](40),
	[ProductNumber] [nvarchar](25),
	[Color] [nvarchar](15),
	[StandardCost] [money],
	[ListPrice] [money],
	[Size] [nvarchar](5),
	[Weight] [decimal](8, 2),
	[ProductCategoryID] [int],
	[ProductModelID] [int],
	[SellStartDate] [date],
	[SellEndDate] [date],
	[DiscontinuedDate] [date],
	[ThumbNailPhoto] [varbinary](max),
	[ThumbnailPhotoFileName] [nvarchar](50),
	[rowguid] [uniqueidentifier],
	[ModifiedDate] [date]
) ON [PRIMARY]
GO
```

## Create a Pipeline to Move the Data

1. In a second tab, return to the Azure Portal overview, and open the lab-provided Data factory (V2).
2. Click Launch Studio.
3. Click the blue "New" button and choose Pipeline.
4. Once the page has loaded, click Create pipeline.
5. Rename your pipeline (pipeline1) to Product Migration.
6. Under Move & Transform, drag and drop a Data flow task to the pipeline.
7. Rename the task Product Archive and click the Settings tab.
8. Next to Data flow, click + New.
9. Click Add Source.
10. Rename your dataflow1 to be Product Archive.
11. Under Source settings, name the Output stream name acweb.
12. Next to Dataset, click + New.
13. Select Azure SQL Database and click Continue.
14. For the name, enter acweb_product, and for the Linked service, select + New.
15. Name the linked service acweb, and under Azure subscription, select the lab-provided subscription.
16. For the Server name, select the lab-provided server.
17. For the Database name, select acweb.
18. Under Authentication type, choose SQL Authentication. Enter the following for the User name and Password:
* User name: delmar
* Password: AwesomePassword!
19. Click Test connection.
20. Once the test is successful, click Create.
21. For the Table name, select SalesLT.Product.
Click Ok.

## Convert the Date Columns

1. In the pipeline, next to the acweb data flow, click the + icon, and select Derived Column.
2. At the bottom of the page, under Output stream name, enter DateTransform.
3. Under Columns, set up the following. There will be 4 columns, each with a corresponding expression. Copy and paste the expressions below for each column:

* SellStartDate:
```toDate(substring(toString(SellStartDate),1,10)) ```
* SellEndDate: 
```toDate(substring(toString(SellEndDate),1,10)) ```
* DiscontinuedDate: 
``` toDate(substring(toString(DiscontinuedDate),1,10)) ```
* ModifiedDate: 
```toDate(substring(toString(ModifiedDate),1,10)) ```

4. In the pipeline, next to the DateTransform task, click the + icon, and under Destination, select Sink.
5. At the bottom of the page, under Output stream name, enter acwebarch.
6. Next to Dataset, click + New.
7. Select Azure SQL Database and click Continue.
8. For the name, enter acweb_arch_productarchive, and for the Linked service, select + New.
9. Name the linked service acweb_arch, and under Azure subscription, select the lab-provided subscription.
10. For the Server name, select the lab-provided server.
11. For the Database name, select acweb_arch.
12. Enter the following for the User name and Password, and click Test

connection:
* User name: delmar
* Password: AwesomePassword!

13. Once the test is successful, click Create.
14. For the Table name, select dbo.ProductArchive.
15. Click Ok.
16. At the top of the page, click Publish all, then "Publish".
17. Once finished, click the Product Migration tab.
18. Click Add trigger, and select Trigger now.
19. Click Ok.
20. Using the left-hand menu, navigate to the Monitor section and monitor the progress.
21. Once completed, navigate back to the Query editor tab, and run the following query:

```SELECT * FROM dbo.ProductArchive```