# Task: Network ACLs - Stateless, Subnet-Level Firewalls

## Objective
Configure a Network Access Control List (NACL) at the subnet level to explicitly block a specific IP range (e.g., `203.0.113.0/24`) while allowing legitimate web traffic, and compare NACL behavior to Security Groups.

---

## 1. Network ACL Configuration

### Inbound Rules (Ingress)
Rules are evaluated in order from lowest to highest rule number. Once a rule matches traffic, it is applied immediately without evaluating subsequent rules.

| Rule # | Type | Protocol | Port Range | Source | Allow / Deny | Purpose |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **90** | All Traffic | All | All | `203.0.113.0/24` | **DENY** | Explicitly block malicious/unwanted IP CIDR block |
| **100** | HTTP (80) | TCP | 80 | `0.0.0.0/0` | **ALLOW** | Allow inbound public HTTP traffic |
| **110** | HTTPS (443) | TCP | 443 | `0.0.0.0/0` | **ALLOW** | Allow inbound public HTTPS traffic |
| **120** | SSH (22) | TCP | 22 | `<ADMIN_IP>/32` | **ALLOW** | Restrict administrative SSH access |
| `*` | All Traffic | All | All | `0.0.0.0/0` | **DENY** | Default implicit catch-all deny |

### Outbound Rules (Egress)
Because NACLs are **stateless**, ephemeral ports must be explicitly opened to allow return response traffic back to the client.

| Rule # | Type | Protocol | Port Range | Destination | Allow / Deny | Purpose |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **100** | Custom TCP | TCP | `1024-65535` | `0.0.0.0/0` | **ALLOW** | Return traffic to client ephemeral ports |
| **110** | HTTP (80) | TCP | 80 | `0.0.0.0/0` | **ALLOW** | Outbound HTTP requests from instances |
| **120** | HTTPS (443) | TCP | 443 | `0.0.0.0/0` | **ALLOW** | Outbound HTTPS requests from instances |
| `*` | All Traffic | All | All | `0.0.0.0/0` | **DENY** | Default implicit catch-all deny |

---

## 2. AWS CLI Provisioning Steps

```bash
# 1. Create a custom NACL
aws ec2 create-network-acl \
    --vpc-id <VPC_ID> \
    --query 'NetworkAcl.NetworkAclId' \
    --output text

# 2. Add Rule 90: Explicit DENY for blocked CIDR
aws ec2 create-network-acl-entry \
    --network-acl-id <NACL_ID> \
    --ingress \
    --rule-number 90 \
    --protocol -1 \
    --rule-action deny \
    --cidr-block 203.0.113.0/24

# 3. Add Rule 100: ALLOW inbound HTTP
aws ec2 create-network-acl-entry \
    --network-acl-id <NACL_ID> \
    --ingress \
    --rule-number 100 \
    --protocol 6 \
    --port-range From=80,To=80 \
    --rule-action allow \
    --cidr-block 0.0.0.0/0

# 4. Add Rule 100 Outbound: ALLOW outbound return traffic across ephemeral ports
aws ec2 create-network-acl-entry \
    --network-acl-id <NACL_ID> \
    --egress \
    --rule-number 100 \
    --protocol 6 \
    --port-range From=1024,To=65535 \
    --rule-action allow \
    --cidr-block 0.0.0.0/0

# 5. Associate NACL with the target subnet
aws ec2 replace-network-acl-association \
    --association-id <CURRENT_ASSOCIATION_ID> \
    --network-acl-id <NACL_ID>
