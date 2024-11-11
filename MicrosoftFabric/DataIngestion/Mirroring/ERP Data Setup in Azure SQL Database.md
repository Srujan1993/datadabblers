### **Steps to setup ERP Data in Azure SQL Database**

 ### **Prerequisites**

  - Use an existing Azure Account with a valid Azure Subscription or else create a free azure account using this details https://azure.microsoft.com/en-in/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
  - An Azure SQL Database needs to be created in a valid Azure Subscription as we are using Azure SQL Database in our Scenario. 
  - Install SQL Server Management Studio or Azure Data Studio on your local machine

### **Steps to create an Azure SQL Database**
 
  - Use this link : https://learn.microsoft.com/en-us/azure/azure-sql/database/free-offer?view=azuresql to follow the stpes and create an azure sql database in your azure subscription

  - while setting up the database , go for mixed mode authentication , we would be using sql authentication (username and passoword) later while we are connecting to the sql database in Microsoft Fabric
 
  - If you do not have an existing azure sql database in your current subscription , you can choose the free tier offering  giving you 100,000 vCore seconds of serverless database compute and a maximum size of 32 GB of data.

  - We utilized an Azure pay-as-you-go subscription within our personal Azure Account for the purpose of   
   establishing and configuring the necessary Azure Components.

  - Under the Subscription, Azure PaaS SQL Database named "freedemodb" was created
 
  - Once the database is created , it would be like below in the azure subsccription
 
  - Add image 01
 
  - Free tier Database is allowed one per subscription,so we are able to configure the database in free tier as mentioned in the screenshot . However , we have consumed the 100000vCore seconds limit.
 
  - Try to utilise free tier in developer/sandbox use cases to save running costs on the azure sql database 

  -  Next step is load the adventure works dataset(tables) into the sql database


  ### **Steps to Configure Adventure Works Dataset**
    
   - Use the details mentioned in the github link mentioned below to restore Adventure works data into your own Azure SQL Database
   - [sql-docs/docs/samples/adventureworks-install-configure.md at live · MicrosoftDocs/sql-docs · GitHub](https://github.com/MicrosoftDocs/sql-docs/blob/live/docs/samples/adventureworks-install-configure.md)
   - This github link provides comprehensive details on how to install the adventure works database tables into the azure sql database.
   - A screenshot of adventure works tables loaded into the azure sql database
   - Add image 02
     
   ### **Tables to be used in the ERP Database for our use case**
   
   - In the context of our specific use case, we focused our attention on leveraging the tables pertaining to Products and Sales from the loaded Adventure Works data. 
   - These particular datasets were chosen for their relevance to our project objectives and their ability to provide valuable insights into product and sales performance metrics.

      - Production.Product
      - Production.ProductCategory
      - Production.ProductModel
      - Production.ProductSubCategory
      - Sales.Customer
      - Sales.SalesOrderaHeader
      - Sales.SalesOrderHeaderSalesReason
      - Sales.SalesReason
      - Sales.SalesTerritory
      - Sales.SpecialOffer
