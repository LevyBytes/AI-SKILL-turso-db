# Private Endpoints

> Turso Cloud reference. Original prose; identifiers preserved verbatim.

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
1

Create VPC Endpoint

Open **VPC Dashboard**, then **Endpoints**, then **Create endpoint**, and set these options:

Basic Settings

- **Name**: Use a clear name, for example `turso-database-endpoint`
- **Service category**: Choose "Other endpoint services"
- **Service name**: Use `com.amazonaws.vpce.us-east-1.vpce-svc-0d9cc6f6a688ce8a5`
- Click **Verify service**

Network Configuration

- Select your **VPC**
- Enable **DNS name**
- Set DNS record type to **IPv4**
- Pick subnets in supported AZs (`use1-az4` and/or `use1-az6`)
- Set the security groups, then click **Create endpoint**
2

Configure Security Groups

Adjust security groups so communication works correctly:**VPC Endpoint Security Group:**
- Inbound: Permit HTTPS (port 443) traffic from your application security groups
**Application Security Groups:**
- Outbound: Permit HTTPS (port 443) traffic to the VPC endpoint security group
3

Update Application Connections

Use the regional Turso URL format for connections:

```
curl https://<database_name>-<organization_slug>.aws-us-east-1.turso.io

```

4

Verify Setup

From inside your VPC, verify the VPC endpoint configuration:

```
# Test connectivity
curl -v https://<database_name>-<organization_slug>.aws-us-east-1.turso.io

# Verify private routing (should show traffic staying within AWS network)
traceroute -T <database_name>-<organization_slug>.aws-us-east-1.turso.io

```

1

Create VPC Endpoint

Open **VPC Dashboard**, then **Endpoints**, then **Create endpoint**, and set these options:

Basic Settings

- **Name**: Use a clear name, for example `turso-database-endpoint`
- **Service category**: Choose "Other endpoint services"
- **Service name**: Enter `com.amazonaws.vpce.us-east-2.vpce-svc-08cc04ea0962d5036`
- Click **Verify service**

Network Configuration

- Select your **VPC**
- Enable **DNS name**
- Set DNS record type to **IPv4**
- Pick subnets in supported AZs (`use2-az1` and/or `use2-az2`)
- Set the security groups, then click **Create endpoint**
2

Configure Security Groups

Adjust security groups so communication works correctly:**VPC Endpoint Security Group:**
- Inbound: Permit HTTPS (port 443) traffic from your application security groups
**Application Security Groups:**
- Outbound: Permit HTTPS (port 443) traffic to the VPC endpoint security group
3

Update Application Connections

Use the regional Turso URL format for connections:

```
curl https://<database_name>-<organization_slug>.aws-us-east-2.turso.io

```

4

Verify Setup

From inside your VPC, verify the VPC endpoint configuration:

```
# Test connectivity
curl -v https://<database_name>-<organization_slug>.aws-us-east-2.turso.io

# Verify private routing (should show traffic staying within AWS network)
traceroute -T <database_name>-<organization_slug>.aws-us-east-2.turso.io

```

1

Create VPC Endpoint

Open **VPC Dashboard**, then **Endpoints**, then **Create endpoint**, and set these options:

Basic Settings

- **Name**: Use a clear name, for example `turso-database-endpoint`
- **Service category**: Choose "Other endpoint services"
- **Service name**: Enter `com.amazonaws.vpce.us-west-2.vpce-svc-0372e98cdacb325b7`
- Click **Verify service**

Network Configuration

- Select your **VPC**
- Enable **DNS name**
- Set DNS record type to **IPv4**
- Pick subnets in supported AZs (`usw2-az1` and/or `usw2-az3`)
- Set the security groups, then click **Create endpoint**
2

Configure Security Groups

Adjust security groups so communication works correctly:**VPC Endpoint Security Group:**
- Inbound: Permit HTTPS (port 443) traffic from your application security groups
**Application Security Groups:**
- Outbound: Permit HTTPS (port 443) traffic to the VPC endpoint security group
3

Update Application Connections

Use the regional Turso URL format for connections:

```
curl https://<database_name>-<organization_slug>.aws-us-west-2.turso.io

```

4

Verify Setup

From inside your VPC, verify the VPC endpoint configuration:

```
# Test connectivity
curl -v https://<database_name>-<organization_slug>.aws-us-west-2.turso.io

# Verify private routing (should show traffic staying within AWS network)
traceroute -T <database_name>-<organization_slug>.aws-us-west-2.turso.io

```

1

Create VPC Endpoint

