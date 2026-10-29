# Task: Security Groups vs NACLs - When You Need Both

## Objective
Explain, with a practical end-to-end example, a scenario where both a Security Group rule and a Network ACL (NACL) rule are required to permit the same traffic to reach an EC2 instance.

---

## Defense-in-Depth Architecture
In AWS networking, traffic traverses multiple layers before reaching an application instance:

1. **Subnet Boundary (NACL):** First line of defense. Evaluates traffic entering or exiting the subnet (stateless).
2. **Instance / ENI Boundary (Security Group):** Second line of defense. Evaluates traffic reaching the virtual network interface (stateful).

Because these layers operate in series, **traffic must be explicitly allowed by both layers**. If either layer denies the traffic, the packet is dropped.

---

## Practical Example: Public HTTPS Web Server

Consider a production web server (`EC2`) hosted inside a public subnet serving incoming HTTPS requests on port 443 from global clients (`0.0.0.0/0`).

### 1. Network ACL (NACL) Requirements (Subnet Level)
Because NACLs are **stateless**, rules must explicitly cover both inbound requests and outbound responses back to the client's ephemeral port range:

- **Inbound Rule (Port 443):**
  - Rule Number: `100`
  - Protocol: `TCP`
  - Port Range: `443`
  - Source: `0.0.0.0/0`
  - Action: `ALLOW`

- **Outbound Rule (Ephemeral Ports for Response):**
  - Rule Number: `100`
  - Protocol: `TCP`
  - Port Range: `1024 - 65535`
  - Destination: `0.0.0.0/0`
  - Action: `ALLOW`

*(If the outbound ephemeral rule is missing from the custom NACL, client requests will reach the subnet, but response packets will be dropped at the subnet boundary).*

---

### 2. Security Group Requirements (Instance Level)
Because Security Groups are **stateful**, only the inbound rule needs to be defined:

- **Inbound Rule (Port 443):**
  - Protocol: `TCP`
  - Port Range: `443`
  - Source: `0.0.0.0/0`
  - Description: `Allow inbound HTTPS from anywhere`

- **Outbound Rule:**
  - Connection tracking automatically permits response traffic back to the client, irrespective of egress rules.

---

## What Happens If One Is Missing?
- **NACL allows Port 443, SG has no Port 443 rule:** Traffic passes the subnet boundary but is immediately blocked at the EC2 ENI. The connection drops.
- **SG allows Port 443, NACL lacks inbound Port 443 or outbound ephemeral ports:** Traffic is blocked at the subnet perimeter, never reaching the EC2 instance.
