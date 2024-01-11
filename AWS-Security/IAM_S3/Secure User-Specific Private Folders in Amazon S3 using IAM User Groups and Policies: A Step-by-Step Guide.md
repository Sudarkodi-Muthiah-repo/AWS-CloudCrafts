# Secure User-Specific Private Folders in Amazon S3 using IAM User Groups and Policies: A Step-by-Step Guide
## Use Case
* The company has a shared S3 bucket with dedicated folders for each user, named fnuser1, hruser1.
* The IAM policy is configured to allow users to upload or download files only from their respective folders and restrict access to other folders in the bucket.
* Additionally, one user has access only through the AWS Management Console, while the other user has access exclusively through the AWS Command Line Interface (CLI).
## Solution
To implement this use case, you would need to create IAM users for users and configure their corresponding policies accordingly. Here’s a high-level overview of the steps involved:

1. **Create IAM Users and IAM User group:** Create IAM users named **fnuser1** and **hruser1** in the AWS Management Console and add them into an IAM user group.

2. **Create Folders:** In the S3 bucket **corp-xxxxxxx**, create folders named fnuser1 and hruser1 to match the IAM usernames.

3. **Create IAM Policy:**

    * Instead of creating individual policies for each user, use policy variables to create a group policy that applies to multiple users and attach it to the IAM user group.
    * Policy variables, such as **${aws: username}**, allow one policy that can be used for many users. When the policy is evaluated, the variables are replaced with values that come from the context of the request.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor10",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": [
                "arn:aws:s3:::<YOUR_BUCKET_NAME>"
            ],
            "Condition": {
                "StringLike": {
                    "s3:prefix": [
                        "",
                        "${aws:username}*"
                    ]
                }
            }
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:DeleteObject*"
            ],
            "Resource": "arn:aws:s3:::<YOUR_BUCKET_NAME>/${aws:username}*"
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": "s3:GetBucketLocation",
            "Resource": "arn:aws:s3:::*"
        },
        {
            "Sid": "VisualEditor3",
            "Effect": "Allow",
            "Action": "s3:ListAllMyBuckets",
            "Resource": "*"
        }
    ]
}
```
      * In this policy, when a user makes a request to AWS, the requester’s name replaces the variable. For example, when fnuser1 makes a request, ${aws:username} resolves to fnuser1.
4. **Test Access:** Test the access for each user by attempting to upload or download files from their respective folders.

## Solution Architecture
![IAM_final](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/b92fa14a-46e3-48f1-9d86-38307f8862b5)

## Step-by-Step guide

📄 **Step 1 Create a bucket and folders for users**

In this step, we will create a bucket, add folders (fnuser1 and hruser1) to the bucket, and upload one or two sample documents in each folder.

* Create a bucket with name corp-<Any Random number>
* For Region, choose the AWS Region as us-east-1
* Leave remaining as default.

**Note:** For step-by-step instructions, see https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html


  
