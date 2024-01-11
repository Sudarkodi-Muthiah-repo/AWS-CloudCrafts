# Secure User-Specific Private Folders in Amazon S3 using IAM User Groups and Policies: A Step-by-Step Guide
## Use Case
* The company has a shared S3 bucket with dedicated folders for each user, named fnuser1, hruser1.
* The IAM policy is configured to allow users to upload or download files only from their respective folders and restrict access to other folders in the bucket.
* Additionally, one user has access only through the AWS Management Console, while the other user has access exclusively through the AWS Command Line Interface (CLI).
## Solution
To implement this use case, you would need to create IAM users for users and configure their corresponding policies accordingly. Here’s a high-level overview of the steps involved:

1. Create IAM Users and IAM User group: Create IAM users named **fnuser1** and **hruser1** in the AWS Management Console and add them into an IAM user group.

2. Create Folders: In the S3 bucket **corp-xxxxxxx**, create folders named fnuser1 and hruser1 to match the IAM usernames.

3. Create IAM Policy:

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
