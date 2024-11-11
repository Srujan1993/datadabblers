### Ratings and Reviews
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

To allow Microsoft Fabric to access data from Amazon S3, you’ll need to set specific access permissions and policies on your S3 bucket. Follow these steps:

- **Create Access Keys in IAM**: First, go to AWS Identity and Access Management (IAM) and create an **Access Key ID** and **Secret Access Key**. These keys will enable secure access from Microsoft Fabric.

- **Set S3 Read-Only Policy**: Apply a read-only policy to your S3 bucket. This will grant Microsoft Fabric permission to access data without altering it.
- Copy the **Access Key ID**, **Secret Access Key**, and the **folder URL**. If the entire folder is needed, copy the folder link only (remove any specific file from the URL, so it points to the folder level only).
- Reference images and detailed setup steps are available.



---

### Create a Shortcut to the S3 Bucket in Microsoft Fabric

- In Microsoft Fabric, set up a data shortcut pointing to your Amazon S3 bucket. This shortcut will serve as a persistent connection for fetching data directly from Amazon S3.
- When creating this shortcut, enter the **Url**, **Access Key ID** and **Secret Access Key** generated from AWS, along with the specific **S3 bucket name** and **path**.



---

### Validate the Connection

- After the shortcut setup, test the connection to ensure Microsoft Fabric can access and read data from the S3 bucket.
- Perform a sample data import to confirm everything is working as expected.