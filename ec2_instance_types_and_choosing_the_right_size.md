# Task: EC2 Instance Types and Choosing the Right Size

## Objective
Compare three Amazon EC2 instance types across different families for a production sample workload and justify the optimal choice based on performance, architecture, and cost efficiency.

---

## Sample Workload Profile: Scalable Web Application Backend
- **Application:** Containerized Node.js / Express REST API handling 2,500 requests/minute.
- **Resource Characteristics:** Moderate CPU usage, consistent memory footprint (approx. 4–6 GB active working set per worker node), steady network throughput with spikes during peak business hours.
- **Availability Requirement:** Horizontally scalable tier managed by an Auto Scaling Group behind an Application Load Balancer (ALB).

---

## Instance Types Comparison

| Attribute | **t4g.xlarge** (General Purpose - Burstable) | **m6i.xlarge** (General Purpose - Fixed) | **c6i.xlarge** (Compute Optimized) |
| :--- | :--- | :--- | :--- |
| **Processor Architecture** | AWS Graviton2 (64-bit ARM) | Intel Xeon 8375C (x86_64) | Intel Xeon 8375C (x86_64) |
| **vCPUs** | 4 | 4 | 4 |
| **Memory (RAM)** | 16 GiB | 16 GiB | 8 GiB |
| **CPU Performance** | Burstable (CPU Credits / Unlimited) | Dedicated Baseline (100% sustained) | Dedicated Baseline (100% sustained) |
| **Network Bandwidth** | Up to 5 Gbps | Up to 12.5 Gbps | Up to 12.5 Gbps |
| **On-Demand Price (us-east-1)** | ~$0.1344 / hr | ~$0.1920 / hr | ~$0.1700 / hr |

---

## Analysis & Evaluation

1. **`t4g.xlarge` (Burstable General Purpose):**
   - *Pros:* Lowest cost among the three options; Graviton2 delivers exceptional performance-per-dollar for ARM-compatible runtimes like Node.js.
   - *Cons:* Relies on CPU credit balances. Prolonged sustained traffic spikes can exhaust credits, resulting in throttling or extra burst charges under T4g Unlimited mode.

2. **`m6i.xlarge` (Balanced General Purpose - Dedicated):**
   - *Pros:* Balanced 1:4 vCPU-to-memory ratio with guaranteed 100% compute capacity; handles steady traffic without credit overhead or throttling risk.
   - *Cons:* Slightly higher cost than ARM-based alternatives.

3. **`c6i.xlarge` (Compute Optimized):**
   - *Pros:* High clock speeds and strong single-thread compute throughput.
   - *Cons:* Limited memory (8 GiB total, 1:2 ratio). With Node.js clustering and memory overhead, this leaves little headroom for cache caching or unexpected memory pressure.

---

## Justification & Recommendation

### Chosen Instance: **`t4g.xlarge`** (with `m6i.xlarge` fallback)
- **Cost Efficiency:** `t4g.xlarge` offers an immediate **~30% cost reduction** compared to equivalent x86 instances (`m6i.xlarge`) while providing 16 GiB of memory.
- **Runtime Compatibility:** Node.js, V8, and Docker run natively on ARM64 architectures with zero code alterations required.
- **Horizontal Scaling Synergy:** In an Auto Scaling Group configuration, instances scale out when CPU utilization reaches 65–70%, preventing sustained 100% CPU lock that would drain burst credits.
