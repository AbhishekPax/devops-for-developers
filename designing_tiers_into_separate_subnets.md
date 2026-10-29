# Task: Designing Tiers into Separate Subnets

## Objective
Design and lay out a production-ready VPC architecture with isolated subnets across multiple Availability Zones for a 3-tier application: Web Tier (Public), Application Tier (Private), and Database Tier (Isolated Private).

---

## VPC Architecture & CIDR Breakdown
- **VPC CIDR Block:** `10.0.0.0/16` (65,536 total IP addresses)
- **Region:** Dual-AZ deployment (`us-east-1a` and `us-east-1b`) for high availability and fault tolerance.

### Subnet Allocation Table

| Tier | Subnet Name | AZ | CIDR Block | Route Table Target | Accessibility |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Web Tier** | `public-web-subnet-1a` | us-east-1a | `10.0.1.0/24` | Internet Gateway (`igw-xxxx`) | Public (Direct Internet Inbound/Outbound) |
| **Web Tier** | `public-web-subnet-1b` | us-east-1b | `10.0.2.0/24` | Internet Gateway (`igw-xxxx`) | Public (Direct Internet Inbound/Outbound) |
| **App Tier** | `private-app-subnet-1a` | us-east-1a | `10.0.11.0/24` | NAT Gateway (`nat-xxxx-1a`) | Private (Outbound Internet via NAT only) |
| **App Tier** | `private-app-subnet-1b` | us-east-1b | `10.0.12.0/24` | NAT Gateway (`nat-xxxx-1b`) | Private (Outbound Internet via NAT only) |
| **DB Tier** | `isolated-db-subnet-1a` | us-east-1a | `10.0.21.0/24` | Local VPC Only (`10.0.0.0/16`) | Isolated (No Internet access) |
| **DB Tier** | `isolated-db-subnet-1b` | us-east-1b | `10.0.22.0/24` | Local VPC Only (`10.0.0.0/16`) | Isolated (No Internet access) |

---

## Route Tables and Traffic Routing

1. **Public Route Table (Web Tier):**
   - `10.0.0.0/16` -> `local`
   - `0.0.0.0/0` -> `Internet Gateway (IGW)`
   - Hosts Internet-facing Application Load Balancers (ALB) and public bastion hosts.

2. **Private Route Table (App Tier):**
   - `10.0.0.0/16` -> `local`
   - `0.0.0.0/0` -> `NAT Gateway` (located in the public subnet)
   - Allows application servers (Node.js/Spring/Python) to fetch software packages and security updates without exposing them to incoming internet traffic.

3. **Isolated Route Table (Database Tier):**
   - `10.0.0.0/16` -> `local`
   - No default route (`0.0.0.0/0`). RDS instances or database nodes are completely shielded from both inbound and outbound internet traffic.

---

## Security Group Layering (Least Privilege Access)

- **ALB Security Group (Web Tier):**
  - Inbound: HTTP (80) & HTTPS (443) from `0.0.0.0/0`
  - Outbound: Application port (e.g., 3000/8080) targeting `App-Tier-SG`
- **Application Security Group (App Tier):**
  - Inbound: Application port strictly from `ALB-SG`
  - Outbound: Database port (e.g., PostgreSQL 5432 / MySQL 3306) targeting `DB-Tier-SG`
- **Database Security Group (DB Tier):**
  - Inbound: Port 5432 / 3306 strictly from `App-Tier-SG`
  - Outbound: Minimal local VPC traffic only
