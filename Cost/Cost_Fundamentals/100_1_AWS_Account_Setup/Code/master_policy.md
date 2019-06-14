NOTE: Policy should be removed from the user after this exercise is complete.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "organizations:InviteAccountToOrganization",
                "organizations:ListRoots",
                "aws-portal:ModifyAccount",
                "s3:ListBucketVersions",
                "organizations:DescribeAccount",
                "s3:CreateBucket",
                "s3:ListBucket",
                "s3:GetBucketPolicy",
                "organizations:ListChildren",
                "aws-portal:ModifyBilling",
                "organizations:ListCreateAccountStatus",
                "organizations:DescribeOrganization",
                "organizations:EnableAllFeatures",
                "aws-portal:ViewBilling",
                "organizations:DescribeHandshake",
                "s3:PutBucketVersioning",
                "organizations:DescribeCreateAccountStatus",
                "organizations:CreateOrganization",
                "s3:GetBucketPolicyStatus",
                "s3:GetBucketPublicAccessBlock",
                "s3:PutBucketPublicAccessBlock",
                "aws-portal:ViewAccount",
                "s3:GetBucketVersioning",
                "organizations:ListAWSServiceAccessForOrganization",
                "s3:DeleteBucketPolicy",
                "organizations:ListHandshakesForOrganization",
                "organizations:ListAccounts",
                "iam:CreateServiceLinkedRole",
                "s3:ListAllMyBuckets",
                "s3:PutBucketPolicy",
                "s3:GetBucketLocation"
            ],
            "Resource": "*"
        }
    ]
}
```
