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

<details>
  <summary><strong>▶ main.py</strong></summary>

```python
{% raw %}
import os
import sys
import json
from datetime import date
from collections import defaultdict
from s3_io import load_json, save_json
from budget import Category, create_spend_chart

BUCKET = os.environ.get("BUDGET_BUCKET", "elis-budget-tracker-123456")
LEDGER_KEY = os.environ.get("LEDGER_KEY", "data/ledger.json")

def load_categories_from_s3():
    raw = load_json(BUCKET, LEDGER_KEY, default={"categories": []})
    return [Category.from_dict(c) for c in raw.get("categories", [])]

def save_categories_to_s3(categories):
    payload = {"categories": [c.to_dict() for c in categories]}
    return save_json(BUCKET, LEDGER_KEY, payload)

# ... (rest of your code, like cmd_add, cmd_transfer, etc.)
{% endraw %}
```


</details> <details> <summary><strong>▶ budget.py</strong></summary>
```python
{% raw %}
class Category:
    def __init__(self, category=None):
        self.ledger = []
        self.balance = 0
        self.category = category

    def deposit(self, amount, description=""):
        self.ledger.append({"amount": amount, "description": description})
        self.balance += amount

    def withdraw(self, amount, description=""):
        if self.check_funds(amount):
            self.ledger.append({"amount": -amount, "description": description})
            self.balance -= amount
            return True
        return False

    def check_funds(self, amount):
        return amount <= self.balance

    def get_balance(self):
        return self.balance

    def to_dict(self):
        return {"category": self.category, "ledger": self.ledger, "balance": self.balance}

    @classmethod
    def from_dict(cls, data):
        c = cls(data["category"])
        c.ledger = data.get("ledger", [])
        c.balance = data.get("balance", 0)
        return c
{% endraw %}
```
</details> <details> <summary><strong>▶ s3_io.py</strong></summary>

```python
{% raw %}
import json
import boto3
from botocore.exceptions import ClientError

_s3 = boto3.client("s3")

def load_json(bucket: str, key: str, default):
    try:
        obj = _s3.get_object(Bucket=bucket, Key=key)
        return json.loads(obj["Body"].read().decode("utf-8"))
    except ClientError as e:
        code = e.response["Error"]["Code"]
        if code in ("NoSuchKey", "NoSuchBucket"):
            return default
        raise

def save_json(bucket: str, key: str, data):
    body = json.dumps(data, indent=2).encode("utf-8")
    _s3.put_object(Bucket=bucket, Key=key, Body=body, ContentType="application/json")
    return f"s3://{bucket}/{key}"
{% endraw %}
```
</details>


## 🧠 Key Features

- 💰 Add, withdraw, and transfer funds between categories

- ☁️ Automatic ledger storage in AWS S3

- 🔐 Encrypted and versioned storage by default

- 📊 Generate JSON reports on spending by category


## 🖥️ How It Works

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
