# Setup Guide — AWS Mini Infrastructure Project

## 1. Prerequisites

Before starting the setup, make sure you have:

- An AWS account
- Access to the AWS Management Console
- An SSH key pair for EC2 access
- A computer with SSH client support
- Basic understanding of AWS services

## 2. Create the VPC

Create a VPC with the following configuration:

- **Name:** `Project6-VPC`
- **IPv4 CIDR:** `10.0.0.0/16`

The VPC provides the isolated network environment for the project.

## 3. Create the Internet Gateway

Create an Internet Gateway:

- **Name:** `Project6-IGW`

Attach the Internet Gateway to:

```text
Project6-VPC
```

The Internet Gateway provides internet connectivity for resources in the public subnet.

## 4. Create the Public Subnet

Create a subnet inside `Project6-VPC`:

- **Name:** `Project6-Public-Subnet`
- **CIDR:** `10.0.1.0/24`
- **Availability Zone:** Any available AZ in the selected region

This subnet is used to host the EC2 instance.

## 5. Create the Route Table

Create a route table:

- **Name:** `Project6-Public-RT`
- **VPC:** `Project6-VPC`

Add the following route:

```text
Destination: 0.0.0.0/0
Target: Project6-IGW
```

Associate the route table with:

```text
Project6-Public-Subnet
```

This makes the subnet a public subnet capable of accessing the internet through the Internet Gateway.

## 6. Create the Security Group

Create a Security Group:

- **Name:** `Project6-EC2-SG`
- **VPC:** `Project6-VPC`

Configure the inbound rule:

```text
Type: SSH
Port: 22
Source: My IP
```

Keep the default outbound rule that allows outbound traffic.

## 7. Launch the EC2 Instance

Launch an EC2 instance with:

- **Name:** `Project6-EC2`
- **AMI:** Ubuntu
- **Instance Type:** `t3.micro`
- **Key Pair:** `demo.pem`
- **VPC:** `Project6-VPC`
- **Subnet:** `Project6-Public-Subnet`
- **Auto-assign Public IP:** Enabled
- **Security Group:** `Project6-EC2-SG`

After launching the instance, wait until the instance reaches the running state.

## 8. Connect to EC2

Use SSH to connect to the Ubuntu instance.

Example:

```bash
ssh -i demo.pem ubuntu@<PUBLIC-IP>
```

Replace `<PUBLIC-IP>` with the public IPv4 address of the EC2 instance.

## 9. Verify Internet Connectivity

After connecting to EC2, run:

```bash
curl -I https://aws.amazon.com
```

A successful response should include:

```text
HTTP/2 200
```

This verifies internet connectivity from the EC2 instance.

## 10. Create the S3 Bucket

Create an S3 bucket in the same AWS Region used for the project.

Configure the bucket with:

- **Block Public Access:** Enabled
- **ACLs:** Disabled
- **Object Ownership:** Bucket owner enforced

Upload a sample object:

```text
project6-sample.txt
```

The bucket should remain private.

No direct EC2-to-S3 application integration is configured in this project.

## 11. Create the IAM User

Create an IAM user:

```text
Project6-User
```

Enable console access if required.

Attach the following permission:

```text
AmazonS3ReadOnlyAccess
```

This provides read-only access to S3 instead of unrestricted administrator access.

No access key was created for this IAM user in this project.

## 12. Configure EC2 IAM Role

The EC2 instance uses an IAM role for AWS CLI access.

The role used for this project is:

```text
Project4-EC2-ReadOnly-Role
```

The role has:

```text
AmazonEC2ReadOnlyAccess
```

attached.

This allows the EC2 instance to interact with AWS APIs using temporary credentials provided through the IAM role.

## 13. Verify IAM Role Access

From the EC2 instance, run:

```bash
aws sts get-caller-identity
```

The command should return information about the AWS identity associated with the EC2 instance.

This verifies that the EC2 instance can obtain AWS credentials through its attached IAM role.

## 14. Configure CloudWatch Monitoring

Open Amazon CloudWatch and create an alarm for the EC2 instance.

Select:

```text
Metric:
EC2 → Per-Instance Metrics → CPUUtilization
```

Use the following configuration:

- **Instance:** `Project6-EC2`
- **Metric:** `CPUUtilization`
- **Statistic:** Average
- **Period:** 5 minutes
- **Threshold:** Greater than 70%
- **Datapoints:** 1 out of 1
- **Alarm Name:** `Project6-EC2-CPU-Above-70`

## 15. Configure SNS Notifications

Create an SNS topic:

```text
project6-cloudwatch-alert
```

Configure an email subscription for the topic.

If AWS sends a subscription confirmation email, confirm the subscription from the email.

Use this SNS topic as the notification target for the CloudWatch alarm.

## 16. Final Architecture

The setup can be summarized as:

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
   +------> CloudWatch
   |           |
   |           v
   |      CPU > 70%
   |           |
   |           v
   |          SNS
   |           |
   |           v
   |      Email Notification
   |
   +------> IAM Role


Project6-User
      |
      v
S3 Read-Only Access
      |
      v
Private S3 Bucket
```

## 17. Setup Verification Checklist

Before considering the setup complete, verify:

- [ ] VPC `Project6-VPC` created
- [ ] Internet Gateway attached
- [ ] Public subnet created
- [ ] Route table associated with public subnet
- [ ] `0.0.0.0/0` route points to Internet Gateway
- [ ] Security Group allows SSH from My IP
- [ ] EC2 instance is running
- [ ] EC2 has a public IP
- [ ] SSH connection works
- [ ] Internet connectivity verified using `curl`
- [ ] Private S3 bucket created
- [ ] Sample object uploaded
- [ ] IAM user created
- [ ] S3 read-only permission assigned
- [ ] EC2 IAM role attached
- [ ] `aws sts get-caller-identity` works
- [ ] CloudWatch CPU alarm configured
- [ ] SNS topic configured
- [ ] Email subscription configured/confirmed if required

## 18. Important Notes

This setup is intended as a learning and demonstration environment.

The project uses a single public subnet and a single EC2 instance. Production environments may require additional security, high availability, private subnets, load balancing, Auto Scaling, database services, and other architecture components depending on the workload requirements.
