# Task: AMIs and Launching Instances

## Objective
Launch an Amazon EC2 instance from an official public AMI (Amazon Linux 2023 or Ubuntu 24.04 LTS) and establish a secure remote connection over SSH.

---

## What is an Amazon Machine Image (AMI)?
An AMI is a packaged template containing the root volume image, operating system, initial software configuration, and launch permissions necessary to initialize an EC2 virtual server.

---

## Implementation Steps

### 1. Key Pair Creation
Generate an SSH key pair to authenticate the remote session:
```bash
aws ec2 create-key-pair \
    --key-name devops-ec2-key \
    --query 'KeyMaterial' \
    --output text > devops-ec2-key.pem

# Apply strict permissions required by OpenSSH
chmod 400 devops-ec2-key.pem
