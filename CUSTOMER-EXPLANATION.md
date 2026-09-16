# Customer Explanation — AWS Mini Infrastructure Project

## 1. Customer Requirement

The requirement was to build a small AWS infrastructure environment that demonstrates:

- Secure network connectivity
- A compute environment
- Private object storage
- Controlled user access
- Infrastructure monitoring
- Alert notifications

The setup was built manually in AWS to demonstrate the complete flow and understand how the individual AWS services work together.

## 2. Proposed Solution

The solution uses the following AWS services:

- Amazon VPC
- Public Subnet
- Internet Gateway
- Route Table
- Security Group
- Amazon EC2
- Amazon S3
- AWS IAM
- Amazon CloudWatch
- Amazon SNS

The infrastructure is organized so that each service has a specific responsibility.

## 3. Network Setup

A dedicated VPC named `Project6-VPC` was created with the CIDR range:

```text
10.0.0.0/16
```

Inside the VPC, a public subnet was created:

```text
Project6-Public-Subnet
10.0.1.0/24
```

An Internet Gateway named `Project6-IGW` was attached to the VPC.

A route table named `Project6-Public-RT` was configured with:

```text
0.0.0.0/0 → Project6-IGW
```

The public subnet was explicitly associated with this route table.

This configuration allows resources in the public subnet to communicate with the internet through the Internet Gateway.

## 4. Compute Environment

An Ubuntu EC2 instance named `Project6-EC2` was deployed inside the public subnet.

The instance configuration includes:

- Ubuntu operating system
- `t3.micro` instance type
- Public IP enabled
- `Project6-Public-Subnet`
- `Project6-EC2-SG` security group

The EC2 instance acts as the compute/server component of the infrastructure.

## 5. Security Configuration

A Security Group named `Project6-EC2-SG` was attached to the EC2 instance.

The inbound configuration allows:

```text
SSH — Port 22 — My IP
```

Outbound traffic uses the default allow configuration.

Restricting SSH access to the required IP helps reduce unnecessary exposure of the management port.

## 6. Storage

Amazon S3 was configured as a separate private object storage service.

The bucket configuration includes:

- Block Public Access enabled
- ACLs disabled
- Bucket owner enforced
- Sample object: `project6-sample.txt`

The bucket was intentionally kept private.

No direct EC2-to-S3 application integration was configured as part of this project.

## 7. Identity and Access Management

An IAM user named `Project6-User` was created.

The user was assigned:

```text
AmazonS3ReadOnlyAccess
```

This demonstrates the use of permission-specific access instead of unrestricted administrator permissions.

The EC2 instance also used an IAM role for AWS CLI access.

The EC2 identity was verified using:

```bash
aws sts get-caller-identity
```

This allows AWS CLI operations to use the assigned IAM role without storing long-lived access keys directly on the EC2 instance.

## 8. Monitoring

Amazon CloudWatch was configured to monitor the EC2 instance.

The following metric was used:

```text
CPUUtilization
```

The alarm configuration was:

- Metric: `CPUUtilization`
- Statistic: Average
- Period: 5 minutes
- Threshold: Greater than 70%
- Datapoints: 1 out of 1
- Alarm: `Project6-EC2-CPU-Above-70`

This provides a basic mechanism for detecting increased CPU utilization on the EC2 instance.

## 9. Alerting

Amazon SNS was configured to provide email notifications for the CloudWatch alarm.

The SNS topic is:

```text
project6-cloudwatch-alert
```

The monitoring flow is:

```text
EC2
   |
   v
CloudWatch CPU Metric
   |
   v
CloudWatch Alarm
   |
   v
SNS Topic
   |
   v
Email Notification
```

When the configured alarm condition is reached, the CloudWatch alarm can publish a notification to the SNS topic.

## 10. Internet Connectivity Verification

The EC2 instance was accessed through SSH.

Internet connectivity from the instance was verified using:

```bash
curl -I https://aws.amazon.com
```

The response returned:

```text
HTTP/2 200
```

This confirmed that the EC2 instance could communicate with the internet through the configured VPC networking.

## 11. Customer-Facing Architecture Flow

The overall infrastructure can be explained as:

```text
                    AWS CLOUD
                        |
                 Project6-VPC
                  10.0.0.0/16
                        |
                Public Subnet
                 10.0.1.0/24
                        |
                     EC2
                 Project6-EC2
                        |
                   CloudWatch
                        |
                  CPU > 70%
                        |
                      SNS
                        |
                Email Notification


      Project6-User
            |
            v
     S3 Read-Only Access
            |
            v
      Private S3 Bucket
```

## 12. Security Approach

The project follows a basic security-focused approach:

- SSH access is restricted to My IP.
- S3 Block Public Access is enabled.
- S3 ACLs are disabled.
- S3 access is provided through a read-only IAM policy.
- EC2 uses an IAM role for AWS CLI authentication.
- Long-lived AWS access keys were not stored on the EC2 instance.

These configurations demonstrate basic AWS security and access-control practices.

## 13. Current Scope

This is a small infrastructure demonstration designed to show the fundamental interaction between AWS networking, compute, storage, identity, and monitoring services.

The current implementation contains:

- One VPC
- One public subnet
- One EC2 instance
- One private S3 bucket
- One IAM user
- IAM role-based EC2 access
- CloudWatch CPU monitoring
- SNS notification configuration

The project does not currently include:

- Private subnets
- NAT Gateway
- Application Load Balancer
- Auto Scaling
- RDS/database layer
- VPC endpoints
- Multi-AZ architecture

These components can be introduced when a more production-oriented architecture is required.

## 14. Possible Future Improvements

The infrastructure can be extended based on future requirements.

Possible improvements include:

- Adding private subnets for backend resources
- Deploying databases in private subnets
- Adding an Application Load Balancer
- Configuring Auto Scaling
- Using VPC endpoints for private AWS service access
- Adding additional CloudWatch metrics and dashboards
- Introducing more granular IAM policies
- Designing a multi-AZ architecture

## 15. Final Customer Outcome

The completed setup provides a basic AWS infrastructure environment with:

- Controlled network access
- EC2-based compute
- Private object storage
- IAM-based access control
- EC2 monitoring
- SNS-based alerting

The project demonstrates how these AWS services can be combined to create a small, manageable, and security-conscious cloud environment.
