# Setting up AWS Credential for Data Project If Target Location Already Exists

This guide only needs to be followed if you are sharing AWS accounts and want different S3 buckets for target locations with it's own unique credential. The generated template from TMC won't work in Cloud Formation because resources already exist, so you need to massage the generated templates.

## Generate Template

The template you use for cloud formation needs to be changed to only generate the bucket (We will be adding the bucket to the existing Policy and Data Role). Take note of the generated policy and role as you need those in subsequent steps.

The new cloud formation template should look like this:

```
{
    "AWSTemplateFormatVersion": "2010-09-09",
    "Resources": {
        "DataProtectionBucket": {
            "Type": "AWS::S3::Bucket",
            "Properties": {
                "BucketName": "vmware-tmc-dp-xxxxx",
                "BucketEncryption": {
                    "ServerSideEncryptionConfiguration": [
                        {
                            "ServerSideEncryptionByDefault": {
                                "SSEAlgorithm": "AES256"
                            }
                        }
                    ]
                }
            },
            "DeletionPolicy": "Retain"
        },
    }
}

```

## Edit VMwareTMCProviderCredentialMgr

Add in the template's generated assumeRolePolicy Statement to the Role.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:sts::xxxx:assumed-role/account-manager/kiam-kiam"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "sts:ExternalId": "a0a4d65b-xxxx"
                }
            }
        },

        !!!! ---> ADD THIS SECTION FROM THE GENERATED TEMPLATE

        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:sts::xxxx:assumed-role/account-manager/kiam-kiam"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "sts:ExternalId": "f457b1dd-xxxxxx"
                }
            }
        }
    ]
}
```

## Edit dataprotection.tmc.cloud.vmware.com Policy
Edit the dataprotection.tmc.cloud.vmware.com policy to add in additional permissions for the new bucket (the DataProtectionPolicy statements prior removed from template)

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "ec2:DeleteSnapshot",
                "ec2:CreateTags",
                "ec2:DescribeVolumes",
                "ec2:CreateSnapshot",
                "ec2:DescribeSnapshots",
                "ec2:CreateVolume"
            ],
            "Resource": "*",
            "Effect": "Allow",
            "Sid": "SnapshotVolumes"
        },
        {
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:AbortMultipartUpload",
                "s3:DeleteObject",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": "arn:aws:s3:::vmware-tmc-111/*",
            "Effect": "Allow",
            "Sid": "Bucket"
        },
        {
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::vmware-tmc-dp-111",
            "Effect": "Allow",
            "Sid": "BucketList"
        },

        !!!! --- ADD THIS SECTION FROM THE GENERATED TEMPLATE

        {
            "Sid": "Bucketjc",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:AbortMultipartUpload",
                "s3:DeleteObject",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": "arn:aws:s3:::vmware-tmc-dp-xxx/*"
        },
        {
            "Sid": "BucketListjc",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::vmware-tmc-dp-xxx"
        }
    ]
}
```

## Create target location in TMC with success!

Now you should be able to create the Target Location in TMC with Success!


