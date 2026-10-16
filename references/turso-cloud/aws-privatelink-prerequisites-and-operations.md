# AWS privatelink prerequisites and operations

[Context index](aws-private-endpoint-workflows.md)

Create an AWS VPC endpoint for Turso database access through AWS PrivateLink, so traffic stays on AWS's private network.

### Prerequisites

- A VPC already present in the target AWS region
- Subnets in supported availability zones; check the region-specific requirements below
- IAM permissions for creating VPC endpoints and updating security groups
- Active Turso account with databases already configured

### Quickstart

-  us-east-1
-  us-east-2
-  us-west-2
-  eu-west-1
-  ap-south-1
-  ap-northeast-1

### Important Notes

- Traffic remains within AWS’s private network
- Standard AWS VPC endpoint pricing applies
- Applications may reach the endpoint from any AZ in your VPC
- Service names and supported availability zones vary by region
