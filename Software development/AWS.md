How to choose AWS region ? 
1. Pricing
2. Compliance with government related to data.
3. Proximity to customers
4. Available Service

Each region has three to six availability zones. Normally it is three zones. Like for Sydney, it is( ap-southeast-2).   ap-southeast-2a, ap-southeast-2b, ap-south-2c. Each zone are separated from each other so that natural disaster can't harm them. And they are connected with high-bandwidth and ultra-low latency network. (==We need to see this video again ==)
### IAM
```
{
  "Version": "2012-10-17", // Specifies the version of the policy language. Use "2012-10-17" for current features.
  "Statement": [ // Array of permission statements. Each statement defines specific access rules.
    {
      "Sid": "AllowS3ReadWriteFromSpecificIP", // (Optional) Statement ID for easy identification of the rule.
      "Effect": "Allow", // Grants access. Can be "Allow" or "Deny".
      "Action": [ // Specifies the actions allowed or denied by this statement.
        "s3:PutObject", // Allows uploading objects to an S3 bucket.
        "s3:GetObject", // Allows downloading objects from an S3 bucket.
        "s3:ListBucket" // Allows listing objects in an S3 bucket.
      ],
      "Resource": [ // Defines the resource(s) the actions apply to. Supports ARNs or wildcards.
        "arn:aws:s3:::my-bucket-name", // The bucket itself (required for certain actions like "ListBucket").
        "arn:aws:s3:::my-bucket-name/*" // All objects within the bucket.
      ],
      "Condition": { // Optional: Adds restrictions based on specific conditions.
        "IpAddress": { // Restricts access based on the requester's IP address.
          "aws:SourceIp": "192.168.1.0/24" // Only allows access from this IP range.
        },
        "DateGreaterThan": { // Restricts access to requests made after a certain date.
          "aws:CurrentTime": "2024-01-01T00:00:00Z" // ISO 8601 format.
        }
      }
    },
    {
      "Sid": "DenyUnencryptedUploads", // Another statement with its unique ID.
      "Effect": "Deny", // Denies access explicitly, even if other statements allow it.
      "Action": "s3:PutObject", // Applies only to upload (PutObject) actions.
      "Resource": "arn:aws:s3:::my-bucket-name/*", // All objects in the specified bucket.
      "Condition": { // Restricts based on a condition.
        "StringNotEqualsIfExists": { // Applies only if the specified key exists.
          "s3:x-amz-server-side-encryption": "aws:kms" // Deny uploads without KMS encryption.
        }
      }
    },
    {
      "Sid": "AllowDynamoDBFullAccessForTaggedResources", // Example for DynamoDB.
      "Effect": "Allow",
      "Action": "dynamodb:*", // Grants all actions for DynamoDB.
      "Resource": "arn:aws:dynamodb:us-east-1:123456789012:table/my-table", // Specific DynamoDB table.
      "Condition": { // Restricts based on resource tags.
        "StringEquals": {
          "aws:RequestTag/Environment": "Production" // Only applies to resources tagged with "Environment: Production".
        }
      }
    },
    {
      "Sid": "RestrictEC2AccessByTime", // Example for EC2 access with time-based conditions.
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances", // Allows starting EC2 instances.
        "ec2:StopInstances" // Allows stopping EC2 instances.
      ],
      "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*", // Applies to all EC2 instances in the account.
      "Condition": {
        "DateGreaterThan": {
          "aws:CurrentTime": "2023-12-31T12:00:00Z" // Allows actions only after this time.
        },
        "DateLessThan": {
          "aws:CurrentTime": "2024-01-31T12:00:00Z" // Allows actions only before this time.
        }
      }
    }
  ]
}

```

Using AWS ECS()
1. We will push the docker to registry to ECR i.e. Elastic container registry
2. And then we will use ECS (Elastic Container Service), we will scale up, deploy and manage our container.

At first go to ECR, then push the docker app to there and copy the URI from there.
After that, go to ECS and and create a cluster. Cluster can have multiple services.![[Pasted image 20241221144109.png]]Tasks
They know how to run the configuration file related to images. They are linked to images and service-api is concerned with running that tasks. We tell service to run the tasks. And it will scale up, scale down and re run if something goes wrong. We can also have multiple tasks.
![[Pasted image 20241221144446.png]]

#### Task
#### HealthCheck in task
We have `/health` route in our app. everytime the task is setup. We can see that 

#### Service in Cluster
We can create service in cluster to connect task with required cluster. Under family there is option to connect tasks. There is ==desired tasks== also. Lets say we put 2 there. That means there will be two tasks and they will be handled using load balancer. That means two tasks will run parallely and load balancer will balance the load. And there is Load Balancing options also. It is different from the load balancer described above. Select Application Load Balancer. And there is service autoscalling also. There is option for minimum and maximum. That means in low traffic it will be minimum and in high traffic it will be maximum. ![[Pasted image 20241221151647.png]]
It means if average CPU Utilization is more than 70, run another container and now if you go in tasks, there will be two tasks why ? cuz min tasks was 2.

### Remove tasks
First you need to remove the tasks. However you don't need to delete tasks, rather go to update cluster and reduce the desired no of tasks to 0. now delete clusters, and then ECR. You can delete tasks also. infact, delete tasks so that you can sure.

### S3
Every bucket needs to be unique worldwide. Why ? cuz S3 make an link for the object in its bucket. you can see at Object uri, that there is that bucket name is link. For that object to be publicly accessible. we should set the policy also. And we can host static sites there also. It is amazing. There is options for that in S3 buckets and we can do it. For that static site, it will be 
`our url/that bucket name/ that index.html`.

You can have folder also, and the name of that folder is called key. and for private bucket, whenever we want to upload something and see that media from bucket using open button(not URL directly), the token and signature are automatically generated which exist for some time like 1 min, 2 min. For private bucket, we can't use private media directly from URL. that token and signature are generated from our IAM users account ID. 

Presigned URL only works for private buckets as public buckets don't need it.

And we don't need to know that much about puttingObject and gettingObject. It can be searched in Internet.

In permission, there is tags, it is not that complicated. It just provide meta data like maintainer("Ramesh"), user("bindesh"). For Bucket Versioning, Let's say we upload same video with same name, then S3 will save both versions. It will increase bills. but sometime worth it.  For AWS Cloud trail, we can track of activities of IAM users. Like let's say our server was hacked then we can see because of whom, our server was hacked. It tracks at when does this user logged in or who changed what. For Event notification, the things is that it allows for some lambda function to be runned if certain event occur. It is used widely. Like if user upload something, we can tell user in email that his file has been uploaded.

In Access Control List,we can set the permissions of who can do what in this buckets.

In Managements, we can manage Life cycle rules. Like if the user don't request for certain media for certain days, then we can downgrade that to HDD. Normally that policy is 10 days. For 20 days downgrade to Glacier. We can delete after certain days also. This saves cost. In replication rules, we can copy the data from one bucket to another. Lets say we have buckets in Mumbai and data are stored there. we have option to replicate that to bucket from US so that Americans can get their data from there which reduces latency and time also. For Inventory Configuration, We can produce daily logs in json. Like what happened today, who put Object and who Get it there.

### Costing
There is no costing for data IN, but there is for Storing and OUT.

### EC2
