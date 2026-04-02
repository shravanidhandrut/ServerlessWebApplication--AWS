# ☁️ Serverless Web Application on AWS

![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazon-aws&logoColor=white)
![S3](https://img.shields.io/badge/Amazon_S3-Static_Hosting-orange?logo=amazon-s3&logoColor=white)
![CloudFront](https://img.shields.io/badge/CloudFront-CDN-blue?logo=amazon-aws&logoColor=white)
![Lambda](https://img.shields.io/badge/AWS_Lambda-Serverless-orange?logo=aws-lambda&logoColor=white)
![DynamoDB](https://img.shields.io/badge/DynamoDB-NoSQL-blue?logo=amazon-dynamodb&logoColor=white)
![Route53](https://img.shields.io/badge/Route_53-DNS-orange?logo=amazon-aws&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-success)

> A fully serverless web application deployed on AWS using free-tier eligible services. Features a static website hosted on S3 + CloudFront with a live visitor counter powered by Lambda and DynamoDB — all served over HTTPS on a custom domain.

---

## 📌 Table of Contents

- [Project Overview](#-project-overview)
- [Architecture](#-architecture)
- [AWS Services Used](#-aws-services-used)
- [Project Structure](#-project-structure)
- [Step-by-Step Implementation](#-step-by-step-implementation)
- [Lambda Function Code](#-lambda-function-code)
- [Challenges & Solutions](#-challenges--solutions)
- [Cost Breakdown](#-cost-breakdown)
- [Resource Cleanup](#-resource-cleanup)
- [Key Learnings](#-key-learnings)

---

## 📖 Project Overview

This project demonstrates how to build and deploy a **fully serverless web application** on AWS without managing any servers. The application includes:

- 🌐 A **static website** served globally via CloudFront CDN
- 🔒 **HTTPS** secured with a free SSL certificate from AWS ACM
- 🌍 A **custom domain** (`shra.qzz.io`) registered for free via DigitalPlat
- 📊 A **live visitor counter** that increments on every visit using Lambda + DynamoDB
- 🆓 Built entirely on **AWS Free Tier** (cost: ~$0.50 for Route 53 only)

---

## 🏗️ Architecture

```
User / Browser
      │
      │ HTTPS
      ▼
Amazon CloudFront (CDN + HTTPS termination)
      │                         │
      │ Static Files            │ JS fetch()
      ▼                         ▼
Amazon S3                  AWS Lambda
(Website Files)         (Visitor Counter)
                               │
                               │ read / write
                               ▼
                       Amazon DynamoDB
                       (views count)

Supporting Services:
├── Route 53        → DNS management
├── ACM             → Free SSL certificate
├── IAM Role        → Lambda permissions
└── DigitalPlat     → Free domain registration
```

---

## 🛠️ AWS Services Used

| Service | Purpose |
|---|---|
| **Amazon S3** | Stores and serves static website files (HTML, CSS, JS) |
| **Amazon CloudFront** | CDN for global content delivery + HTTPS termination |
| **Amazon Route 53** | DNS management for custom domain |
| **AWS Certificate Manager** | Free SSL/TLS certificate for HTTPS |
| **Amazon DynamoDB** | NoSQL database to store visitor count |
| **AWS Lambda** | Serverless function for visitor counter logic |
| **AWS IAM** | Role with DynamoDB permissions for Lambda |
| **DigitalPlat** | Free domain provider (no student email required) |

---

## 🚀 Step-by-Step Implementation

### Step 1 — Create S3 Bucket

- Navigate to S3 in the AWS Console (region: `ap-south-1`)
- Create bucket named `shra-serverless-wep-app`
- Block all public access (CloudFront handles access via OAC)
- Upload website files (HTML, CSS, JS)

![S3 Bucket Created](screenshots/01-s3-bucket-created.png)

---

### Step 2 — Create CloudFront Distribution

- Create a new distribution named `distribution-shra-serverless-web-app`
- Set origin to the S3 bucket created above
- Enable **Origin Access Control (OAC)**

![CloudFront Distribution](screenshots/02-cloudfront-distribution.png)

![CloudFront Origin](screenshots/03-cloudfront-origin.png)

---

### Step 3 — Configure S3 Bucket Policy

- Go to CloudFront → Origins → copy the OAC bucket policy
- Go to S3 → Permissions → Bucket Policy → paste the policy
- This allows only CloudFront to access the S3 bucket

![S3 Bucket Policy Step 1](screenshots/04-s3-bucket-policy-1.png)

![S3 Bucket Policy Step 2](screenshots/05-s3-bucket-policy-2.png)

![S3 Bucket Policy Step 3](screenshots/06-s3-bucket-policy-3.png)

---

### Step 4 — Set Default Root Object

- In CloudFront distribution → Settings → Edit
- Set **Default Root Object** to `index.html`

![CloudFront Default Root Object](screenshots/07-cloudfront-default-root.png)

---

### Step 5 — Register Free Domain & Configure Route 53

- Registered free domain `shra.qzz.io` from [DigitalPlat](https://domain.digitalplat.org) (no student email required)
- In Route 53 → Create a **Public Hosted Zone** for `shra.qzz.io`
- Copy the 4 NS records from Route 53
- Paste NS records into DigitalPlat nameserver settings

![Route 53 Hosted Zone](screenshots/08-route53-hosted-zone.png)

![Route 53 Nameservers](screenshots/09-route53-nameservers.png)

---

### Step 6 — Set CloudFront Alternate Domain Name

- Go to CloudFront → General tab → Edit
- Under **Alternate Domain Names (CNAMEs)** add `shra.qzz.io`

![CloudFront Alternate Domain](screenshots/10-cloudfront-alternate-domain.png)

---

### Step 7 — Create SSL Certificate (ACM)

> ⚠️ Switch region to **us-east-1 (N. Virginia)** — required for CloudFront!

- Go to AWS Certificate Manager → Request public certificate
- Enter domain: `shra.qzz.io`
- Choose **DNS validation** → Click "Create records in Route 53"
- Wait for status to change to **Issued**

![ACM Certificate](screenshots/11-acm-certificate.png)

---

### Step 8 — Create Route 53 DNS Validation Record

- From ACM certificate settings → Click "Create records in Route 53"
- Verify the CNAME record appears in the hosted zone

![Route 53 SSL Record 1](screenshots/12-route53-ssl-record-1.png)

![Route 53 SSL Record 2](screenshots/13-route53-ssl-record-2.png)

---

### Step 9 — Attach SSL Certificate to CloudFront

- Go back to CloudFront → General tab → Edit
- Under **Custom SSL Certificate** select the issued ACM certificate
- Save and wait for deployment

![CloudFront SSL Attached](screenshots/14-cloudfront-ssl-attached.png)

---

### Step 10 — Create Route 53 A Record for CloudFront

- In Route 53 hosted zone → Create record
- Record name: `greeting`
- Type: **A (Alias)**
- Route traffic to: **CloudFront distribution**
- Save and verify

![Route 53 A Record 1](screenshots/15-route53-a-record-1.png)

![Route 53 A Record 2](screenshots/16-route53-a-record-2.png)

---

### Step 11 — Verify Domain Works

- Visit `https://shra.qzz.io` in browser
- Website loads over HTTPS with custom domain ✅

![Domain Working](screenshots/17-domain-working.png)

---

### Step 12 — Create DynamoDB Table

- Navigate to DynamoDB in `ap-south-1`
- Table name: `db-shra-serverless-wep-app`
- Partition key: `id` (String)
- Billing mode: On-demand (free tier)

![DynamoDB Table Created](screenshots/18-dynamodb-table.png)

---

### Step 13 — Create Initial Item in DynamoDB

- Go to Explore Table Items → Create item
- Add initial item:
```json
{
  "id": "0",
  "views": 0
}
```

![DynamoDB Item Created](screenshots/19-dynamodb-item.png)

---

### Step 14 — Create IAM Role for Lambda

- Go to IAM → Roles → Create Role
- Use case: **Lambda**
- Attach policies:
  - `AmazonDynamoDBFullAccess`
  - `AWSLambdaBasicExecutionRole`
- Role name: `role-shra-serverless-wep-app`

![IAM Role Created](screenshots/20-iam-role.png)

---

### Step 15 — Create Lambda Function

- Navigate to Lambda in `ap-south-1`
- Function name: `fun-shra-serverless-wep-app`
- Runtime: **Python 3.x**
- Execution role: select `role-shra-serverless-wep-app`
- Paste the visitor counter code (see below)
- Click **Deploy**
- Enable **Function URL** with Auth type: `NONE` and CORS: Enabled

![Lambda Function Created](screenshots/21-lambda-function.png)

![Lambda Code Deployed](screenshots/22-lambda-code-deployed.png)

![Lambda Test Success](screenshots/23-lambda-test-success.png)

![Lambda URL Working](screenshots/24-lambda-url-working.png)

![DynamoDB Views Updated](screenshots/25-dynamodb-views-updated.png)

---

## 🐍 Lambda Function Code

```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('db-shra-serverless-wep-app')

def lambda_handler(event, context):
    response = table.get_item(Key={
        'id': '0'
    })
    views = response['Item']['views']
    views = views + 1
    print(views)

    response = table.put_item(Item={
        'id': '0',
        'views': views
    })

    return {
        'statusCode': 200,
        'headers': {
            'Content-Type': 'application/json',
            'Access-Control-Allow-Origin': '*'
        },
        'body': json.dumps(views)
    }
```

**How it works:**
1. `get_item` — reads current visitor count from DynamoDB (`id = '0'`)
2. Increments count by 1
3. `put_item` — writes updated count back to DynamoDB
4. Returns count as JSON response with CORS headers

---

## 🐛 Challenges & Solutions

| Challenge | Solution |
|---|---|
| No `.edu` student email for GitHub Student Pack | Used **DigitalPlat** for a free domain with zero verification required |
| Route 53 is not free ($0.50/month) | Accepted minimal cost for the project duration; terminated after completion |
| Lambda Function URL showing DNS error in browser | Changed Chrome's DNS to Google DNS (`8.8.8.8`) within browser security settings only |
| `ResourceNotFoundException` when calling DynamoDB | Fixed by verifying exact table name, ensuring same region (`ap-south-1`), and confirming initial item existed |
| Visitor count incrementing by 2 instead of 1 | Browser sends two requests (page + favicon). Resolves naturally when called via JavaScript `fetch()` |
| `put_item` writing to wrong item (`id='1'` instead of `id='0'`) | Fixed typo in the code — changed `put_item` id from `'1'` to `'0'` |

---

## 💰 Cost Breakdown

| Service | Cost |
|---|---|
| Amazon S3 | $0.00 (Free Tier) |
| Amazon CloudFront | $0.00 (Free Tier) |
| AWS Lambda | $0.00 (Free Tier) |
| Amazon DynamoDB | $0.00 (Free Tier) |
| AWS ACM SSL Certificate | $0.00 (Always Free) |
| AWS IAM | $0.00 (Always Free) |
| DigitalPlat Domain | $0.00 (Free) |
| **Amazon Route 53** | **~$0.50** (one hosted zone, one month) |
| **Total** | **~$0.50** |

---

## 🗑️ Resource Cleanup

All resources were terminated after project completion. Deletion order to avoid dependency errors:

```
1. Lambda Function
2. CloudFront Distribution  (disable first → wait → delete)
3. ACM Certificate          (switch to us-east-1 first)
4. Route 53 Hosted Zone     (delete records first → delete zone)
5. S3 Bucket                (empty first → delete)
6. DynamoDB Table
7. IAM Role                 (always delete last)
```

---

## 📚 Key Learnings

- How to host a static website on **S3 + CloudFront** with Origin Access Control
- Configuring **custom domains** using Route 53 and free third-party providers
- Issuing and attaching **SSL certificates** via AWS Certificate Manager
- Writing **serverless backend functions** with AWS Lambda using Python (boto3)
- Storing and retrieving data from **DynamoDB** via Lambda
- Configuring **IAM roles** with least-privilege permissions
- Debugging real-world AWS issues including region mismatches, DNS errors, and CORS

---
**Shravani Dhandrut**
☁️ AWS Cloud Enthusiast
🔗 [LinkedIn](https://www.linkedin.com/in/shravanidhandrut/)
```
> ⭐ If you found this project helpful, please give it a star!
