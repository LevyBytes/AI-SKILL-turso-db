# AWS privatelink eu west 1

[Context index](aws-private-endpoint-workflows.md)

### Step 1: Create VPC Endpoint

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
### Step 2: Configure Security Groups

Adjust security groups so communication works correctly:**VPC Endpoint Security Group:**
- Inbound: Permit HTTPS (port 443) traffic from your application security groups
**Application Security Groups:**
- Outbound: Permit HTTPS (port 443) traffic to the VPC endpoint security group
### Step 3: Update Application Connections

Use the regional Turso URL format for connections:

```
curl https://<database_name>-<organization_slug>.aws-eu-west-1.turso.io

```

### Step 4: Verify Setup

From inside your VPC, verify the VPC endpoint configuration:

```
# Test connectivity
curl -v https://<database_name>-<organization_slug>.aws-eu-west-1.turso.io

# Verify private routing (should show traffic staying within AWS network)
traceroute -T <database_name>-<organization_slug>.aws-eu-west-1.turso.io

```
