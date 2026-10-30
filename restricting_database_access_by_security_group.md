# Task: Restricting Database Access by Security Group

## Objective
Restrict the Amazon RDS PostgreSQL database security group so that network connectivity on port 5432 is strictly permitted only from the Application Tier's Security Group, eliminating static IP hardcoding and public access.

---

## 1. Why Reference Security Groups Instead of CIDR Blocks?
- **Dynamic Auto Scaling:** When application instances scale up or down dynamically, their private IP addresses change. Hardcoding CIDR ranges or individual IP addresses creates brittle rules and operational overhead.
- **Principle of Least Privilege:** Referencing the Application Tier Security Group ID (`sg-appxxxx`) as the source in the Database Security Group ensures that only EC2 instances explicitly assigned the app security group can talk to the database on port 5432.
- **Zero Public Exposure:** Even within the VPC, unauthorized instances (e.g., public bastion hosts, web proxies, or other unrelated microservices) are completely blocked at the network interface level.

---

## 2. Architecture Diagram
