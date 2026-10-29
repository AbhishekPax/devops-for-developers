# Task: Creating a Custom AMI

## Objective
Configure a base Amazon EC2 instance with custom software and application files, create a customized Amazon Machine Image (AMI) from it, and launch a new instance verified from that custom AMI.

---

## 1. Golden AMI Workflow Overview
Creating a customized "Golden AMI" allows you to pre-bake operating system patches, application runtimes, configurations, and static dependencies into the image. This eliminates repetitive runtime bootstrapping and shortens scale-out launch times.
