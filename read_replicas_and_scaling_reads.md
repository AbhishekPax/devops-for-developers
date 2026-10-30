# Task: Read Replicas and Scaling Reads

## Objective
Create an Amazon RDS PostgreSQL Read Replica and demonstrate offloading read-heavy workloads by routing read-only queries (`SELECT`) to the replica while keeping write traffic (`INSERT`/`UPDATE`/`DELETE`) directed at the primary database instance.

---

## 1. What is an RDS Read Replica?
An RDS Read Replica is an independent, read-only database instance that replicates data from a source primary database using **asynchronous replication**.

- **Read Scalability:** Serves read-intensive traffic to alleviate processing load from the primary writer node.
- **Dedicated Endpoint:** Has its own distinct DNS endpoint address that client applications can point to for read operations.
- **Replication Lag:** Because replication is asynchronous, updates made on the primary appear on the read replica after a slight replication delay.

---

## 2. Creating a Read Replica via AWS CLI

```bash
# 1. Create a Read Replica from the primary database instance
aws rds create-db-instance-read-replica \
    --db-instance-identifier "dev-postgres-db-replica" \
    --source-db-instance-identifier "dev-postgres-db" \
    --db-instance-class "db.t4g.micro" \
    --no-publicly-accessible

# 2. Retrieve the endpoints for both Primary and Replica
aws rds describe-db-instances \
    --query 'DBInstances[?DBInstanceIdentifier==`dev-postgres-db` || DBInstanceIdentifier==`dev-postgres-db-replica`].[DBInstanceIdentifier,Endpoint.Address,StatusInfo.Status]' \
    --output table
