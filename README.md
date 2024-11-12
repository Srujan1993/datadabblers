
# Project FabricIQ : Data Driven Intelligence with Microsoft Fabric and Azure Open AI

With the increasing adoption of SaaS platforms, companies often rely on multiple systems to manage their daily operations. While this approach ensures the use of top-tier solutions for specific tasks, it makes obtaining a comprehensive view of the business challenging, as data is scattered across disconnected databases. Traditionally, consolidating this data into a central repository required skilled data engineers and architects to develop and maintain complex ETL pipelines. Microsoft Fabric simplifies this process for businesses of all sizes by offering a robust platform that can ingest and store data from various sources, transform it, and deliver it to users through dynamic reports and integrations. This can be accomplished using either No-Code or Pro-Code tools within Fabric.

Building on this foundation, the use of Large Language Models (LLMs) can significantly expedite the analysis of vast amounts of data, a task that would otherwise take analysts days or even weeks to complete. This enables faster data-driven decision-making, allowing businesses to quickly adapt to market changes and stay ahead of competitors.

As a demonstration of these capabilities, we developed a PowerBI report designed for Sales and Marketing directors who require clear, actionable insights for their product portfolio. The report includes:

- An AI-generated summary of sales data, comparing performance across different months and years while highlighting trends and anomalies.
- AI-driven insights exploring the factors influencing sales.
- AI-suggested actions identifying top priorities for improving sales based on the generated insights.

This report provides all the necessary information to make informed decisions, improve sales, and gain a competitive advantage in rapidly changing markets.

## High Level Architecture

