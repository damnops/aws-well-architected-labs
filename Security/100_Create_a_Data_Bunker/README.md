# Level 100: Create a Data Bunker Account

## Overview
In this lab we will create a secure data bunker. A data bunker is a secure account which will hold important security data in a secure location. Ensure that only members of your security team have access to this account. In this lab we will create a new security account, create a secure S3 bucket in that account and then turn on CloudTrail for our organisation tp send these logs to th bucket in the secure data account. You may want to also think about what other data you need in there such as secure backups.

![Data bunker account structure](Images/data-bunker-architecture.png)

## Prerequisites
* An AWS account that you have administrative access which is the root account for an AWS Organization.

NOTE: You will be billed for the AWS CloudTrail logs and Amazon S3 storage setup as part of this lab. See [AWS CloudTrail Pricing](https://aws.amazon.com/cloudtrail/pricing/) and [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/) for further details.

## Detailed Instructions

### 1. Create a Security account from the master account
1. Login to your master account
2. Navigate to AWS Organizations and select **Create Account**. Include a cross account access role - we will modify this later to remove unnecessary access.
3. Navigate to **Settings** and take a note of your Organization ID
### 2. Create the bucket for CloudTrail logs
1. Navigate to S3
2. Press **Create Bucket**
3. Enter a *name* for your bucket, make note of it and click **Next**
4. Under configuration options *enable versioning* and *enable object lock*. This will prevent our logs from being deleted. Press **Next**
5. Do not modify any permissions - press **Next**
6. Press **Create Bucket**
7. Press the bucket we just create and navigate to the **Properties** tab
8. Under **Object Lock**, *enable compliance mode* and set a *retention period*. The length of the retention period will depend on your organisational requirements. If you are enabling this just for baseline security start with 31 days to keep one month of logs
9. Under the **Permissions** tab, replace the Bucket Policy with the following, replacing [bucket] and [organization id]. Pres **Save**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AWSCloudTrailAclCheck20150319",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudtrail.amazonaws.com"
            },
            "Action": "s3:GetBucketAcl",
            "Resource": "arn:aws:s3:::[bucket]"
        },
        {
            "Sid": "AWSCloudTrailWrite20150319",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudtrail.amazonaws.com"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::[bucket]/AWSLogs/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "bucket-owner-full-control"
                }
            }
        },
        {
            "Sid": "AWSCloudTrailWrite20150319",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudtrail.amazonaws.com"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::[bucket]/AWSLogs/[organization id]/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "bucket-owner-full-control"
                }
            }
        }
    ]
}
```
10. (Optional) Next we will add a lifecycle policy to clean up old logs. Navigate to **Management**
11. (Optional) Add a lifecycle rule named *Delete old logs*, press **Next**
12. (Optional) Add a transition rule for both the current and previous versions to move to Glacier after 32 days. Press **Next**
13. (Optional) Select the current and previous versions and set them to delete after *365* days
### 3. Correct the IAM permissions so only cross account access is read-only
1. Navigate to **IAM** and select **Roles**
2. Select the *OrganizationAccountAccessRole*
3. Press **Attach Policy** and attach the AWS managed *ReadOnlyAccess* Policy
4. Navigate back to the *OrganizationAccountAccessRole* and press the **X** to remove the *AdministratorAccess* policy
### 4. Turn on CloudTrail from the root account
1. Switch back to the root account
2. Navigate to **CloudTrail**
3. Select **Trails** from the menu on the left
4. Press **Create Trail**
5. Name the trail *Organization Trail*
5. Select *Yes* next to *Apply trail to my organization*
6. Under *Storage location*, select *No* for *Create new S3 bucket* and instead select the bucket created in the security account created previously

### Verification
1. Switch back to the Security account
2. Navigate to the S3 bucket previously created
3. (Optional) You can start to [explore the logs using CloudTrail](https://docs.aws.amazon.com/athena/latest/ug/cloudtrail-logs.html)

***

## License
Licensed under the Apache 2.0 and MITnoAttr License. 

Copyright 2019 Amazon.com, Inc. or its affiliates. All Rights Reserved.

Licensed under the Apache License, Version 2.0 (the "License"). You may not use this file except in compliance with the License. A copy of the License is located at

    http://aws.amazon.com/apache2.0/

or in the "license" file accompanying this file. This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
