vpc_subnets_and_route_tables.md
# AWS Networking Basics: VPC, Subnets and Route Tables

## Objective
Build a custom Amazon Virtual Private Cloud (VPC) containing one public subnet, one private subnet, an Internet Gateway, and dedicated route tables.

---

## 1. Architecture Overview

- **VPC CIDR:** `10.0.0.0/16`
- **Public Subnet:** `10.0.1.0/24` (Direct routing to Internet Gateway)
- **Private Subnet:** `10.0.2.0/24` (Internal traffic only, no public route)
- **Internet Gateway (IGW):** Attached to the VPC to enable outbound/inbound internet connectivity for the public subnet.

---

## 2. Step-by-Step Implementation (AWS CLI)

### Step 1: Create the VPC
```bash
VPC_ID=$(aws ec2 create-vpc \
  --cidr-block 10.0.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=Custom-VPC}]' \
  --query 'Vpc.VpcId' \
  --output text)

# Enable DNS support and hostnames
aws ec2 modify-vpc-attribute --vpc-id $VPC_ID --enable-dns-hostnames "{\"Value\":true}"
aws ec2 modify-vpc-attribute --vpc-id $VPC_ID --enable-dns-support "{\"Value\":true}"

# Public Subnet (e.g., in us-east-1a)
PUBLIC_SUBNET_ID=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.1.0/24 \
  --availability-zone us-east-1a \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=Public-Subnet}]' \
  --query 'Subnet.SubnetId' \
  --output text)

# Enable auto-assign public IPv4 on launch for the public subnet
aws ec2 modify-subnet-attribute --subnet-id $PUBLIC_SUBNET_ID --map-public-ip-on-launch

# Private Subnet (e.g., in us-east-1a)
PRIVATE_SUBNET_ID=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.2.0/24 \
  --availability-zone us-east-1a \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=Private-Subnet}]' \
  --query 'Subnet.SubnetId' \
  --output text)

  IGW_ID=$(aws ec2 create-internet-gateway \
  --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=Custom-IGW}]' \
  --query 'InternetGateway.InternetGatewayId' \
  --output text)

aws ec2 attach-internet-gateway --vpc-id $VPC_ID --internet-gateway-id$IGW_ID

PUBLIC_RT_ID=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=Public-Route-Table}]' \
  --query 'RouteTable.RouteTableId' \
  --output text)

# Add route target to the Internet Gateway
aws ec2 create-route \
  --route-table-id $PUBLIC_RT_ID \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id $IGW_ID

# Associate Public Subnet
aws ec2 associate-route-table --subnet-id $PUBLIC_SUBNET_ID --route-table-id$PUBLIC_RT_ID

PRIVATE_RT_ID=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=Private-Route-Table}]' \
  --query 'RouteTable.RouteTableId' \
  --output text)

# Associate Private Subnet (keeps local-only default routing)
aws ec2 associate-route-table --subnet-id $PRIVATE_SUBNET_ID --route-table-id$PRIVATE_RT_ID

# Verify route table association for subnets
aws ec2 describe-route-tables --route-table-ids $PUBLIC_RT_ID$PRIVATE_RT_ID
