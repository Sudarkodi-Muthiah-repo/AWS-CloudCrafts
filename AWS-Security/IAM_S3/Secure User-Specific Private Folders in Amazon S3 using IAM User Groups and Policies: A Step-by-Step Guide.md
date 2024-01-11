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



   * In this policy, when a user makes a request to AWS, the requester’s name replaces the variable. For example, when fnuser1 makes a request,
      **${aws: username}** resolves to **fnuser1**.
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
After the bucket has been created,
* In the Buckets list, choose the name of the bucket that you created.
* Choose Create folder.
* Enter a name for the folder fnuser1. Name of the folder is same as the name of the user. Then choose Create folder.
* Repeat the steps 5 and 6 for hruser1.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/fc038353-4b9a-4ebf-a04a-34083d96d447)

**Note:** For step-by-step instructions to create a folder, see [Organizing objects in the Amazon S3 console by using folders](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-folders.html)https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-folders.html in the Amazon Simple Storage Service User Guide.
📄 **Step 2 Uploading objects**
* On the Objects tab, review to ensure that two folders appear, one for each user defined in the system.
* Click Buckets to return to the list of buckets.
* Click the folder hruser1/ and click upload. Choose file mygoals.txt to upload.
* After uploading this file, go back to the folder fnuser1/ and click upload. Upload the file todo_list.txt.

**Note:** For step-by-step instructions, see https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html

📄 **Step 3 Creating IAM users**
Use the IAM console to add two IAM users, fnuser1 and hruser1, to your AWS account. Also create a user group named s3-private-bucket-access, and then add both users to the group.
* On the Console Home page, select the IAM service.
* In the navigation pane, select Users and then select Create user.

![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/76aefea9-c99f-455f-8c32-f29396aa91f2)

* On the Specify user details page, under User details, in User name, enter the name for the new user as fnuser1.
* Select Next.  

![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/94bf2466-ec68-454d-8208-3c0e73183fc6)

* On the Set permissions page, specify how you want to assign permissions for this user. Select Add user to group
* Click next
* Review and create user.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/9668ed2e-75e3-46de-bfd0-63fe992b3e40)
Create another user hruser1.
* On the Specify user details page, under User details, in User name, enter the name for the new user as hruser1.
* Select Provide user access to the — AWS Management Console optional This produces AWS Management Console sign-in credentials for the hruser1.
* Select I want to create an IAM user and continue following this procedure.

![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/4d7e6dab-80a3-4d6a-9330-2f288ca1ab0f)
* For Console password,select Custom password
* Enter a password LabPassword123! and Click Next
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/5e9c2ef9-9aa1-4340-9d9f-9fcb479cd2e2)
* On the Set permissions page,Select Add user to group.
* Click Next
* Review and create user.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/24ffaaa5-2fd0-434c-9ee5-afe79d91f933)
* Copy and save the user name and password for later use.
  
