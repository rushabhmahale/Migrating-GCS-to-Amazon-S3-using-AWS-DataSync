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
![image](https://user-images.githubusercontent.com/63963025/200161376-4e8b2433-9d91-41dc-9f2b-92472449f022.png)

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
!![image](https://user-images.githubusercontent.com/63963025/200161285-fe2903ad-cf3d-42e9-90d7-7faf837de84b.png)


- Copy the access key secret key <b> do not share access-key and secret-key to anyone </b>. 
![image](https://user-images.githubusercontent.com/63963025/200161333-1a109be3-eaf1-447a-a5e0-7eb6aabd5f83.png)


- You can also go through this document given below:- https://cloud.google.com/storage/docs/authentication/managing-hmackeys#console 
- Keep the key at secret location we will use later. 
- The service account principal needs sufficient permission for the DataSync agent to connect and migrate the objects. You can assign a predefined role named Storage Object Viewer to the service account principal as a way to grant this permission.
- Now go to IAM & Admin section select IAM search your service account that you have created earlier add another role Storage Object Viewer.

![image](https://user-images.githubusercontent.com/63963025/200161234-fceecdb7-326a-4aca-b107-2b53d06f82eb.png)

- Now select IAM condition add.
![image](https://user-images.githubusercontent.com/63963025/200753281-83442c36-ac7c-4018-907a-af1dc8f90cfe.png)

Refer this document:- https://cloud.google.com/iam/docs/managing-conditional-role-bindings.

- The access of this role by adding a condition to only allow when the resource name starts with projects/_/buckets/gcs-bucket-9291 where the demo-customer-bucket is the name of the source bucket. Notice that the condition uses a starts with condition. This allows the permission to be granted for the bucket and the objects within the bucket using a single statement.

![image](https://user-images.githubusercontent.com/63963025/200893516-a557d0d6-2d64-4551-85db-76bc98d83c91.png)


Refer this document:- https://cloud.google.com/storage/docs/introduction#resources 

resource.name.startsWith(“projects/_/buckets/gcs-bucket-9291”)

- Now go to AWS S3 Create destination bucket where you will store all your gcs bucket data.

Refer this document:- https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html 

### Step 3:- Create Destination bucket in S3 AWS 
 - Once you create the destination bucket, obtain the bucket <b>Amazon Resource Name (ARN)</b> from the bucket’s Properties tab.
![image](https://user-images.githubusercontent.com/63963025/200758589-1f86b3a3-119a-43c1-b94e-85a5c296f7a5.png)


### Step 4 Create IAM Role in AWS and attach to s3 bucket 
- AWS DataSync needs to access the Amazon S3 bucket in order to transfer the data to the destination bucket. This requires DataSync to assume an IAM role with appropriate permission and trust relationship. 
- Go to AWS IAM role and create role.  
![image](https://user-images.githubusercontent.com/63963025/201480786-e50b18bb-809b-44f5-920a-529401327ccf.png)
- Select the AWS service. 
![image](https://user-images.githubusercontent.com/63963025/201480893-97f8292c-88cd-4038-85ec-a91bd6f82725.png)
- In use-case section select s3 next. 
![image](https://user-images.githubusercontent.com/63963025/201480932-b354dd89-5d37-4aa1-92d0-2d4d9e4e105d.png)
- Create policy. 
![image](https://user-images.githubusercontent.com/63963025/201480970-38791851-fd73-4e40-8465-289c68708260.png)
- Select json format. 
![image](https://user-images.githubusercontent.com/63963025/201481010-5fb4aee0-e019-47b5-bc1f-8c1dc103f27a.png)
- Add this json plolicy. 
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListBucket",
                "s3:ListBucketMultipartUploads"
            ],
            "Effect": "Allow",
            "Resource": "YourS3BucketArn"
        },
        {
            "Action": [
                "s3:AbortMultipartUpload",
                "s3:DeleteObject",
                "s3:GetObject",
                "s3:ListMultipartUploadParts",
                "s3:GetObjectTagging",
                "s3:PutObjectTagging",
                "s3:PutObject"
              ],
            "Effect": "Allow",
            "Resource": "YourS3BucketArn/*"
        }
    ]
}
```
![image](https://user-images.githubusercontent.com/63963025/201481190-07453049-0ea9-4718-8974-870073d75fa8.png)

Refer this document:- https://docs.aws.amazon.com/datasync/latest/userguide/using-identity-based-policies.html#customer-managed-policies

### Step 5 Create the VPC Endpoints 
- Create the VPC, subnet, route table, and security group based on the network requirements when using VPC endpoints.
![image](https://user-images.githubusercontent.com/63963025/201482787-e276d91a-ce24-4d58-8e98-2cf8024b63cc.png)

- Subnet 
![image](https://user-images.githubusercontent.com/63963025/201482812-cc017e26-f69c-4953-a3af-48eb3d66e4f6.png)

- Edit route table subnet associations 
![image](https://user-images.githubusercontent.com/63963025/201482902-be0ba184-d816-47bf-ba16-1addba9b083b.png)

- Internet Gateway
 ![image](https://user-images.githubusercontent.com/63963025/201482976-ed9d95d6-887d-4fbf-98c8-dc30bcc414df.png)

- Attach routes 0.0.0.0/0 for internet gateway 
![image](https://user-images.githubusercontent.com/63963025/201483045-b9c28a7f-a48c-46eb-87ab-66af79c59432.png)

- Lets create VPC Endpoint 
![image](https://user-images.githubusercontent.com/63963025/201483167-5d5f56a8-8dae-489d-8aa1-b15895bb27bc.png)

![image](https://user-images.githubusercontent.com/63963025/201483199-1d2f50ea-73d7-4fc3-ab6c-d14751bf7887.png)

![image](https://user-images.githubusercontent.com/63963025/201483213-92735f5a-8105-4dec-97da-fc7bea56053a.png)

![image](https://user-images.githubusercontent.com/63963025/201483230-3efe2532-fb16-4097-927b-0ffb897fb4d3.png)

Refer this document:- https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html

### Step 6 Deploy an Amazon EC2 DataSync agent

Refer this document:-  https://docs.aws.amazon.com/datasync/latest/userguide/deploy-agents.html#ec2-deploy-agent

### Step 7 Create DataSync locations
- Open the AWS DataSync console and choose Locations. Then select the Create Location.
![image](https://user-images.githubusercontent.com/63963025/201484279-09283047-c97b-47c2-b54b-fda3794a1d8f.png)

- 
