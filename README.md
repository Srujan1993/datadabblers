
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

- Create a Fabric enabled workspace in [app.powerbi.com](http://app.powerbi.com)
  
![image](https://github.com/Srujan1993/datadabblers/blob/d1adc4b2b13a547ac7f770dc4ecfe131ab3dfe2a/MicrosoftFabric/Assets/01-Fabric_Home_Page.png)

## Creation of Lakehouse in Microsoft Fabric

- We will be bringing the data into Lakehouse to perform insights using Medallion Architecture.

- Click on New Fabric Item option
  
![image](https://github.com/Srujan1993/datadabblers/blob/d1adc4b2b13a547ac7f770dc4ecfe131ab3dfe2a/MicrosoftFabric/Assets/02-Fabric_New_Item.png)

- Search for Fabric Lakehouse Option and click on it
  
![image](https://github.com/Srujan1993/datadabblers/blob/d1adc4b2b13a547ac7f770dc4ecfe131ab3dfe2a/MicrosoftFabric/Assets/03-Create_Fabric_Lakehouse_Item.png)

- A new lakehouse creation window opens , provide details accordingly

![image](https://github.com/Srujan1993/datadabblers/blob/d1adc4b2b13a547ac7f770dc4ecfe131ab3dfe2a/MicrosoftFabric/Assets/04_Fabric_Lakehouse_Creation_Window.png)
  
- A lakehouse when created will have a semantic model and SQL analytics endpoint. We have named our Lakehouse as AdventureWorks_Lakehouse
  
![image](https://github.com/Srujan1993/datadabblers/blob/ac87948d95618bf965237873c2801a5774296641/MicrosoftFabric/Assets/05_Create_Fabric_Lakehouse.png)


- We will be detailing the implementation steps in three phases

  - **Data Ingestion**
  - **Data Transformation**
  - **Data Presentaion**
    

## Data Ingestion

### Data Ingestion - HubSpot CRM to Microsoft Fabric

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

### Data Ingestion - Mirroring ERP Data to Microsoft Fabric

   - We use mirroring feature in fabric to load ERP data from Azure SQL Database into Microsoft Fabric One Lake showcasing the advantage of avoiding complex ETL for bringing the data into Fabric
   - Mirroring in Fabric is a fully managed service, so you don't have to worry about hosting, maintaining, or managing replication of the mirrored connection.
   - Flexibility of choosing whether to replicate an entire database or individual tables and Mirroring will automatically keep your data in sync. Once set up, data will continuously replicate into the OneLake for analytics consumption.
   - Mirroring in Fabric provides an easy experience to speed the time-to-value for insights and decisions

#### Steps to configure mirroring in MS Fabric

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

### Data Ingestion - Load Social Reviews Data into Microsoft Fabric using Shortcuts
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

## Data Transformation

Now that we have successfully ingested data into the Microsoft Fabric One Lake, our next crucial step is to logically organize this data into distinct categories:
- Raw data
- Cleansed and transformed data
- Aggregated data for comprehensive business analysis and insights

This systematic organization is essential for efficient data management and utilization.

### Lakehouse Medallion Architecture

To achieve this structured approach, we have implemented the Lakehouse Medallion Architecture on our data. 

The Medallion architecture is composed of three distinct layers or zones, each representing a different level of data refinement:

1. **Bronze**: This initial layer contains the raw, unprocessed data as it is ingested from various sources.
2. **Silver**: In this intermediate layer, data undergoes cleansing, validation, and transformation processes.
3. **Gold**: The final layer houses fully refined, aggregated, and analysis-ready data.

Each successive layer in this architecture indicates an increase in the quality and usability of the data stored in the lakehouse, with higher levels representing more refined and valuable information.

### Implementation of Lakehouse Medallion Architecture

To implement this architecture effectively, we have created custom schemas within our lakehouse for each layer:

- Ops_Bronze: Houses the raw, unprocessed data of CRM,ERP and Social Media Reviews as Delta Lake Tables
- Ops_Silver: Contains cleansed and transformed data as Delta Lake Tables 
- Ops_Gold: Stores aggregated, analysis-ready data as Delta Lake Tables

- The use of Delta Lake Tables ensures data integrity,reliability and optimised query performance as it transcends through the bronze, silver, and gold layers in our Medallion architecture implementation.

- we have used Spark Notebooks within Microsoft Fabric for Data Processing within the Medallion Architecture. 

#### Bronze Layer in Medallion Architecture

- Within Bronze Layer , we created a fabric notebook and used PySpark code to read crm and social review data from lakehouse files and mirrored tables brought as shortcuts in Lakehouse default schema as input sources
- This data is then converted into our bronze schema format and loaded as delta lake tables
- Link to Notebook: [Lakehouse_Ingestion_Medallion_Bronze_Layer](https://github.com/Srujan1993/datadabblers/blob/2e53193f597bb89d5f8fe25527387ed9054d7c01/MicrosoftFabric/DataTransformation/Notebooks/Bronze/Lakehouse_Ingestion_Medallion_Bronze_Layer.ipynb)

![image](https://github.com/Srujan1993/datadabblers/blob/555478be204da46d782c3da4d9ddaeacf769720c/MicrosoftFabric/DataTransformation/assets/01_Lakehouse_Medallion_Bronze_Schema.png)

#### Silver Layer in Medallion Architecture

- Within Silver Layer , we created two fabric notebooks which implemented data cleansing , data enrichement and transformation
- This notebook : [Lakehouse_Transformation_Medallion_Silver_Layer_CRM_ERP_Data.ipynb](https://github.com/Srujan1993/datadabblers/blob/2e53193f597bb89d5f8fe25527387ed9054d7c01/MicrosoftFabric/DataTransformation/Notebooks/Silver/Lakehouse_Transformation_Medallion_Silver_Layer_CRM_ERP_Data.ipynb) is used for data cleaning and data transformation on raw bronze data to generate optimised version of delta lake tables containing ERP and CRM tables such as products, sales, customers etc
- This notebook : [Lakehouse_Transformation_Medallion_Silver_Layer_SocialReview_Data.ipynb](https://github.com/Srujan1993/datadabblers/blob/2e53193f597bb89d5f8fe25527387ed9054d7c01/MicrosoftFabric/DataTransformation/Notebooks/Silver/Lakehouse_Transformation_Medallion_Silver_Layer_SocialReview_Data.ipynb)  reads the data from social twitter reviews table in bronze, and with the use of Azure OpenAI we extract/estimate the item being reviewed from the review text and add a Product Model column to reflect the item in review and provide a key to join to the product table.

![image](https://github.com/Srujan1993/datadabblers/blob/2e53193f597bb89d5f8fe25527387ed9054d7c01/MicrosoftFabric/DataTransformation/assets/02_Lakehouse_Medallion_Silver_Schema.png)

#### Gold Layer in Medallion Architecture

- Within Gold Layer Layer , we created three fabric notebooks which implemented for dimemsional data modelling generating dim , fact tables using dataframes and Azure Open AI LLMs
- Notebook 1
  - Dimension and Fact tables are created for Products and Sales data using PySpark Notebooks and are loaded as delta lake tables under Gold Schema in the Lakehouse
  - Dimension tables are created for Products , Customer and Companies where as Fact table is created for Sales data containing details related to Sales along with territorial information and for sales reason
  - Link to Notebook : [Lakehouse_Medallion_Gold_Layer_without_LLM.ipynb](https://github.com/Srujan1993/datadabblers/blob/2e53193f597bb89d5f8fe25527387ed9054d7c01/MicrosoftFabric/DataTransformation/Notebooks/Gold/Lakehouse_Medallion_Gold_Layer_without_LLM.ipynb)
- Notebook 2
  - This notebook utilises Azure OpenAI to evaluate the reviews from social media sites. The LLM is used to :
      - Summarise the overall sentiment of customer reviews, whether positive, negative, or neutral.
      - Extract key insights and themes that stand out, giving us a clearer picture of what customers care about most.
      - Identify critical inputs or specific product feedback that could drive product improvements or marketing decisions.
  - Link to Notebook : [Lakehouse_Medallion_Gold_Layer_Fact_SocialReviewAnalysis_with_LLM.ipynb](https://github.com/Srujan1993/datadabblers/blob/2e53193f597bb89d5f8fe25527387ed9054d7c01/MicrosoftFabric/DataTransformation/Notebooks/Gold/Lakehouse_Medallion_Gold_Layer_Fact_SocialReviewAnalysis_with_LLM.ipynb)
- Notebook 3
  - This notebook uses the dim and fact tables data created within in the gold layer to come up with aggregated data like sales analyis by product model , prouct categories , Territorial Sales , Sales by Seller Type
  - Utilising Azure OpenAI to summarise the sales aggregated data in the gold layer. Utilising multiple calls to the LLM we compare previous months and years as well as analysing trends and anomalies. Key comparisons are stored as individual values in fact ai sales analysis table.
  - Evaluating the results from the LLM calls in the previous step, we utilise the LLM again to extract insights and understand why sales performed as they did. Combining with sales data a product model category level, territories, customer type and social reviews analysis, the LLM was able to identify reasons. Key insights 
        are stored as individual values in fact ai sales analysis table
  - Link to Notebook : [Lakehouse_Medallion_Gold_Layer_Fact_Sales_Analysis_with_LLM.ipynb](https://github.com/Srujan1993/datadabblers/blob/2e53193f597bb89d5f8fe25527387ed9054d7c01/MicrosoftFabric/DataTransformation/Notebooks/Gold/Lakehouse_Medallion_Gold_Layer_Fact_Sales_Analysis_with_LLM.ipynb)

  ![image](https://github.com/Srujan1993/datadabblers/blob/2e53193f597bb89d5f8fe25527387ed9054d7c01/MicrosoftFabric/DataTransformation/assets/03_Lakehouse_Medallion_Gold_Schema.png) 

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
