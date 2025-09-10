---
title: Launching Your First EC2 Instance on AWS
date: 2025-09-10 00:00:00 +0200
categories: [AWS, Cloud, EC2]
tags: [AWS, EC2, Cloud Computing, Linux, CLI]
author: elis_cazacu
description: "Step-by-step walkthrough of launching and connecting to your first EC2 instance on AWS"
---

# Launching Your First EC2 Instance ☁️

This project demonstrates how to launch an EC2 instance on AWS, configure key pairs, choose an OS, set up networking, and connect to it.  

---

## Lab Overview 🖼️

![Lab Flowchart](/assets/img/first-ec2-instance/launch-page1.png)  
> This is the general workflow for launching an EC2 instance on AWS.
{: .prompt-info }

---

## 1. EC2 Instance Setup ⚙️

### Choosing an AMI (Operating System)

- Selected the desired OS for the EC2 instance:
  - Amazon Linux 2023 -- in this case
  - Ensure the OS matches your project requirements

![Choosing OS](/assets/img/first-ec2-instance/choosing-os.png)

### Selecting Instance Type

- Chose **t3.micro** for free-tier eligibility
- Verified CPU, memory, and cost suitability

![Instance Type](/assets/img/first-ec2-instance/instance-type.png)

### Key Pair Configuration

- Created a new key pair for SSH access
- Downloaded the `.pem` file and stored securely

![Created Key Pair](/assets/img/first-ec2-instance/created-key-pair.png)
---

## 2. Network & Storage Settings 🌐💾

### Networking

- Selected default VPC and subnet
- Configured security group rules:
  - SSH (port 22) open for your IP
  - HTTP/HTTPS optional if running a web server

![Network Settings](/assets/img/first-ec2-instance/network-settings.png)

### Storage

- Attached default 8 GB EBS volume
- Optionally increased storage based on use case

![Storage Configuration](/assets/img/first-ec2-instance/storage.png)

---

## 3. Launching the Instance 🚀

- Reviewed all settings
- Launched the instance
- Verified status as **running**

![Instance Running](/assets/img/first-ec2-instance/instance-running.png)  

---

## 4. Connecting to the Instance 🔗

- Open your terminal or CLI
- Go to where you saved your key pair:

![Key Pair Showcase](/assets/img/first-ec2-instance/key-pair-showcase.png)

- Connect via SSH:
```bash
ssh -i "my-key-pair.pem" ubuntu@<Public-IP-Address>
```
- You should now be logged into your EC2 instance

![Success!](/assets/img/first-ec2-instance/success!.png)

## Lessons Learned 📚

- How to select an AMI and instance type for free-tier or custom needs
- Key pair creation and secure storage
- Configuring networking and storage options for EC2 instances
- SSH access to Linux-based instances

## Future Work 🔮

- Launch multiple EC2 instances and test networking between them
- Automate EC2 deployment with AWS CLI or CloudFormation
- Install and configure web servers, databases, or other services