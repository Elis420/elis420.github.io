---
title: "Cloud Budget Tracker (Python + AWS S3)"
date: 2025-11-12 10:00:00 +0200
categories: [Projects, Cloud]
tags: [AWS, Python, S3, Automation, boto3]
author: elis_cazacu
description: "A serverless-style Python project that integrates with AWS S3 for secure, cloud-based budget management."
---

# ☁️ Cloud Budget Tracker (Python + AWS S3)

This project demonstrates how to use **Python** and **AWS S3** to build a cloud-based budget tracker.  
All data — deposits, withdrawals, and spending reports — are stored securely in S3, showing how automation and cloud storage can work together to simplify everyday tasks.

---

## 🧩 Overview

**Architecture**

```text
Python CLI ──► AWS S3 (ledger + reports)
```

> The app stores each transaction in a JSON ledger and automatically generates spending summaries saved to the /reports folder in S3.
{: .prompt-info }

## ⚙️ Tech Stack

| Component | Description |
|------------|--------------|
| **Python 3.12** | Core logic and budget tracking |
| **boto3** | AWS SDK for S3 interactions |
| **AWS CloudShell** | Cloud execution environment |
| **S3 Bucket** | Secure ledger + report storage |

## 💻 Full Source Code




## 🧠 Key Features

- 💰 Add, withdraw, and transfer funds between categories

- ☁️ Automatic ledger storage in AWS S3

- 🔐 Encrypted and versioned storage by default

- 📊 Generate JSON reports on spending by category


## 💾 Source Code

The full implementation is available on GitHub:  
👉 [View on GitHub → elis420/cloud-budget-tracker](https://github.com/Elis420/cloud-budget-tracker)


### 1. Set Environment Variables

```bash
export BUDGET_BUCKET=elis-budget-tracker-123456
export LEDGER_KEY=data/ledger.json
```
### 2. Run the App

```bash
python3 main.py add Food 150 "Groceries"
python3 main.py add Rent 400 "October rent"
python3 main.py transfer Food Rent 50
python3 main.py report
```
All data and reports are automatically synced to your AWS S3 bucket:

```bash
s3://elis-budget-tracker-123456/data/ledger.json
s3://elis-budget-tracker-123456/reports/YYYY-MM-DD.json
```

## 🛡️ Security

> All S3 objects are encrypted (SSE-S3) and versioned.
IAM roles follow the principle of least privilege (s3:GetObject, s3:PutObject, s3:ListBucket).
{: .prompt-tip }

## 🌟 Future Improvements

- Automate weekly reports with AWS Lambda + EventBridge

- Add Amazon SES for email notifications

- Build a web UI using Streamlit to visualize spending

## 📸 Screenshots

> Budget summary report stored in S3:

![report stored in S3](/assets/img/budget-tracker/report-stored.png)

> Running the tracker inside AWS CloudShell:

![running in CloudShell](/assets/img/budget-tracker/running-in-aws-cloudshell.png)

## 🧩 Lessons Learned

> Combining Python automation with AWS services can turn a simple script into a scalable, cloud-native workflow.
This project helped reinforce my knowledge of S3 permissions, encryption, and the power of serverless tools.
{: .prompt-info }
