
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

#### **Source Data Creation**

Social reviews generated through OpenAI from Twitter (for simulation purposes) are saved as CSV files in an Amazon S3 bucket.
This setup is intended to mimic a real-world environment where social review data from various sources (Twitter, Facebook, Instagram, etc.) is stored in S3 for easy access and management.

#### **Data Transfer**

The social review data is then transferred to the Lakehouse folder as a shortcut, enabling quick and efficient access to data within the Fabric environment without replicating data storage.

#### **Prerequisites for Fabric Lakehouse Connection**

To establish a connection (or shortcut) between the Amazon S3 bucket and Fabric Lakehouse, a few configurations are required:

##### a. Amazon S3 Permissions:

Set up bucket policies to grant Fabric Lakehouse permission to access the S3 bucket. This includes:
- Enabling read access for the Fabric Lakehouse service.
- Configuring the IAM access policy allows read-only access to the necessary S3 bucket resources.
- Create and configure access keys for authorised access. Ensure the access keys are stored securely and have the appropriate permissions to access the specified S3 bucket.

##### b. Fabric Lakehouse Shortcut Setup:

In the Fabric Lakehouse environment, use the shortcut feature to create a connection to the S3 bucket where the social review data is stored.
- Ensure that the connection setup uses the access keys configured on the S3 bucket, and validate the connection by testing access to the sample social review data files.
- Verify that the shortcut connection enables data reading and ingestion workflows within the Lakehouse.

##### Data Validation:

After setting up the shortcut, validate that data from the S3 bucket is accessible in Fabric Lakehouse. Check for any data format inconsistencies and ensure the data pipeline from S3 to Fabric operates as expected.

# Detailed Project Setup in Microsoft Fabric

1. Create a Fabric enabled workspace in [app.powerbi.com](http://app.powerbi.com)

2. Create a Fabric Lakehouse within the Fabric Workspace. We will be bringing the data into Lakehouse to perform insights using Medallion Architecture. A lakehouse when created will have a semantic model and SQL analytics endpoint. We have named our Lakehouse as AdventureWorks_Lakehouse

3. As part of our use case, we need to bring the data from Hubspot CRM, Azure SQL Database, and an AWS S3 holding the social media reviews file

## Data Ingestion

1. Loading Hub Spot CRM data into Fabric:
   - We used an ETL pipeline to import customers and companies data from CRM into the Fabric.
   - To keep it simple, we are always deleting the companies and customers csv file and import the latest companies and customers data from Hubspot CRM using
   - An API connection is created to the Hubspot CRM from the pipeline
   - Copy activity is used within the ETL pipeline to copy files from Hubspot CRM to Lakehouse in our Fabric Workspace
   - Once the pipeline runs successfully, files are pushed successfully to Lakehouse Files explorer

2. Load ERP Data from SQL Database into Fabric
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








