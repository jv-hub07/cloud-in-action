# AWS Concepts Glossary — Plain English Edition

> Every AWS service used in this project, explained for non-technical readers.
> No jargon. No assumed knowledge. Just plain English.

---

## Quick Reference Table

| Term | AWS Service | Plain English | Free Tier Limit |
|---|---|---|---|
| CloudFormation | AWS CloudFormation | The recipe book for your infrastructure | Always free |
| S3 | Amazon S3 | Cloud storage — a USB drive that never gets lost | 5 GB + 20K requests/mo |
| EC2 | Amazon EC2 | A virtual computer in Amazon's data center | 750 hrs/mo for 12 months |
| Lambda | AWS Lambda | A function that runs without a server | 1M requests/mo forever |
| API Gateway | Amazon API Gateway | The front door to your Lambda functions | 1M calls/mo for 12 months |
| IAM | AWS IAM | The security guard that controls who can do what | Always free |
| VPC | Amazon VPC | Your private section of Amazon's network | Always free |
| CloudWatch | Amazon CloudWatch | The log book and alarm system for your apps | 5 GB logs/mo |
| SSM | AWS Systems Manager | Secure shell access without opening port 22 | Always free |

---

## Detailed Glossary

---

### ☁️ CloudFormation
**AWS Service:** AWS CloudFormation

**Plain English:**
CloudFormation is the recipe book for your cloud infrastructure.
Instead of clicking through the AWS website to create servers and storage,
you write a YAML file (the recipe) that describes exactly what you want.
CloudFormation reads your recipe and builds everything automatically.

**Why it matters:**
- Repeatable: deploy the exact same infrastructure 100 times with one command
- Reversible: delete everything with one command (no leftover orphaned resources)
- Auditable: your infrastructure is code — version controlled in Git like any other code
- Collaborative: teams can review infrastructure changes in Pull Requests

**The analogy:** A traditional chef goes to the kitchen and builds the meal by hand.
A CloudFormation chef writes a recipe, hands it to a robot, and the robot builds
an identical meal every single time — in any kitchen, anywhere in the world.

**Free Tier:** CloudFormation itself is always free. You only pay for the resources it creates.

---

### 📦 S3 — Simple Storage Service
**AWS Service:** Amazon S3

**Plain English:**
S3 is cloud storage — like a USB drive that never gets lost, never runs out of space,
and can be accessed from anywhere in the world by anyone you choose.

Files in S3 are called **objects**. Folders are called **buckets**.
A bucket is like a hard drive partition. An object is like a file.

**What you can store:** Anything. HTML pages, images, videos, database backups,
log files, zip files, PDFs, code packages. If it's a file, S3 can store it.

**In this demo:** We configure an S3 bucket as a static website host.
This turns the bucket into a web server that serves HTML files to visitors —
no EC2 instance, no nginx, no maintenance. Amazon handles everything.

**The analogy:** S3 is like Google Drive, but for developers. You control exactly
who can access what, and you pay only for what you store.

**Free Tier:** 5 GB storage + 20,000 GET requests + 2,000 PUT requests per month.

---

### 🖥️ EC2 — Elastic Compute Cloud
**AWS Service:** Amazon EC2

**Plain English:**
EC2 is a virtual computer running in Amazon's data center that you rent by the hour.
You choose the operating system, the size (CPU and RAM), and the software to install.
It behaves exactly like a physical server — but you never touch the hardware.

**"Elastic"** means you can change its size anytime — make it bigger for heavy workloads,
smaller when traffic is light. You only pay for what you use.

**In this demo:** We create a t2.micro EC2 instance running Amazon Linux 2023.
A startup script (UserData) automatically installs nginx and writes a welcome page
when the server boots — zero manual steps required.

**The analogy:** EC2 is like renting a fully-equipped office workspace.
You get a desk, a computer, and internet access — without buying any furniture
or signing a long lease. You pay by the hour and can cancel anytime.

**Free Tier:** t2.micro instance — 750 hours per month FREE for the first 12 months.
That's enough to run a server 24/7 for an entire month at $0.

---

### ⚡ Lambda
**AWS Service:** AWS Lambda

**Plain English:**
Lambda lets you run code without managing any servers.
You write a function (a small piece of code), upload it to AWS,
and it runs whenever something triggers it (an HTTP request, a file upload, a timer).

You pay only for the time your code is actually running — billed in milliseconds.
When nobody calls your function, it costs nothing. When 1,000 people call it
simultaneously, it scales automatically. You never manage capacity.

**In this demo:** Our Lambda function runs Python code that returns a JSON greeting
whenever someone visits the API URL. The function runs for ~5 milliseconds per call.

**The analogy:** Imagine a vending machine for code. You put in a request,
the machine runs your code for a moment, gives you a response, and turns off.
You pay only for those moments of execution — not for the machine sitting idle.

**Free Tier:** 1,000,000 requests per month **FREE FOREVER** (not just the first 12 months).
The first million monthly invocations always cost $0, forever.

---

### 🚪 API Gateway
**AWS Service:** Amazon API Gateway

**Plain English:**
API Gateway is the front door to your Lambda functions.
It receives HTTP requests from the internet (browsers, mobile apps, other services),
routes them to the right Lambda function, and returns the response.

