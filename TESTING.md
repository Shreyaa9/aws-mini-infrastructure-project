# Testing & Verification — AWS Mini Infrastructure Project

## 1. Testing Overview

After creating the AWS infrastructure, different components were verified individually to ensure that the configured resources were working as expected.

The testing covered:

- VPC networking
- EC2 connectivity
- Internet access
- S3 configuration
- IAM access
- CloudWatch monitoring
- SNS notification configuration

## 2. VPC Verification

The VPC configuration was verified using the AWS Management Console.

Expected configuration:

- **VPC:** `Project6-VPC`
- **CIDR:** `10.0.0.0/16`

The VPC was confirmed to contain the project subnet and networking resources.

## 3. Subnet Verification

The public subnet was checked to confirm:

- **Name:** `Project6-Public-Subnet`
- **CIDR:** `10.0.1.0/24`
- Correct VPC association
- Correct route table association

The subnet was associated with `Project6-Public-RT`.

## 4. Internet Gateway Verification

The Internet Gateway was verified to ensure that it was attached to:

```text
Project6-VPC
```

The Internet Gateway was also referenced by the public route:

```text
0.0.0.0/0 → Project6-IGW
```

## 5. Route Table Verification

The route table was checked for the following configuration:

```text
Route Table: Project6-Public-RT
Destination: 0.0.0.0/0
Target: Project6-IGW
```

The public subnet was confirmed as an associated subnet.

## 6. Security Group Verification

The EC2 Security Group was checked to confirm that SSH access was configured.

Expected inbound rule:

```text
SSH
Port: 22
Source: My IP
```

No HTTP inbound rule was configured because HTTP access was not required for this project.

## 7. EC2 Verification

The EC2 instance was verified with the following configuration:

- **Instance:** `Project6-EC2`
- **OS:** Ubuntu
- **Instance Type:** `t3.micro`
- **Subnet:** `Project6-Public-Subnet`
- **Public IP:** Enabled
- **Security Group:** `Project6-EC2-SG`

The instance successfully reached the running state.

## 8. SSH Connectivity Test

SSH connectivity to the EC2 instance was successfully verified.

The instance was accessed using the configured EC2 key pair and SSH port 22.

Successful SSH access confirmed that:

- The EC2 instance was reachable
- The Security Group allowed SSH from the configured IP
- The correct key pair was being used

## 9. Internet Connectivity Test

Internet connectivity from the EC2 instance was tested using:

```bash
curl -I https://aws.amazon.com
```

The response returned:

```text
HTTP/2 200
```

This confirmed that the EC2 instance could successfully communicate with the internet through the configured VPC networking.

## 10. IAM Role Verification

The EC2 instance's IAM role access was verified using:

```bash
aws sts get-caller-identity
```

The command successfully returned AWS identity information.

This confirmed that the EC2 instance was able to obtain AWS credentials through its attached IAM role.

The IAM role used was:

```text
Project4-EC2-ReadOnly-Role
```

with:

```text
AmazonEC2ReadOnlyAccess
```

## 11. S3 Verification

The S3 bucket was checked to confirm that:

- Block Public Access was enabled
- ACLs were disabled
- Bucket owner enforced was enabled
- The bucket remained private
- `project6-sample.txt` was present

The project did not configure direct application-level EC2-to-S3 integration.

## 12. IAM User Verification

The IAM user was checked to confirm:

```text
User: Project6-User
Permission: AmazonS3ReadOnlyAccess
```

The purpose of this configuration was to demonstrate restricted access to S3 instead of unrestricted administrative permissions.

No access key was created for this IAM user as part of the project.

## 13. CloudWatch Alarm Verification

The CloudWatch alarm configuration was checked for:

- **Alarm:** `Project6-EC2-CPU-Above-70`
- **Metric:** `CPUUtilization`
- **Statistic:** Average
- **Period:** 5 minutes
- **Threshold:** Greater than 70%
- **Datapoints:** 1 out of 1

The alarm was configured to monitor CPU utilization on `Project6-EC2`.

## 14. SNS Verification

The SNS notification configuration was checked for:

```text
Topic:
project6-cloudwatch-alert
```

The topic was configured as the notification destination for the CloudWatch alarm.

Email notification was configured through the SNS topic.

The actual CPU threshold condition was not intentionally forced during testing, so an actual alarm-trigger email should not be considered a tested result unless separately verified.

## 15. Overall Verification

The main successfully verified components were:

| Component | Verification |
|---|---|
| VPC | Configuration verified |
| Public Subnet | Configuration and association verified |
| Internet Gateway | Attached and referenced by route |
| Route Table | `0.0.0.0/0 → Project6-IGW` verified |
| Security Group | SSH from My IP verified |
| EC2 | Running and accessible |
| SSH | Successfully connected |
| Internet Access | `curl` returned `HTTP/2 200` |
| IAM Role | `aws sts get-caller-identity` successful |
| S3 | Private configuration verified |
| IAM User | S3 read-only permission configured |
| CloudWatch | CPU alarm configured |
| SNS | Notification topic configured |

## 16. Final Testing Result

The infrastructure components were configured and individually verified through the AWS Management Console and EC2 command-line tests.

The most important connectivity test was:

```bash
curl -I https://aws.amazon.com
```

with the successful response:

```text
HTTP/2 200
```

The IAM role was also verified using:

```bash
aws sts get-caller-identity
```

These tests confirmed the core network connectivity and IAM role-based AWS access of the project.
