# Migrating-GCS-to-Amazon-S3-using-AWS-DataSync

## What is GCS Bucket in GCP?
The Buckets resource represents a bucket in Cloud Storage. There is a single global namespace shared by all buckets. For more information, see bucket name requirements.
Buckets contain objects which can be accessed by their own methods. In addition to the acl property, buckets contain bucketAccessControls, for use in fine-grained manipulation of an existing bucket's access controls.

Refer this document:- https://cloud.google.com/storage/docs/json_api/v1/buckets

## What is S3 Bucket in AWS?
Amazon Simple Storage Service (Amazon S3) is an object storage service that offers industry-leading scalability, data availability, security, and performance. Customers of all sizes and industries can use Amazon S3 to store and protect any amount of data for a range of use cases, such as data lakes, websites, mobile applications, backup and restore, archive, enterprise applications, IoT devices, and big data analytics. Amazon S3 provides management features so that you can optimize, organize, and configure access to your data to meet your specific business, organizational, and compliance requirements.

Refer this document:- https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html

## What is DataSync Service in AWS? 
AWS DataSync is an online data transfer service that simplifies, automates, and accelerates moving data between storage systems and services.

- Use cases
  - These are some of the main use cases for DataSync:

  - Data migration – Move active datasets rapidly over the network into Amazon S3, Amazon EFS, FSx for Windows File Server, FSx for Lustre, or FSx for OpenZFS. DataSync includes automatic encryption and data integrity validation to help make sure that your data arrives securely, intact, and ready to use.

  - Archiving cold data – Move cold data stored in on-premises storage directly to durable and secure long-term storage classes such as S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive. Doing so can free up on-premises storage capacity and shut down legacy systems.

  - Data protection – Move data into any Amazon S3 storage class, choosing the most cost-effective storage class for your needs. You can also send data to Amazon EFS, FSx for Windows File Server, FSx for Lustre, or FSx for OpenZFS for a standby file system.

  - Data movement for timely in-cloud processing – Move data in or out of AWS for processing. This approach can speed up critical hybrid cloud workflows across many industries. These include machine learning in the life-sciences industry, video production in media and entertainment, big-data analytics in financial services, and seismic research in the oil and gas industry.

Refer this document:- https://docs.aws.amazon.com/datasync/latest/userguide/what-is-datasync.html


## Steps to be followed:- 
### Step 1:- Go to GCP console search for GCS bucket and create a bucket upload data in bucket.
![image](https://user-images.githubusercontent.com/63963025/200155068-972cf1cc-84d0-4344-a1ea-95e173794659.png)

- inside webpage-content folder i am having 6 files,folder my target is to transfer all the data in s3 bucket lets achieve this doing by datasync. 
![image](https://user-images.githubusercontent.com/63963025/200155153-d014eea4-8050-4078-9d5d-7fa1b7b45e9a.png)

### Step 2:- Create a GCP HMAC key. 
### What is HMAC Key? 
An HMAC key is a type of credential and can be associated with a service account or a user account in Cloud Storage. You use an HMAC key to create signatures which are then included in requests to Cloud Storage. Signatures show that a given request is authorized by the user or service account.

Refer this document:- https://cloud.google.com/storage/docs/authentication/hmackeys

- Lets create HMAC Key go to IAM & Admin --> Service accounts. Create Service account. 
![image](https://user-images.githubusercontent.com/63963025/200159434-5aeb8c40-6799-4abb-89fd-a42d3c4f857c.png)

- Service-account name. </br>
![image](https://user-images.githubusercontent.com/63963025/200159474-42a2d9f2-7bbb-4f5e-b59f-e69639b2a57a.png)

- Grant permission service account <b>Storage HMAC Key Admin</b>. 
![image](https://user-images.githubusercontent.com/63963025/200159618-30b8ae74-25cf-433d-a867-11f8a956dfa6.png)

- All default create service account.

- We have Service account lets create HMAC key for bucket go to bucket setting. 
![image](https://user-images.githubusercontent.com/63963025/200159808-380d651c-1878-43e2-8a2a-95533b7315d2.png)

- Select the Interoperability tab. </br>
![image](https://user-images.githubusercontent.com/63963025/200159874-b304e1cd-0233-46a4-89dc-d92223af6189.png)

- Create a key for a service account. </br>
![image](https://user-images.githubusercontent.com/63963025/200159954-3b49813b-d315-47a7-ac11-c904a8384241.png)

- Select the service account you want the HMAC key to be associated with. </br>
![image](https://user-images.githubusercontent.com/63963025/200160029-93b97e44-bc80-4986-9e47-0059347dea1d.png)

- Copy the access key secret key <b> do not share access-key and secret-key to anyone </b>. 
![image](https://user-images.githubusercontent.com/63963025/200160166-c451723c-b0f7-4280-9a5d-d7582a38bba9.png)

- You can also go through this document given below:- https://cloud.google.com/storage/docs/authentication/managing-hmackeys#console 
- Keep the key at secret location we will use later. 
- The service account principal needs sufficient permission for the DataSync agent to connect and migrate the objects. You can assign a predefined role named Storage Object Viewer to the service account principal as a way to grant this permission.
- Now go to IAM & Admin section select IAM search your service account that you have created earlier add another role Storage Object Viewer.

![image](https://user-images.githubusercontent.com/63963025/200161234-fceecdb7-326a-4aca-b107-2b53d06f82eb.png)