Think of API Gateway as a reception desk in a building:
- Visitors arrive and tell the receptionist what they need
- The receptionist routes them to the right department (Lambda function)
- The department answers, and the receptionist sends the answer back to the visitor

**In this demo:** We create an HTTP API with one route: `GET /hello`.
When a browser visits that URL, API Gateway calls the Lambda function and
returns the JSON response with a proper HTTP 200 status code and headers.

**Free Tier:** 1,000,000 HTTP API calls per month FREE for the first 12 months.

---

### 🔐 IAM — Identity and Access Management
**AWS Service:** AWS IAM

**Plain English:**
IAM is the security guard that controls who can do what in your AWS account.
Every action in AWS (creating a server, reading a file, sending an email)
requires permission. IAM grants and revokes those permissions.

**Key concepts:**
- **User:** a human or application with an identity (username + password or access keys)
- **Role:** a set of permissions that can be assumed by an AWS service (e.g., EC2 can write to S3)
- **Policy:** a document listing what is allowed or denied
- **Least Privilege:** only grant the minimum permissions needed — nothing more

**In this demo:**
- EC2 gets an IAM Role that allows SSM Session Manager access (and nothing else)
- Lambda gets an IAM Role that allows writing CloudWatch logs (and nothing else)

**The analogy:** IAM is like a keycard system in an office building.
Different employees have different access levels — some can enter the server room,
others can only access the break room. IAM defines those access levels for AWS.

**Free Tier:** IAM is always free — no charges for creating users, roles, or policies.

---

### 🏢 VPC — Virtual Private Cloud
**AWS Service:** Amazon VPC

**Plain English:**
A VPC is your private section of Amazon's massive global network.
Imagine AWS's network as a huge office building with millions of tenants.
Your VPC is YOUR floor — isolated from everyone else's floor, even though
you share the same physical building.

**Key components:**
- **Subnet:** a section of your VPC assigned to one availability zone (like rooms on your floor)
- **Internet Gateway:** the elevator that connects your floor to the outside world
- **Route Table:** the directory that tells traffic where to go
- **Security Group:** the lock on each room's door

**In this demo:** We create a VPC with one public subnet. "Public" means resources
inside it can have public IP addresses and reach the internet.

**The analogy:** VPC is like your company's private office floor in a shared skyscraper.
Other companies are in the building, but they can't access your floor.
You control who gets in, what rooms they can enter, and what they can do there.

**Free Tier:** VPC is always free — no charges for creating VPCs, subnets, or route tables.

---

### 📋 CloudWatch
**AWS Service:** Amazon CloudWatch

**Plain English:**
CloudWatch is the observation deck for your AWS resources.
It collects logs (text output from your applications), metrics (numbers like CPU usage),
and can send alarms when something goes wrong (CPU too high, errors detected).

In this demo we use CloudWatch Logs to capture everything the Lambda function
prints during execution. Every time someone calls the API, a log entry appears.

**The analogy:** CloudWatch is like the security camera system for your cloud infrastructure.
It records everything that happens, lets you search through recordings, and sends
alerts when it detects something unusual.

**Free Tier:** 5 GB of log data ingestion and storage per month FREE.

---

### 🔑 SSM Session Manager
**AWS Service:** AWS Systems Manager — Session Manager

**Plain English:**
Session Manager lets you open a terminal (command line) on your EC2 server
without opening any network ports and without using SSH keys.

Traditional server access requires:
1. Opening port 22 (SSH) on the server's firewall
2. Creating an SSH key pair
3. Distributing the private key to all admins
4. Managing key rotation and revocation

Session Manager requires:
1. Nothing — just an IAM role on the EC2 instance

The connection tunnels through AWS's infrastructure — never through the public internet.

**The analogy:** Instead of giving everyone a physical key to the server room (SSH),
you install a fingerprint scanner (SSM) that only works if AWS's central system
authorizes you. No key to lose, no lock to pick.

**Free Tier:** Session Manager is always free.

---

## The Big Picture — How It All Fits Together

```
YOUR LAPTOP (VS Code)
    │
    │  You write YAML describing your infrastructure
    │
    ▼
GITHUB REPOSITORY
    │
    │  GitHub Actions reads your YAML
    │  A human approves the deployment
    │
    ▼
AWS CLOUDFORMATION
    │
    │  CloudFormation reads the YAML recipe
    │  and creates resources in order
    │
    ├──► S3 BUCKET ──────────────────────────► Visitors see your website
    │
    ├──► VPC → EC2 → NGINX ─────────────────► Visitors see your server's page
    │
    └──► LAMBDA → API GATEWAY ──────────────► Visitors get JSON from your API
```

Every resource is tagged, logged, and managed by CloudFormation.
Delete the stack → every resource disappears cleanly. No orphaned resources. No surprise bills.

---

## Want to Learn More?

| Resource | What it teaches | Cost |
|---|---|---|
| [AWS Cloud Practitioner Essentials](https://aws.amazon.com/training/digital/aws-cloud-practitioner-essentials/) | AWS fundamentals | Free |
| [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/) | Best practices | Free |
| [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/) | CFN deep dive | Free |
| AWS Cloud Practitioner Certification | Prove your knowledge | $100 exam fee |
| AWS Solutions Architect Associate | Advanced architecture | $150 exam fee |
