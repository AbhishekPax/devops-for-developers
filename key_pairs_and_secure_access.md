# Task: Key Pairs and Secure Access

## Objective
Generate an asymmetric SSH key pair and use it to establish a secure, passwordless remote connection to an Amazon EC2 Linux instance.

---

## 1. Concepts: Public-Key Cryptography in AWS EC2
AWS utilizes asymmetric key pairs for instance access:
- **Public Key:** AWS injects the public key into the target instance at launch time, appending it to `~/.ssh/authorized_keys` for the default user (e.g., `ec2-user` or `ubuntu`).
- **Private Key (`.pem`):** Retained locally by the administrator. During SSH handshake, the private key proves identity cryptographically without sending any password over the network, completely mitigating brute-force password attacks.

---

## 2. Generating the Key Pair

### Option A: AWS CLI Method
```bash
aws ec2 create-key-pair \
    --key-name prod-ssh-key \
    --key-type rsa \
    --query 'KeyMaterial' \
    --output text > prod-ssh-key.pem
