
# Project X : Using Microsoft Fabric and AI

About the project 


## Use Case

About the use case

## High Level Architecture

![image](https://github.com/user-attachments/assets/ad8dd1c2-f2bd-4146-af4c-e244ef390d81)

## Components used in the Project

* **Data Sources** : HubSpot CRM , ERP data on Azure SQL Database , Social Media Ratings and Reviews File from AWS S3
* **Components/Features used within Microsoft Fabric** : Shortcuts , Mirroring , Lakehouse , Data Pipeline , Spark Notebooks , Power BI
* **Azure Components** : Azure PaaS SQL Database , Azure Open AI Proxy Service


## Setting up the Project

### Hubspot CRM Setup

To be entered

### ERP Data Setup

- For this project, we leveraged Azure Platform as a Service (PaaS) SQL Database as our Enterprise Resource Planning (ERP) system.
- To populate our ERP system with realistic and comprehensive data, we utilized the Adventure Works Sample Database, a well-known and feature-rich dataset provided by Microsoft.
- This sample database offers a wide array of business scenarios and data structures, making it an ideal choice for our project's requirements.
- Please follow the detailed instructions provided below to replicate our setup and ensure a smooth implementation of the Azure PaaS SQL Database with the Adventure Works Sample Database as your ERP foundation.

    ### **Steps to setup ERP Data in Azure SQL Database**

    ### **Prerequisites**
    - An Azure SQL Database needs to be created in a valid Azure Subscription as we are using Azure SQL Database in our Scenario. 
    - Install SQL Server Management Studio or Azure Data Studio

    ### **Steps to Configure Adventure Works Dataset**
    - Use the details mentioned in the github link mentioned below to restore Adventure works data into your own Azure SQL Database
    - [sql-docs/docs/samples/adventureworks-install-configure.md at live · MicrosoftDocs/sql-docs · GitHub](https://github.com/MicrosoftDocs/sql-docs/blob/live/docs/samples/adventureworks-install-configure.md)

    ### **Our Azure SQL Database Setup**
    - We utilized an Azure pay-as-you-go subscription within our personal Azure Account for the purpose of establishing and configuring the necessary Azure Components.
    - Under the Subscription, Azure PaaS SQL Database named "freedemodb" was created. This database was subsequently populated with the comprehensive Adventure Works dataset.
    - In the context of our specific use case, we focused our attention on leveraging the tables pertaining to Products and Sales from the loaded Adventure Works data. 
    - These particular datasets were chosen for their relevance to our project objectives and their ability to provide valuable insights into product and sales performance metrics.

    ### **Tables used in the ERP Database for our use case**
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