Open **VPC Dashboard**, then **Endpoints**, then **Create endpoint**, and set these options:

Basic Settings

- **Name**: Use a clear name, for example `turso-database-endpoint`
- **Service category**: Choose "Other endpoint services"
- **Service name**: Use `com.amazonaws.vpce.eu-west-1.vpce-svc-09559a4fb467b07fd`
- Click **Verify service**

Network Configuration

- Select your **VPC**
- Enable **DNS name**
- Set DNS record type to **IPv4**
- Pick subnets in supported AZs (`euw1-az1` and/or `euw1-az3`)
- Set the security groups, then click **Create endpoint**
2

Configure Security Groups

Adjust security groups so communication works correctly:**VPC Endpoint Security Group:**
- Inbound: Permit HTTPS (port 443) traffic from your application security groups
**Application Security Groups:**
- Outbound: Permit HTTPS (port 443) traffic to the VPC endpoint security group
3

Update Application Connections

Use the regional Turso URL format for connections:

```
curl https://<database_name>-<organization_slug>.aws-eu-west-1.turso.io

```

4

Verify Setup

From inside your VPC, verify the VPC endpoint configuration:

```
# Test connectivity
curl -v https://<database_name>-<organization_slug>.aws-eu-west-1.turso.io

# Verify private routing (should show traffic staying within AWS network)
traceroute -T <database_name>-<organization_slug>.aws-eu-west-1.turso.io

```

1

Create VPC Endpoint

Open **VPC Dashboard**, then **Endpoints**, then **Create endpoint**, and set these options:

Basic Settings

- **Name**: Use a clear name, for example `turso-database-endpoint`
- **Service category**: Choose "Other endpoint services"
- **Service name**: Use `com.amazonaws.vpce.ap-south-1.vpce-svc-0c1607d1026a49817`
- Click **Verify service**

Network Configuration

- Select your **VPC**
- Enable **DNS name**
- Set DNS record type to **IPv4**
- Pick subnets in supported AZs (`aps1-az1` and/or `aps1-az3`)
- Set the security groups, then click **Create endpoint**
2

Configure Security Groups

Adjust security groups so communication works correctly:**VPC Endpoint Security Group:**
- Inbound: Permit HTTPS (port 443) traffic from your application security groups
**Application Security Groups:**
- Outbound: Permit HTTPS (port 443) traffic to the VPC endpoint security group
3

Update Application Connections

Use the regional Turso URL format for connections:

```
curl https://<database_name>-<organization_slug>.aws-ap-south-1.turso.io

```

4

Verify Setup

From inside your VPC, verify the VPC endpoint configuration:

```
# Test connectivity
curl -v https://<database_name>-<organization_slug>.aws-ap-south-1.turso.io

# Verify private routing (should show traffic staying within AWS network)
traceroute -T <database_name>-<organization_slug>.aws-ap-south-1.turso.io

```

1

Create VPC Endpoint

Open **VPC Dashboard**, then **Endpoints**, then **Create endpoint**, and set these options:

Basic Settings

- **Name**: Use a clear name, for example `turso-database-endpoint`
- **Service category**: Choose "Other endpoint services"
- **Service name**: Enter `com.amazonaws.vpce.ap-northeast-1.vpce-svc-070f604683ada07db`
- Click **Verify service**

Network Configuration

- Select your **VPC**
- Enable **DNS name**
- Set DNS record type to **IPv4**
- Pick subnets in supported AZs (`apne1-az4` and/or `apne1-az1`)
- Set the security groups, then click **Create endpoint**
2

Configure Security Groups

Adjust security groups so communication works correctly:**VPC Endpoint Security Group:**
- Inbound: Permit HTTPS (port 443) traffic from your application security groups
**Application Security Groups:**
- Outbound: Permit HTTPS (port 443) traffic to the VPC endpoint security group
3

Update Application Connections

Use the regional Turso URL format for connections:

```
curl https://<database_name>-<organization_slug>.aws-ap-northeast-1.turso.io

```

4

Verify Setup

From inside your VPC, verify the VPC endpoint configuration:

```
# Test connectivity
curl -v https://<database_name>-<organization_slug>.aws-ap-northeast-1.turso.io

# Verify private routing (should show traffic staying within AWS network)
traceroute -T <database_name>-<organization_slug>.aws-ap-northeast-1.turso.io

```

### Important Notes

- Traffic remains within AWS’s private network
- Standard AWS VPC endpoint pricing applies
- Applications may reach the endpoint from any AZ in your VPC
- Service names and supported availability zones vary by region
