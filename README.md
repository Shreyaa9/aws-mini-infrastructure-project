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

The objective of this project was to build a small AWS environment manually and document the complete architecture from a customer perspective.

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
