# Task: Security Groups - Stateful, Instance-Level Firewalls

## Objective
Create an AWS Security Group that restricts inbound SSH access strictly to your public IP and allows inbound HTTP traffic from anywhere.

---

## Security Group Concepts
- **Instance-Level Firewall:** Controls inbound and outbound traffic at the elastic network interface (ENI) level of an EC2 instance.
- **Stateful Nature:** If an inbound request is permitted, the return/response traffic is automatically tracked and allowed outbound, regardless of outbound rules (and vice versa).

---

## Implementation Details

### Inbound Rules (Ingress)
| Type | Protocol | Port Range | Source | Description |
| :--- | :--- | :--- | :--- | :--- |
| **SSH** | TCP | 22 | `<YOUR_PUBLIC_IP>/32` | Restrict administrative SSH access to your specific IP only |
| **HTTP** | TCP | 80 | `0.0.0.0/0` | Allow public web traffic from anywhere |

### Outbound Rules (Egress)
| Type | Protocol | Port Range | Destination | Description |
| :--- | :--- | :--- | :--- | :--- |
| **All traffic** | All | All | `0.0.0.0/0` | Default allow-all outbound rule |

---

## AWS CLI Provisioning Commands

```bash
# 1. Create the Security Group
aws ec2 create-security-group \
    --group-name web-ssh-secgroup \
    --description "Allow SSH from my IP and HTTP from anywhere" \
    --vpc-id <YOUR_VPC_ID>

# 2. Add rule for SSH restricted to your public IP
aws ec2 authorize-security-group-ingress \
    --group-id <SECURITY_GROUP_ID> \
    --protocol tcp \
    --port 22 \
    --cidr <YOUR_PUBLIC_IP>/32

# 3. Add rule for HTTP accessible anywhere
aws ec2 authorize-security-group-ingress \
    --group-id <SECURITY_GROUP_ID> \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0
