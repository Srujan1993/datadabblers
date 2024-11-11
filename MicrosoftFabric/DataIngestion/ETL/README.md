# HubSpot CRM
In this project, HubSpot is utilised to store Companies and Contacts from the Adventure Works database in a separate system, simulating a typical enterprise setup.

HubSpot was chosen to showcase the CRM component because of its popularity and its free tier, which includes all the essential features needed for this project.

Note: To import the complete list of Contacts from the provided import file, an upgrade to the minimum paid tier is required to increase the contact limit.

## HubSpot Configuration

Once you have created an HubSpot account you will need to configure it to be able to import the Contacts and Companies.

### Companies

The import file HubSpot_Company_Import.csv in this repository is an extract of Stores from the Adventure Works database with all the required columns to successfully import into HubSpot and the IDs to join back to the ERP tables after importing into Fabric.

The minimum required fields to add a company to HubSpot is a Domain Name, Company Name and the Company Owner. You will need to add the name of the Company Owner in the CompanyOwner column. If you have just created an HubSpot account, you will most likely only have one user that can be the Company Owner which will be the name you used to create the account with.

Now the import file is complete you will need to configure HubSpot to include the extra fields we have added. To do this you will need to follow these steps:

- Navigate to Companies under CRM in the Navigation Bar
- Click Edit properties under Actions
- Click Create property and fill in the following details:
	- Property label: ErpStoreId
	- Object type: Company
	- Group: Company information
	- Field type: # Number
	- Number format: Unformatted number
- Click Save

You are now ready to import the list of Companies from the CSV file. Follow the steps below to do this:

- Navigate to Companies under CRM in the Navigation Bar
- Click Import and then Import a file
- Select Companies as the type of data in the file and click next
- Drag and drop the HubSpot_Company_Import.csv file to upload it
- Select Create and update Companies
- Ensure all the columns in the import file are mapped to the right HubSpot property
- Click next
- Click Finished import

![image](https://github.com/Srujan1993/datadabblers/blob/748d28c6d88a3eabe3bb687963f7fea3088b673c/MicrosoftFabric/DataIngestion/ETL/assets/Hubspot%20Companies%20Import%20Mapping.png)
 
### Contacts

The import file HubSpot_Contacts_Import.csv in this repository is an extract of Customers from the Adventure Works database with all the required columns to successfully import into HubSpot and the IDs to join back to the ERP tables after importing into Fabric.

You will need to add the name of the Contact Owner in the ContactOwner column.

Now the import file is complete you will need to configure HubSpot to include the extra fields we have added. To do this you will need to follow these steps:

- Navigate to Contacts under CRM in the Navigation Bar
- Click Edit properties under Actions
- Click Create property and fill in the following details:
	- Property label: ErpStoreId
	- Object type: Contact
	- Group: Contact information
	- Field type: # Number
	- Number format: Unformatted number
- Click Save
- Click Create property and fill in the following details:
	- Property label: ErpPersonId
	- Object type: Contact
	- Group: Contact information
	- Field type: # Number
	- Number format: Unformatted number
- Click Save
- Click Create property and fill in the following details:
	- Property label: Contact Type
	- Object type: Contact
	- Group: Contact information
	- Field type: Dropdown select
	- Sort: Custom
	- Dropdown options: {Order: 1, Label: Direct} & {Order: 2, Label: Reseller}
- Click Save

You are now ready to import the list of Companies from the CSV file. Follow the steps below to do this:

- Navigate to Contacts under CRM in the Navigation Bar
- Click Import and then Import a file
- Select Contacts as the type of data in the file and click next
- Drag and drop the HubSpot_Contacts_Import.csv file to upload it
- Select Create and update Contacts
- Ensure all the columns in the import file are mapped to the right HubSpot property
- Click next
- Click Finished import

![image](https://github.com/Srujan1993/datadabblers/blob/748d28c6d88a3eabe3bb687963f7fea3088b673c/MicrosoftFabric/DataIngestion/ETL/assets/Hubspot%20Contacts%20Import%20Mapping.png)
 
### API

Next you need to configure an integration to pull data from HubSpot API. Complete the following steps to configure an API endpoint for Contacts and Companies:

- Navigate to Integrations under Data Management in the Navigation Bar
- Navigate to Private app
- Click Create a private app
- Enter a name: ms-fabric-ingestion
- Add the following scopes
	- crm.objects.companies.read
	- crm.objects.companies.sensitive.read
	- crm.objects.contacts.read
	- crm.objects.contacts.sensitive.read
