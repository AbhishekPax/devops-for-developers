# Task: Multi-AZ RDS for High Availability

## Objective
Enable Multi-AZ deployment on an Amazon RDS PostgreSQL instance and detail the automated failover mechanism, synchronous data replication, and DNS-based recovery process.

---

## 1. What is Amazon RDS Multi-AZ?
In a Multi-AZ deployment, Amazon RDS automatically provisions and maintains a synchronous standby replica in a different Availability Zone (AZ) within the same AWS Region.

- **Synchronous Replication:** Transactions written to the primary DB instance are synchronously replicated to the standby storage before returning a commit confirmation.
- **Failover Target:** The standby replica is purely for disaster recovery and failover; it does not serve active read or write traffic until promoted.

---

## 2. Enabling Multi-AZ via AWS CLI

```bash
# Modify existing single-AZ RDS instance to Multi-AZ
aws rds modify-db-instance \
    --db-instance-identifier "dev-postgres-db" \
    --multi-az \
    --apply-immediately
