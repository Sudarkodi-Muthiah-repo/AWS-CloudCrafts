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
  
📄 **Step 4 Creating user groups**
* Go to IAM console.
* In the navigation pane, choose User groups and then choose Create group.
  
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/e1fa9aa0-a491-46ee-8507-75e5cff41498)

*For User group name, type the name of the group as s3-private-bucket-access.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/b7885fc3-5ef6-4993-81d7-dda9310f2a64)

* Choose Create group.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/23bfbb45-846d-45f6-a857-04e8611dba6c)

📄 Step 5 Creating IAM Policy and attaching the policy

* In the left navigation pane, click Policies.
* In the Policies section, click Create Policy.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/e7002667-f3c3-431e-b88d-156e3ccc6ea8)

* For Create policy (step 1), click the JSON tab.
* Select (highlight) and delete the existing statement.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/e8eabbb0-c463-4754-8f39-94404f995677)

* In the JSON tab terminal window, copy the policy from the following GitHub repo and paste it.
  
![](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/blob/main/AWS-Security/IAM_S3/lab_s3_policy.json)

![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/8ff76928-48c5-4b7e-aa05-a4b839a41a9c)

* Click Next.
* For Create policy (step 2), click Next: Review.
* For Create policy (step 3), for Name, type: lab_s3_policy
* For Description, type a description that explains the purpose of the policy, such as the Policy to grant private folder access to users.
* Click Create policy.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/b6033126-b2a5-4cff-bcc8-a8fd5f90a38a)

* In the success alert, review the message.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/eccc8139-6fd4-4292-ba01-1f1b8c5c158a)

* Go to User groups.
* In the User groups section, under Group name, click the s3-private-bucket-access group.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/9cd78269-3904-474b-8c07-3f2192c2ec03)

* Click the Users tab and click Add users.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/9c627852-3dde-43f3-a7cc-23853494504b)
* Under User name, choose the two checkboxes to select both users fnuser1 and hruser1, and then click Add users.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/50b575ab-885e-40b1-b748-ec10d7e3fcd3)
* On the Users tab, review to ensure that both users were successfully added to the group.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/955e7e11-ced5-4cd5-a2e9-885f60604ebe)
* Click the Permissions tab.
* In the Permissions policies section, click Add permissions to expand the dropdown menu. Choose Attach policies.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/fe73cece-dfad-490c-83d5-1b65881a242d)
* In the Other permission policies filter box, type: lab_s3_policy and press Enter.
* Under Policy name, choose the check box to select lab_s3_policy.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/0c28ffeb-74bc-4a9c-b70e-a68c469f98dd)
* Click Attach policies.
* In the success alert, review the message.
![image](https://github.com/Sudarkodi-Muthiah-repo/AWS-CloudCrafts/assets/101267167/fc536425-e260-40b6-b460-77ce2543a07b)






