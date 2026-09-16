# AWS Resource Cleanup — Project 6

## 1. Purpose

This document explains how to safely remove the AWS resources created for this project after completing the learning and documentation work.

Cleaning up unused AWS resources helps avoid unnecessary AWS charges.

## 2. Cleanup Order

The recommended cleanup order is:

```text
CloudWatch Alarm
      ↓
SNS Topic
      ↓
EC2 Instance
      ↓
S3 Bucket
      ↓
IAM User
      ↓
IAM Role
      ↓
Security Group
      ↓
Subnet
      ↓
Route Table
      ↓
Internet Gateway
      ↓
VPC
```

## 3. Terminate EC2 Instance

Open:

```text
AWS Console → EC2 → Instances
```

Select:

```text
Project6-EC2
```

Terminate the instance.

Before termination, make sure any required screenshots or documentation have already been saved.

## 4. Delete CloudWatch Alarm

Open:

```text
AWS Console → CloudWatch → Alarms
```

Select:

```text
Project6-EC2-CPU-Above-70
```

Delete the alarm after the EC2 instance is no longer required.

## 5. Delete SNS Resources

Open:

```text
AWS Console → SNS → Topics
```

Select:

```text
project6-cloudwatch-alert
```

Delete the SNS topic if it is no longer required.

If an email subscription exists, remove it as part of the cleanup.

## 6. Delete S3 Bucket

Open:

```text
AWS Console → S3
```

Select the project bucket.

Before deleting the bucket:

- Remove `project6-sample.txt`
- Confirm that no required objects remain
- Delete the bucket

The bucket should only be deleted after all required project screenshots and documentation have been completed.

## 7. Delete IAM User

Open:

```text
AWS Console → IAM → Users
```

Select:

```text
Project6-User
```

Remove the user if it is no longer required.

Before deletion, review the user's permissions and access configuration.

## 8. Delete IAM Role

The EC2 instance used the existing role:

```text
Project4-EC2-ReadOnly-Role
```

Do not delete this role while it is still being used by another project or AWS resource.

If the role is no longer required anywhere, it can be removed after confirming that no resources depend on it.

## 9. Delete Security Group

Open:

```text
AWS Console → EC2 → Security Groups
```

Select:

```text
Project6-EC2-SG
```

The Security Group can be deleted after the EC2 instance has been terminated and no other resources are using it.

## 10. Delete Public Subnet

Open:

```text
AWS Console → VPC → Subnets
```

Select:

```text
Project6-Public-Subnet
```

Delete the subnet after the EC2 instance has been terminated.

## 11. Delete Route Table

Open:

```text
AWS Console → VPC → Route Tables
```

Select:

```text
Project6-Public-RT
```

Remove the subnet association if required and delete the route table.

## 12. Detach and Delete Internet Gateway

Open:

```text
AWS Console → VPC → Internet Gateways
```

Select:

```text
Project6-IGW
```

First detach it from:

```text
Project6-VPC
```

Then delete the Internet Gateway.

## 13. Delete VPC

After all dependent resources have been removed, delete:

```text
Project6-VPC
```

The VPC cannot be deleted while dependent resources still exist.

## 14. Final Resource Checklist

Before finishing the cleanup, verify that the following project resources are no longer running or unnecessarily retained:

- [ ] `Project6-EC2` terminated
- [ ] `Project6-EC2-CPU-Above-70` deleted
- [ ] `project6-cloudwatch-alert` deleted if no longer required
- [ ] Project S3 bucket deleted
- [ ] `Project6-User` deleted if no longer required
- [ ] `Project4-EC2-ReadOnly-Role` reviewed before deletion
- [ ] `Project6-EC2-SG` deleted
- [ ] `Project6-Public-Subnet` deleted
- [ ] `Project6-Public-RT` deleted
- [ ] `Project6-IGW` detached and deleted
- [ ] `Project6-VPC` deleted

## 15. Billing Verification

After cleanup, check the AWS Billing dashboard to make sure there are no unexpected resources still generating charges.

Pay particular attention to:

- EC2 instances
- EBS volumes
- Elastic IP addresses
- NAT Gateways
- S3 storage
- CloudWatch resources
- Other resources created outside this project

## 16. Important Note

Not every resource should be deleted automatically.

For example, the IAM role:

```text
Project4-EC2-ReadOnly-Role
```

was originally created for another project and reused here.

Therefore, it should only be deleted if it is no longer required by any other AWS resource or project.
