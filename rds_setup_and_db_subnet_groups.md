# Task: RDS Setup and DB Subnet Groups

## Objective
Launch an Amazon RDS database instance (PostgreSQL) inside private subnets using a dedicated AWS RDS DB Subnet Group across multiple Availability Zones to ensure network isolation and high availability.

---

## 1. What is an RDS DB Subnet Group?
An RDS DB Subnet Group is a collection of subnets (typically private) within a VPC that you designate for your RDS database instances.
- **High Availability Requirement:** RDS requires subnets in at least two distinct Availability Zones (AZs) within the region to support Multi-AZ failover and maintenance operations.
- **Security Isolation:** Placing database instances in private subnets prevents direct exposure to the public internet, restricting database access exclusively to internal application tiers.

---

## 2. Step-by-Step Implementation

### Step 1: Subnet Identification
Select two isolated private subnets in different Availability Zones within the target VPC:
- `private-subnet-1` in `us-east-1a` (CIDR: `10.0.21.0/24`)
- `private-subnet-2` in `us-east-1b` (CIDR: `10.0.22.0/24`)

*Route tables for these subnets contain only local routes (`10.0.0.0/16`) and have no routes to an Internet Gateway (`0.0.0.0/0`).*

---

### Step 2: Create DB Subnet Group via AWS CLI

```bash
aws rds create-db-subnet-group \
    --db-subnet-group-name "rds-private-subnet-group" \
    --db-subnet-group-description "Private DB Subnet Group for PostgreSQL instances" \
    --subnet-ids "subnet-0123456789abcdef0" "subnet-0abcdef0123456789" \
    --tags Key=Environment,Value=Production
