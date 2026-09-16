# Troubleshooting — AWS Mini Infrastructure Project

## 1. Overview

During the setup of the AWS infrastructure, different configuration areas were checked to identify and resolve common connectivity and access issues.

This document records the main troubleshooting checks relevant to the project.

## 2. EC2 SSH Connection Issues

### Problem

SSH connection to the EC2 instance may fail if the Security Group, key pair, public IP, or network configuration is incorrect.

### Checks Performed

The following configuration was verified:

- EC2 instance was in the running state.
- EC2 was deployed in `Project6-Public-Subnet`.
- A public IP was assigned to the EC2 instance.
- Security Group `Project6-EC2-SG` was attached.
- SSH port `22` was allowed.
- SSH source was restricted to My IP.
- The correct EC2 key pair was used.

### Resolution

After verifying the above configuration, SSH access to the EC2 instance was successfully established.

## 3. EC2 Internet Connectivity Issues

### Problem

An EC2 instance may not be able to access the internet if the subnet routing or Internet Gateway configuration is incorrect.

### Checks Performed

The following components were verified:

- Internet Gateway `Project6-IGW` was attached to `Project6-VPC`.
- Public subnet `Project6-Public-Subnet` was associated with `Project6-Public-RT`.
- Route table contained:

```text
0.0.0.0/0 → Project6-IGW
```

- EC2 had a public IP address.
- Security Group outbound traffic was allowed.

### Verification

Internet connectivity was tested from the EC2 instance using:

```bash
curl -I https://aws.amazon.com
```

The response returned:

```text
HTTP/2 200
```

This confirmed successful internet connectivity from the EC2 instance.

## 4. Route Table Troubleshooting

### Problem

If an EC2 instance in a public subnet cannot access the internet, an incorrect or missing default route can be one possible cause.

### Configuration Checked

The route table was verified as:

```text
Route Table: Project6-Public-RT
Destination: 0.0.0.0/0
Target: Project6-IGW
```

The route table was also explicitly associated with:

```text
Project6-Public-Subnet
```

### Result

The routing configuration allowed the EC2 instance to communicate with the internet.

## 5. Security Group Troubleshooting

### Problem

SSH access can fail if port 22 is not allowed or the source IP is incorrect.

### Configuration Checked

The Security Group was verified as:

```text
Security Group: Project6-EC2-SG
Protocol: TCP
Port: 22
Source: My IP
```

### Result

SSH connectivity was successfully established after verifying the Security Group configuration.

## 6. IAM Role Troubleshooting

### Problem

AWS CLI commands running on EC2 may fail if the instance does not have a suitable IAM role attached.

### Verification Command

The following command was used:

```bash
aws sts get-caller-identity
```

### Result

The command returned AWS identity information, confirming that the EC2 instance could obtain credentials through its attached IAM role.

The role used was:

```text
Project4-EC2-ReadOnly-Role
```

with:

```text
AmazonEC2ReadOnlyAccess
```

## 7. AWS CLI Access

If the AWS CLI is unable to communicate with AWS services, the following areas should be checked:

- AWS CLI installation
- IAM role attachment
- EC2 network connectivity
- IAM permissions
- AWS Region configuration

For this project, the AWS identity was successfully verified using:

```bash
aws sts get-caller-identity
```

## 8. S3 Access Troubleshooting

### Problem

S3 access can be affected by bucket permissions, Block Public Access settings, ACL configuration, or IAM permissions.

### Configuration Checked

The project bucket was configured with:

- Block Public Access enabled
- ACLs disabled
- Bucket owner enforced
- Private bucket configuration

The IAM user `Project6-User` was assigned:

```text
AmazonS3ReadOnlyAccess
```

### Important Note

The project did not configure direct EC2-to-S3 application integration.

Therefore, EC2-to-S3 application connectivity was not part of the testing scope.

## 9. CloudWatch Alarm Troubleshooting

### Problem

A CloudWatch alarm may not change state if the configured metric, threshold, period, or instance selection is incorrect.

### Configuration Checked

The alarm was configured with:

```text
Alarm:
Project6-EC2-CPU-Above-70

Metric:
CPUUtilization

Statistic:
Average

Period:
5 minutes

Threshold:
Greater than 70%

Datapoints:
1 out of 1
```

### Result

The alarm configuration was verified in CloudWatch.

The CPU threshold was not intentionally forced during testing, so an actual threshold-triggered alarm email was not treated as a verified test result.

## 10. SNS Notification Troubleshooting

### Problem

CloudWatch notifications may not be delivered if the SNS topic or email subscription is not configured correctly.

### Configuration Checked

SNS topic:

```text
project6-cloudwatch-alert
```

The topic was configured as the notification destination for the CloudWatch alarm.

An email subscription was configured for notifications.

### Important Note

SNS notification configuration was completed, but an actual CPU-triggered email should only be considered verified if the alarm was intentionally triggered and the email was received.

## 11. General Troubleshooting Checklist

When troubleshooting this infrastructure, check the following in order:

### Network

- [ ] Correct VPC
- [ ] Correct subnet
- [ ] Internet Gateway attached
- [ ] Default route configured
- [ ] Route table associated with subnet

### EC2

- [ ] Instance is running
- [ ] Public IP is available
- [ ] Correct subnet selected
- [ ] Correct Security Group attached
- [ ] Correct key pair used

### Security

- [ ] SSH port 22 allowed
- [ ] SSH source restricted appropriately
- [ ] S3 Block Public Access enabled
- [ ] IAM permissions match the required access

### IAM

- [ ] EC2 IAM role attached
- [ ] Required IAM policy attached
- [ ] `aws sts get-caller-identity` works

### Monitoring

- [ ] Correct EC2 metric selected
- [ ] Correct threshold configured
- [ ] Correct alarm configured
- [ ] SNS topic selected as notification destination

## 12. Key Troubleshooting Lessons

The project demonstrated that AWS connectivity problems can often be traced by checking the infrastructure layer by layer:

```text
VPC
 ↓
Subnet
 ↓
Route Table
 ↓
Internet Gateway
 ↓
Security Group
 ↓
EC2
 ↓
Application / CLI
```

For monitoring:

```text
EC2 Metric
 ↓
CloudWatch Alarm
 ↓
SNS Topic
 ↓
Email Notification
```

Checking each layer individually makes it easier to identify configuration problems and verify the infrastructure systematically.
