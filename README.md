# AWS Mini Infrastructure Project

A manually built AWS mini-infrastructure setup demonstrating networking, compute, storage, access control, and monitoring using core AWS services.

## Project Overview

This project was built manually on AWS to understand how multiple cloud services work together in a basic infrastructure environment.

The setup includes:

- Amazon VPC
- Internet Gateway
- Public Subnet
- Route Table
- Security Group
- Amazon EC2
- Amazon S3
- AWS IAM
- Amazon CloudWatch
- Amazon SNS

## Objective

The objective of this project was to build a small AWS environment manually and document the complete architecture from a 
customer perspective.

The project demonstrates:

- Basic AWS networking
- EC2 deployment inside a VPC
- S3 object storage
- IAM-based access control
- CloudWatch monitoring
- SNS-based alerting
- Basic infrastructure verification

## Architecture

![AWS Architecture](architecture.png)

### Architecture Flow

```text
Internet
   |
   v
Internet Gateway
   |
   v
Project6-VPC
   |
   v
Project6-Public-Subnet
   |
   v
Project6-EC2
   |
   v
Internet


S3
 |
 +--> Object Storage

IAM
 |
 +--> Access Control

CloudWatch
 |
 +--> EC2 CPU Monitoring
 |
 v
SNS
 |
 +--> Email Notification
```

## AWS Services Used

### 1. Amazon VPC

Created a dedicated VPC:

- **Name:** `Project6-VPC`
- **CIDR:** `10.0.0.0/16`

The VPC provides the network environment for the project.

### 2. Internet Gateway

Created and attached an Internet Gateway:

- **Name:** `Project6-IGW`

It provides internet connectivity for the public subnet through the configured route.

### 3. Public Subnet

Created inside the VPC:

- **Name:** `Project6-Public-Subnet`
- **CIDR:** `10.0.1.0/24`

The subnet was explicitly associated with the public route table.

### 4. Route Table

Created a dedicated route table:

- **Name:** `Project6-Public-RT`

Configured route:

- **Destination:** `0.0.0.0/0`
- **Target:** `Project6-IGW`

### 5. Security Group

Created a security group for the EC2 instance:

- **Name:** `Project6-EC2-SG`

Inbound access:

- **SSH:** Port `22` → **My IP**

Default outbound access was retained.

### 6. Amazon EC2

Created an EC2 instance:

- **Name:** `Project6-EC2`
- **OS:** Ubuntu
- **Instance Type:** `t3.micro`
- **Public IP:** Enabled
- **Subnet:** `Project6-Public-Subnet`
- **Security Group:** `Project6-EC2-SG`

The EC2 instance acts as the compute/server layer.

### 7. Amazon S3

Created an S3 bucket for object storage.

Configuration:

- **Block Public Access:** Enabled
- **ACLs:** Disabled / Bucket owner enforced
- **Sample Object:** `project6-sample.txt`

The S3 bucket was kept private.

### 8. IAM

Created an IAM user:

- **Username:** `Project6-User`
- **Permission:** `AmazonS3ReadOnlyAccess`

The project uses controlled permissions instead of unrestricted administrator access.

### 9. Amazon CloudWatch

Configured CloudWatch monitoring for the EC2 instance.

Alarm configuration:

- **Metric:** `CPUUtilization`
- **Statistic:** `Average`
- **Period:** `5 minutes`
- **Threshold:** Greater than `70%`
- **Datapoints:** `1 out of 1`
- **Alarm Name:** `Project6-EC2-CPU-Above-70`

### 10. Amazon SNS

Configured an SNS topic for CloudWatch notifications:

- **Topic:** `project6-cloudwatch-alert`
- **Notification:** Email

An email notification was configured for the CloudWatch alarm.

## Security Considerations

The following security practices were implemented:

- EC2 was deployed inside a dedicated VPC.
- SSH access was restricted to **My IP**.
- S3 public access was blocked.
- IAM permissions were limited to the required access.
- AWS resources were configured manually with controlled network access.

## Verification

The EC2 instance was successfully connected through SSH.

Internet connectivity was verified using:

```bash
curl -I https://aws.amazon.com


The response returned:
HTTP/2 200
This verified that the EC2 instance had working internet connectivity through the configured VPC networking.