![image](https://github.com/user-attachments/assets/cbd8106f-1ae6-4570-b9f7-fa9fd6ed0659)

## Project Demonstration using Video

Video file to be uploaded

## Components used in the Project

* **Data Sources** : HubSpot CRM , ERP data on Azure SQL Database , Social Media Ratings and Reviews File from AWS S3
* **Components/Features used within Microsoft Fabric** : Shortcuts , Mirroring , Lakehouse , Data Pipeline , Spark Notebooks , Power BI
* **Azure Components** : Azure PaaS SQL Database , Azure Open AI

## Prerequisites

### Hubspot CRM Setup

[Steps to set up HubSpot CRM](https://github.com/Srujan1993/datadabblers/blob/2b7181f17f945afe025ad5fb6e356d1235d8ede5/MicrosoftFabric/DataIngestion/ETL/HubSpot%20CRM%20Setup.md)

### ERP Data Setup

[Steps to set up ERP Data in Azure SQL Database](https://github.com/Srujan1993/datadabblers/blob/4b4b2e4f525276942aa46d3063e3f6a73d23f551/MicrosoftFabric/DataIngestion/Mirroring/ERP%20Data%20Setup%20in%20Azure%20SQL%20Database.md)

### Ratings and Reviews

[Steps to set up social review in AWS S3](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/reviews_data_setup_aws_s3.md)


# Detailed Project Setup in Microsoft Fabric

1. Create a Fabric enabled workspace in [app.powerbi.com](http://app.powerbi.com)

2. Create a Fabric Lakehouse within the Fabric Workspace. We will be bringing the data into Lakehouse to perform insights using Medallion Architecture. A lakehouse when created will have a semantic model and SQL
analytics endpoint. We have named our Lakehouse as AdventureWorks_Lakehouse

3. As part of our use case, we need to bring the data from Hubspot CRM, Azure SQL Database, and an AWS S3 holding the social media reviews file

## Data Ingestion - HubSpot CRM to Microsoft Fabric

In this project, we used HubSpot to store Companies and Contacts from the Adventure Works database in a separate system, simulating a typical enterprise setup.

We will be ingesting data from HubSpot via API in a ETL Pipeline, storing the data as a CSV file in the LakeHouse. 

![image](https://github.com/Srujan1993/datadabblers/blob/902760307681bb2bb05c7c2e39c813f9728e8f7c/MicrosoftFabric/DataIngestion/ETL/assets/Hubspot%20CRM%20Import%20Pipeline.png) 

Steps to create and configure an ETL Pipeline for Companies and Customers
- Create new Data Pipeline
   - Within your Fabric workspace, click + New item and select Data pipeline
- Create Delete data activities
   1.	Create a Delete data block from the Activities tab, Move and transform, Delete data
   2.	Name it “Delete Companies CSV” 
   3.	Set the connection to your LakeHouse
   4.	Set it as File path with path: Files / HubspotCrm / Companies.csv
   5.	Disable Logging setting
   6.	Repeat steps 1-6 for Contacts, substituting the name with “Delete Customers CSV” and the path: Files / HubspotCrm / Customers.csv
- Create Copy data activity for Companies
   1.	Next create a Copy data activity and call it “CopyHubSpotCrmCompanies”
   2.	Under Source tab, create a new connection and select REST (this creates the base URL connection for all HubSpot API calls)
	   - Add the Base URL: https://api.hubapi.com/crm/v3
	   - Leave Token Audience Uri blank
	   - Create new connection
	   - Name the Connection:  HubspotCrmApi
	   - Data gateway: none
	   - Authentication kind: Anonymous
   3.	Add the relative URL (the properties selects the specific fields you want the API to return): objects/companies?properties=erpstoreid,domain,name,address,address2,city,state,country,zip
   4.	Request method: GET
   5.	Additional headers:
	   - Name: Authorization
	   - Value: Bearer [Hubspot access token you saved earlier]
   6.	Pagination rules:
	   - Name: RFC5988 / Value: False
	   - Name: AbsoluteURL [Blank] / Value: Body [paging.next.link]
   7.	Under Destination Tab
	   - Connection: Select your lakehouse
	   - Root folder: Files
	   - Path: HubspotCrm / Companies.csv
	   - File format: DelimitedText
   8.	Use the image below to configure Mapping

![image](https://github.com/Srujan1993/datadabblers/blob/902760307681bb2bb05c7c2e39c813f9728e8f7c/MicrosoftFabric/DataIngestion/ETL/assets/Hubspot%20Ingestion%20Pipeline%20Company%20Mapping.png)

- Create Copy data activity for Customers
   1.	Next create a Copy data activity and call it “CopyHubSpotCrmCustomers”
   2.	Under Source tab, select the connection you created for the companies Copy data activity as they share the same base URL.
   3.	Add the relative URL (the properties selects the specific fields you want the API to return): objects/contacts? 
    properties=erpstoreid,erppersonid,Company,jobtitle,firstname,lastname,email,phone,contact_type
   4.	Request method: GET
   5.	Additional headers:
	   - Name: Authorization
	   - Value: Bearer [Hubspot access token you saved earlier]
   6.	Pagination rules:
	   - Name: RFC5988 / Value: False
	   - Name: AbsoluteURL [Blank] / Value: Body [paging.next.link]
   7.	Under Destination Tab
	   - Connection: Select your lakehouse
	   - Root folder: Files
	      - Path: HubspotCrm / Customers.csv
	      - File format: DelimitedText
   8.	Use the image below to configure Mapping

![image](https://github.com/Srujan1993/datadabblers/blob/902760307681bb2bb05c7c2e39c813f9728e8f7c/MicrosoftFabric/DataIngestion/ETL/assets/Hubspot%20Ingestion%20Pipeline%20Customer%20Mapping.png)

- We used an ETL pipeline to import customers and companies data from CRM into the Fabric.
- To keep it simple, we are always deleting the companies and customers csv file and import the latest companies and customers data from Hubspot CRM using
- An API connection is created to the Hubspot CRM from the pipeline
- Copy activity is used within the ETL pipeline to copy files from Hubspot CRM to Lakehouse in our Fabric Workspace
- Once the pipeline runs successfully, files are pushed successfully to Lakehouse Files explorer

## Data Ingestion - Mirroring ERP Data to Microsoft Fabric

   - We use mirroring feature in fabric to load ERP data from Azure SQL Database into Microsoft Fabric One Lake showcasing the advantage of avoiding complex ETL for bringing the data into Fabric
   - Mirroring in Fabric is a fully managed service, so you don't have to worry about hosting, maintaining, or managing replication of the mirrored connection.
   - Flexibility of choosing whether to replicate an entire database or individual tables and Mirroring will automatically keep your data in sync. Once set up, data will continuously replicate into the OneLake for analytics consumption.
   - Mirroring in Fabric provides an easy experience to speed the time-to-value for insights and decisions

### Steps to configure mirroring in MS Fabric

- Go to your Fabric Enabled Workspace and Click on New Item

  ![image](https://github.com/Srujan1993/datadabblers/blob/2e6adfac01d2abaa3480316e910cced729533d58/MicrosoftFabric/DataIngestion/Mirroring/assets/03-Create_New_Item_In_Fabric.png)
  
- Search for Mirrored Azure SQL Database Option in New Item Window
  
  ![image](https://github.com/Srujan1993/datadabblers/blob/2e6adfac01d2abaa3480316e910cced729533d58/MicrosoftFabric/DataIngestion/Mirroring/assets/04-Select_Mirror_Azure_SQL_Database_Option.png)
  
- Once you click in the Mirrored Azure SQL Database, a new screen will pop up asking for the database connection to be setup. I have already setup the connection, so you are seeing one under one lake data hub
  
  ![image](https://github.com/Srujan1993/datadabblers/blob/2e6adfac01d2abaa3480316e910cced729533d58/MicrosoftFabric/DataIngestion/Mirroring/assets/05-Database_Connection_Window.png)
  
- On clicking Azure SQL Database, Connection window opens where you need to provide SQL Database Connection String Details and a connection name.
  
  ![image](https://github.com/Srujan1993/datadabblers/blob/2e6adfac01d2abaa3480316e910cced729533d58/MicrosoftFabric/DataIngestion/Mirroring/assets/06-Database_Connection_Settings_Window.png)
  
- Once connection is established, window will be opened with all the tables associated with your database
  
  ![image](https://github.com/Srujan1993/datadabblers/blob/2e6adfac01d2abaa3480316e910cced729533d58/MicrosoftFabric/DataIngestion/Mirroring/assets/07-Mirrored_Azure_SQL_Database_Screen.png)
  
- Choose the relevant tables which you want to be mirrored into Microsoft Fabric One Lake
  
- For our use case, we have mirrored below tables:
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
- Once you click on connect, mirroring replication starts and it redirects to this window

  ![image](https://github.com/Srujan1993/datadabblers/blob/2e6adfac01d2abaa3480316e910cced729533d58/MicrosoftFabric/DataIngestion/Mirroring/assets/08-Monitoring_Mirrored_Replication_Azure_SQL_Database.png)
  
- When mirroring is successful, these three items are created in Fabric Workspace

  ![image](https://github.com/Srujan1993/datadabblers/blob/2e6adfac01d2abaa3480316e910cced729533d58/MicrosoftFabric/DataIngestion/Mirroring/assets/09-Created_Mirrored_Database_Screnshot.png)

   - Mirroring manages the replication of data into One Lake and conversion to Parquet, in an analytics-ready format. This enables downstream 
     scenarios like data engineering, data science, and more.
   - A SQL Analytics Endpoint
   - A Default Semantic Model

### Load Social Reviews Data into Microsoft Fabric using Shortcuts
We have used Fabric Shortcuts feature to bring the data into Fabric Lakehouse without replicating data but using a pointer reference to the same. To establish a connection (or shortcut) between the Amazon S3 bucket and Fabric Lakehouse, the following configurations is required:
     
1. Amazon S3 Permissions:
Set up bucket policies to grant Fabric Lakehouse permission to access the S3 bucket. This includes:
	- Enabling read access for the Fabric Lakehouse service.
	- Configuring the IAM access policy allows read-only access to the necessary S3 bucket resources.
	- Create and configure access keys for authorized access. Ensure the access keys are stored securely and have the appropriate permissions to access the specified S3 bucket.

2. Fabric Lakehouse Shortcut Setup:
	- In the Fabric Lakehouse environment, use the shortcut feature to create a connection to the S3 bucket where the social review data is stored.
	- Click on Files → Create Shortcut
	- On clicking new shortcut option, a shortcut wizard opens up
	- Ensure that the connection setup uses the access keys configured on the S3 bucket, and validate the connection by testing access to the sample social review data file
	- Verify that the shortcut connection enables data reading and ingestion workflows within the Lakehouse.

## Processing

### Bronze

### Silver

### Gold

## Semantic Model

A Semantic Model is used to build a business layer with standardised metrics and relationships, establishing a single source of truth for reporting and querying. In this case it is used as the underlying model for the PowerBI report. 
 
![image](https://github.com/Srujan1993/datadabblers/blob/35af8301abd555faae22dd5256efe0087bdf8810/MicrosoftFabric/DataPresentation/PowerBIReporting/assets/Full%20Semantic%20Model.png)

### Creating a Semantic Model

- From your Fabric Workspace, click on your LakeHouse and click New semantic model
- Enter a name: SalesSemanticModel
- The workspace should already be selected
- Select the tables you want to bring into your model
- Click Confirm
 
![image](https://github.com/Srujan1993/datadabblers/blob/35af8301abd555faae22dd5256efe0087bdf8810/MicrosoftFabric/DataPresentation/PowerBIReporting/assets/Create%20Semantic%20Model.png)

### Managing Relationships
- Once you have created your model, navigate to the Open Data Model visual editor.
- From here you can create relationships between the tables you have added to your model.
- To create a relationship, drag the column from one table to the corresponding column in the second table, in this case we are linking the OrderDate from the fact_sales table to the Date in dim_calendar table.
- This will create a line between the two tables, double click on this line to configure the relationship, selecting cardinality as many to one and cross-filter direction to both.

![image](https://github.com/Srujan1993/datadabblers/blob/35af8301abd555faae22dd5256efe0087bdf8810/MicrosoftFabric/DataPresentation/PowerBIReporting/assets/Edit%20Semantic%20Model%20Relationship.png) 

- You will need to repeat the steps for all the table joins. The image below shows the full list of joins and their configuration.

![image](https://github.com/Srujan1993/datadabblers/blob/35af8301abd555faae22dd5256efe0087bdf8810/MicrosoftFabric/DataPresentation/PowerBIReporting/assets/Manage%20Semantic%20Model%20Relationships.png)

## PowerBI Report

The final product of this project is a PowerBI report that presents the AI-generated content and the underlying data in a clear and concise manner. The main components of the report include:

- An AI-generated summary of sales data, which compares performance across different months and years, highlighting trends and anomalies.
- AI-driven insights that explore the factors influencing sales.
- AI-suggested actions, which identify top priorities for improving sales based on the AI-generated insights.
- A visual representation of the underlying sales data supporting the AI insights.

![image](https://github.com/Srujan1993/datadabblers/blob/6d4885c7ff9494f8885e5aed97807253a020c3bf/MicrosoftFabric/DataPresentation/PowerBIReporting/assets/PowerBI%20Report%20-%20Road-250.png)
