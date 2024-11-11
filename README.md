
## Project FabricIQ : Data Driven Intelligence with Microsoft Fabric and Azure Open AI

About the project 


## Use Case

About the use case

## High Level Architecture

![image](https://github.com/user-attachments/assets/cbd8106f-1ae6-4570-b9f7-fa9fd6ed0659)

## Project Demonstration using Video

Video file to be uploaded

## Components used in the Project

* **Data Sources** : HubSpot CRM , ERP data on Azure SQL Database , Social Media Ratings and Reviews File from AWS S3
* **Components/Features used within Microsoft Fabric** : Shortcuts , Mirroring , Lakehouse , Data Pipeline , Spark Notebooks , Power BI
* **Azure Components** : Azure PaaS SQL Database , Azure Open AI Proxy Service


## Setting up the Project

### Hubspot CRM Setup

To be entered

### ERP Data Setup

[Steps to setup ERP Data in Azure SQL Database](https://github.com/Srujan1993/datadabblers/blob/4b4b2e4f525276942aa46d3063e3f6a73d23f551/MicrosoftFabric/DataIngestion/Mirroring/ERP%20Data%20Setup%20in%20Azure%20SQL%20Database.md)

### Ratings and Reviews

[Steps to setup social review in AWS S3](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/reviews_data_setup_aws_s3.md)


# Detailed Project Setup in Microsoft Fabric

   1. Create a Fabric enabled workspace in [app.powerbi.com](http://app.powerbi.com)

   2. Create a Fabric Lakehouse within the Fabric Workspace. We will be bringing the data into Lakehouse to perform insights using Medallion Architecture. A lakehouse when created will have a semantic model and SQL
      analytics endpoint. We have named our Lakehouse as AdventureWorks_Lakehouse

   4. As part of our use case, we need to bring the data from Hubspot CRM, Azure SQL Database, and an AWS S3 holding the social media reviews file


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
- Search for Mirrored Azure SQL Database Option in New Item Window
- Once you click in the Mirrored Azure SQL Database, a new screen will pop up asking for the database connection to be setup. I have already setup the connection, so you are seeing one under one lake data hub
- On clicking Azure SQL Database, Connection window opens where you need to provide SQL Database Connection String Details and a connection name.
- Once connection is established, window will be opened with all the tables associated with your database
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
- When mirroring is successful, these three items are created in Fabric Workspace:
  - Mirroring manages the replication of data into [OneLake](https://learn.microsoft.com/en-us/fabric/onelake/onelake-overview) and conversion to Parquet, in an analytics-ready format. This enables downstream scenarios like data engineering, data science, and more.
  - A SQL Analytics Endpoint
  - A Default Semantic Model

3. Load Social Reviews Data into Microsoft Fabric using Shortcuts
   - We have used Fabric Shortcuts feature to bring the data into Fabric Lakehouse without replicating data but using a pointer reference to the same.
   - To establish a connection (or shortcut) between the Amazon S3 bucket and Fabric Lakehouse, a few configurations are required:
     
     a. Amazon S3 Permissions:
     Set up bucket policies to grant Fabric Lakehouse permission to access the S3 bucket. This includes:
     - Enabling read access for the Fabric Lakehouse service.
     - Configuring the IAM access policy allows read-only access to the necessary S3 bucket resources.
     - Create and configure access keys for authorized access. Ensure the access keys are stored securely and have the appropriate permissions to access the specified S3 bucket.
     
     b. Fabric Lakehouse Shortcut Setup:
     - In the Fabric Lakehouse environment, use the shortcut feature to create a connection to the S3 bucket where the social review data is stored.
     - Click on Files → Create Shortcut
     - On clicking new shortcut option, a shortcut wizard opens up
     - Ensure that the connection setup uses the access keys configured on the S3 bucket, and validate the connection by testing access to the sample social review data file
     - Verify that the shortcut connection enables data reading and ingestion workflows within the Lakehouse.








