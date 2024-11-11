### Ratings and Reviews

Social reviews generated through OpenAI from Twitter (for simulation purposes) are saved as CSV files in an Amazon S3 bucket.
This setup is intended to mimic a real-world environment where social review data from various sources (Twitter, Facebook, Instagram, etc.) is stored in S3 for easy access and management.

The social review data is transferred to the Lakehouse folder as a shortcut, enabling quick and efficient access to data within the Fabric environment without duplicating storage.

---



### Set Up Amazon S3 Account and S3 Bucket

- If you haven’t already, create an AWS account and configure a new Amazon S3 bucket. The free tier is sufficient for this work.
- Within Amazon S3, create a dedicated bucket to store your CSV files. This bucket will act as the source location from which Microsoft Fabric can access data.

---

### Save Data as CSV Files in Amazon S3

- Store the Social Review CSV files in the S3 bucket.
- The files should be named and organised in a way that’s easy to manage and reference for data analysis purposes.

---

### Configure Access Permissions and Policies for S3 Bucket

To allow Microsoft Fabric to access data from Amazon S3, set up specific access permissions and policies for secure data access. Follow these steps:

- **Create a Dedicated IAM User**: Set up a new IAM user specifically for the Fabric connection. This user will hold the access keys and policies for this connection only, enhancing security and manageability.

![image](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/assets/01_create_user_in_s3.png)

- **Generate Access Keys in IAM**: In AWS Identity and Access Management (IAM), create an **Access Key ID** and **Secret Access Key** for the new user. These keys provide the necessary credentials for secure access from Microsoft Fabric.

![image](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/assets/02_create_access_key.png)

- **Assign an S3 Read-Only Policy**: Apply a read-only policy to this user for the S3 bucket. This policy ensures Microsoft Fabric has permission to access the data without modifying it.

![image](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/assets/03_set_up_policy.png)

- **Copy Connection Details**: Retrieve and securely store the **Access Key ID**, **Secret Access Key**, and **folder URL** for use in Microsoft Fabric. If you need access to the entire folder, copy the folder URL link only, without specifying any individual files (remove any file details from the URL).

![image](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/assets/04a_copy_Url_for_fabric_shortcut.png)

![image](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/assets/04b_copy_key_and_secret.png)



---

### Create a Shortcut to the S3 Bucket in Microsoft Fabric

- In Microsoft Fabric, set up a data shortcut pointing to your Amazon S3 bucket. This shortcut will serve as a persistent connection for fetching data directly from Amazon S3.

- To create the shortcut, click on the **...** on the right-hand side of the folder, select **Create Shortcut**, and choose **S3 Bucket**.

![image](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/assets/05a_create_shortcut.png)

![image](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/assets/05b_select_s3_bucket_shortcut.png)


- When creating this shortcut, enter the **Url**, **Access Key ID** and **Secret Access Key** generated from AWS, along with the specific **S3 bucket name** and **path**.

![image](https://github.com/Srujan1993/datadabblers/blob/main/MicrosoftFabric/DataIngestion/Shortcut/assets/05c_short_cut_setting_in_Fabric.png)

---

### Validate the Connection

- After the shortcut setup, test the connection to ensure Microsoft Fabric can access and read data from the S3 bucket.
- Perform a sample data import to confirm everything is working as expected.