# Task: Elastic IPs and Instance Metadata

## Objective
Allocate and associate an Amazon EC2 Elastic IP (EIP) with a running instance, verify persistent public connectivity, and retrieve instance metadata using Instance Metadata Service Version 2 (IMDSv2) from inside the instance.

---

## 1. What is an Elastic IP (EIP)?
An Elastic IP address is a static, public IPv4 address designed for dynamic cloud computing:
- Unlike standard auto-assigned public IPs (which change when an instance is stopped and started), an Elastic IP remains fixed to your AWS account.
- It can be dynamically remapped from an unhealthy instance to a standby instance to mask instance failures.

---

## 2. Allocating and Associating an Elastic IP

### Via AWS CLI:
```bash
# 1. Allocate a new Elastic IP in your VPC
ALLOC_ID=$(aws ec2 allocate-address \
    --domain vpc \
    --query 'AllocationId' \
    --output text)

echo "Allocated EIP ID: $ALLOC_ID"

# 2. Associate the Elastic IP with the target EC2 instance
aws ec2 associate-address \
    --instance-id <YOUR_INSTANCE_ID> \
    --allocation-id $ALLOC_ID

# 3. Retrieve the associated public IP
aws ec2 describe-addresses \
    --allocation-ids $ALLOC_ID \
    --query 'Addresses[0].PublicIp' \
    --output text
