# AWS Mini Infrastructure Project

A manually built AWS mini-infrastructure setup demonstrating networking, compute, storage, access control, monitoring, and alerting using core AWS services.

## Project Overview

This project was built manually on AWS to understand how different cloud services work together to create a basic infrastructure environment.

The project includes:

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

The objective of this project was to build and document a small AWS infrastructure environment from a customer perspective.

The project demonstrates:

- AWS VPC networking
- EC2 deployment inside a custom VPC
- Internet connectivity through an Internet Gateway
- Security Group based access control
- Private S3 object storage
- IAM-based permissions
- IAM role-based AWS CLI access
- CloudWatch CPU monitoring
- SNS-based notification configuration
- Infrastructure verification and troubleshooting

---

# Architecture

## Architecture Diagram

![AWS Architecture](architecture.png)

## Architecture Flow

The overall architecture consists of a custom VPC containing a public subnet and an EC2 instance. CloudWatch monitors the EC2 instance and can send notifications through SNS. S3 is configured separately as private object storage, with read-only access provided to the IAM user.

## Internet Connectivity

The EC2 internet connectivity path is:

Internet → Internet Gateway → Project6-VPC → Project6-Public-Subnet → Project6-EC2

The public subnet uses the following route:

0.0.0.0/0 → Project6-IGW

---

# AWS Services Used

## 1. Amazon VPC

A dedicated VPC was created:

- Name: `Project6-VPC`
- CIDR: `10.0.0.0/16`

The VPC provides the isolated network environment for the project.

## 2. Internet Gateway

An Internet Gateway was created and attached to the VPC:

- Name: `Project6-IGW`

It provides internet connectivity for resources in the public subnet through the configured route.

## 3. Public Subnet

A public subnet was created inside the VPC:

- Name: `Project6-Public-Subnet`
- CIDR: `10.0.1.0/24`

The subnet was explicitly associated with the project route table.

## 4. Route Table

A dedicated route table was created:

- Name: `Project6-Public-RT`

Configured route:

- Destination: `0.0.0.0/0`
- Target: `Project6-IGW`

This route allows resources in the public subnet to reach the internet through the Internet Gateway.

## 5. Security Group

A Security Group was created for the EC2 instance:

- Name: `Project6-EC2-SG`

Inbound access:

- SSH: Port `22`
- Source: My IP

Default outbound access was retained.

## 6. Amazon EC2

An Ubuntu EC2 instance was created:

- Name: `Project6-EC2`
- Operating System: Ubuntu
- Instance Type: `t3.micro`
- Public IP: Enabled
- Subnet: `Project6-Public-Subnet`
- Security Group: `Project6-EC2-SG`

The EC2 instance acts as the compute/server layer.

## 7. Amazon S3

A private S3 bucket was created for object storage.

Configuration:

- Block Public Access: Enabled
- ACLs: Disabled / Bucket owner enforced
- Sample Object: `project6-sample.txt`

The bucket was intentionally kept private.

No direct EC2-to-S3 application integration was configured in this project.

## 8. AWS IAM

An IAM user was created:

- Username: `Project6-User`
- Permission: `AmazonS3ReadOnlyAccess`

This demonstrates permission-specific access instead of unrestricted administrator access.

The EC2 instance also used an IAM role for AWS CLI access.

The role used was:

- `Project4-EC2-ReadOnly-Role`
- Policy: `AmazonEC2ReadOnlyAccess`

## 9. Amazon CloudWatch

CloudWatch was configured to monitor the EC2 instance using the `CPUUtilization` metric.

Alarm configuration:

- Metric: `CPUUtilization`
- Statistic: `Average`
- Period: `5 minutes`
- Threshold: Greater than `70%`
- Datapoints: `1 out of 1`
- Alarm Name: `Project6-EC2-CPU-Above-70`

## 10. Amazon SNS

An SNS topic was configured for CloudWatch notifications:

- Topic: `project6-cloudwatch-alert`
- Notification: Email

The SNS topic is used as the notification destination for the CloudWatch alarm.

---

# Security Considerations

The project follows basic AWS security practices:

- EC2 was deployed inside a dedicated VPC.
- SSH access was restricted to My IP.
- S3 Block Public Access was enabled.
- S3 ACLs were disabled.
- IAM permissions were limited to the required access.
- EC2 used an IAM role for AWS CLI authentication.
- Long-lived AWS access keys were not stored on the EC2 instance.

---

# Verification

The infrastructure was verified through AWS Console checks and EC2 command-line tests.

## SSH Connectivity

The EC2 instance was successfully accessed through SSH.

## Internet Connectivity

Internet connectivity from the EC2 instance was verified using:

`curl -I https://aws.amazon.com`

The response returned:

`HTTP/2 200`

This confirmed that the EC2 instance could communicate with the internet through the configured VPC networking.

## IAM Role Verification

The EC2 IAM role was verified using:

`aws sts get-caller-identity`

This confirmed that the EC2 instance could obtain AWS identity information through its attached IAM role.

## CloudWatch and SNS

The CloudWatch CPU alarm and SNS notification configuration were created and verified.

The CPU threshold was not intentionally forced during testing, so an actual threshold-triggered email is not claimed as a tested result.

---

# Project Structure

The repository contains:

- `README.md`
- `ARCHITECTURE.md`
- `CUSTOMER-EXPLANATION.md`
- `SETUP.md`
- `TESTING.md`
- `TROUBLESHOOTING.md`
- `CLEANUP.md`
- `COMMANDS.md`
- `VERIFICATION.md`
- `architecture.png`
- `screenshots/`

The screenshots directory contains AWS console screenshots documenting the major infrastructure components.

---

# What I Learned

Through this project, I gained practical experience with:

- Creating and configuring a custom AWS VPC
- Understanding public subnet routing
- Configuring Internet Gateway connectivity
- Deploying EC2 inside a custom network
- Restricting SSH access using Security Groups
- Configuring private S3 storage
- Applying IAM permissions
- Using IAM roles for EC2-based AWS CLI access
- Monitoring EC2 using CloudWatch
- Configuring SNS notifications
- Verifying AWS infrastructure through CLI commands
- Troubleshooting AWS networking and access issues
- Documenting cloud architecture from a customer perspective

---

# Project Scope

This project is intentionally designed as a small infrastructure demonstration.

The current architecture contains:

- One VPC
- One public subnet
- One EC2 instance
- One private S3 bucket
- IAM user and IAM role-based access
- CloudWatch CPU monitoring
- SNS notification configuration

The project does not currently include:

- Private subnets
- NAT Gateway
- Load Balancer
- Auto Scaling
- RDS
- VPC Endpoints
- Multi-AZ architecture

These components can be added in a future production-oriented version depending on application requirements.

---

# Documentation

Detailed documentation is available in the following files:

- [Architecture](ARCHITECTURE.md)
- [Customer Explanation](CUSTOMER-EXPLANATION.md)
- [Setup Guide](SETUP.md)
- [Testing & Verification](TESTING.md)
- [Troubleshooting](TROUBLESHOOTING.md)
- [Cleanup Guide](CLEANUP.md)

---

# Final Outcome

The project demonstrates a manually configured AWS mini-infrastructure environment combining:

- Networking
- Compute
- Storage
- Identity & Access
- Monitoring
- Alerting

The main goal was to understand how individual AWS services work together and to document the infrastructure in a clear, customer-oriented manner.
