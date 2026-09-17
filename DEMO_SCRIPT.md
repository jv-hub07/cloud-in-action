# 🎤 Demo Script — "Cloud in Action"
## A 20-Minute Presenter's Guide

---

> **For the presenter:** This guide tells you exactly what to say, what to show,
> and what the audience will experience at each step. Times are approximate.
> The goal is not to make the audience understand every detail — it's to make
> them FEEL the magic of infrastructure as code and leave curious to learn more.

---

### Before You Start — Checklist

- [ ] Fork the repo and push to your own GitHub account
- [ ] Add `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_REGION` to GitHub Secrets
- [ ] Create a GitHub Environment named `production` with yourself as required reviewer
- [ ] Open VS Code with the `cloud-in-action.code-workspace` file
- [ ] Open a browser tab: your GitHub repo → Actions tab
- [ ] Open a browser tab: AWS Console → CloudFormation → Stacks
- [ ] Have the `infra/02-server/template.yaml` file open and visible in VS Code
- [ ] Test the setup: run `☁️ Validate All Templates` VS Code task — confirm green
- [ ] Optional: pre-deploy Stack 3 so you can show a live Lambda API instantly

---

## Opening (2 minutes)

**SAY:** *(raise your hand and wait for silence)*
> "Raise your hand if you've ever wondered what 'the cloud' actually is.
> Not the marketing version — the real version."
>
> *(pause)*
>
> "The cloud is just someone else's computer. But smarter, bigger, and
> available to anyone with a credit card and 10 minutes."

**SHOW:** Open `https://aws.amazon.com/about-aws/global-infrastructure/`
*(The AWS global infrastructure map — data centers glowing around the world)*

**SAY:**
> "Amazon has data centers on every continent. When you deploy to AWS,
> you're renting a tiny slice of this — a virtual computer, some storage,
> a network — paying by the minute, cancelling anytime."

**KEY POINT:**
> "Today, I'm going to show you how developers interact with all of this
> without ever touching a server. Using just a text file and a button click."

---

## What is Infrastructure as Code? (3 minutes)

**SAY:**
> "Traditional way: you log into a website, click 'Create Server', fill out
> a form, click 'Create Subnet', click 'Attach Gateway'... twelve screens
> and twenty minutes later, you have a server. If you need to do it again
> tomorrow — you do it again from scratch. If you make a mistake — you
> manually undo each step."
>
> "Modern way: you write a recipe."

**SHOW:** Open `infra/02-server/template.yaml` in VS Code
*(Scroll slowly through the file, pausing on comment blocks)*

**SAY:** *(while scrolling)*
> "See these comments? This file was written to be read by humans, not just machines.
> Every resource has an explanation. Every decision has a reason."
>
> *(point to the Security Group section)*
>
> "Look at this: 'Port 22 is intentionally NOT listed here. No SSH port open =
> no brute force attack surface.' The infrastructure code is self-documenting.
> A new team member can read this file and understand not just WHAT was built,
> but WHY."

**KEY POINT:**
> "If you can read a recipe, you can read CloudFormation.
> Resources are ingredients. Properties are instructions.
> The result is a running server."

---

## Step 1 — Edit the Code (3 minutes)

**SAY:**
> "Let me show you how a developer actually makes a change.
> I'm going to rename this project and update the welcome page."

**SHOW IN VS CODE:**
1. In `infra/02-server/template.yaml`, find the `Default: cloud-in-action` under `ProjectName`
2. Change it to `my-first-cloud-server`
3. Show the yellow dot on the file tab — "unsaved changes"
4. Press `Ctrl+S` to save

**SHOW:** Click the Source Control icon in VS Code sidebar (the git branch icon)

**SAY:**
> "See this? VS Code shows me exactly what changed. The red line is what was there before.
> The green line is the new version. This is called a 'diff' — developers use this
> to review changes before committing them."

**SHOW:** In the terminal, type:
```
git add .
git commit -m "rename project to my-first-cloud-server"
```

**SAY:**
> "That 'commit' just saved a snapshot of this change to my local Git history.
> Now I'm going to push it to GitHub — which will trigger everything else."

---

## Step 2 — Push to GitHub (2 minutes)

**SAY:**
> "The moment I push this code, GitHub wakes up and starts running automated checks."

**SHOW IN VS CODE TERMINAL:**
```
git push origin main
```

**IMMEDIATELY switch to the browser: GitHub repo → Actions tab**

**SAY:**
> "Watch this. See the workflow starting? That's the `validate` workflow.
> GitHub is right now connecting to AWS and checking that our YAML template
> doesn't have any errors — BEFORE deploying anything."

**SHOW:** The validate workflow running in real time, steps completing with green checkmarks

**SAY:**
> "Green checkmark. Our template is valid. Zero errors.
> This is like spell-check for infrastructure — catch mistakes early,
> when they're cheap to fix, not after a broken deployment."

**KEY POINT:**
> "This validation ran automatically. We didn't schedule it.
> We didn't click anything. We pushed code — and the robot woke up."

---

## Step 3 — The Approval Gate ← THE WOW MOMENT (3 minutes)

**SAY:**
> "Now look at this. See the deploy workflow? It's not running yet.
> It says: 'Waiting for approval'."

**SHOW:** GitHub Actions → `deploy-stack2.yml` → "Waiting for review" orange badge

**SAY:**
> "Before any real AWS resources are created — before anything costs money,
> before any servers boot up — a human has to approve.
>
> This is how professional teams prevent accidents. This is how you prevent
> a junior developer from accidentally deploying to production at 3am.
> This is how you prevent surprise bills.
>
> The code says 'I want to build a server.' The robot says
> 'Okay, but let me ask a human first.'"

**SAY:**
> "I'm going to approve now. Watch what happens."

**SHOW:** Click "Review deployments" → check the checkbox → click "Approve and deploy"

**SAY:** *(while the workflow starts running)*
> "Watch these steps. CloudFormation is now reading our YAML recipe and
> building the infrastructure. Let's go watch it happen in AWS."

**SHOW:** Open the AWS Console → CloudFormation → Stacks tab
*(Stack `cloud-in-action-server-main` should appear with CREATE_IN_PROGRESS)*

**SAY:**
> "Click on the stack. Now click 'Events'."

**SHOW the Events tab — resources appearing one by one:**
- VPC created ✅
- Subnet created ✅
- Internet Gateway created ✅
- Security Group created ✅
- EC2 Instance created ✅
- Elastic IP created ✅

**SAY:** *(as each resource appears)*
> "VPC... subnet... internet gateway... security group... EC2 instance.
> We're watching a server being BORN right now. From a text file.
> Not from clicking. Not from a form. From a recipe."

---

## Step 4 — See It in AWS (4 minutes)

**SAY:**
> "The stack should be complete. Let's look at what CloudFormation created."

**SHOW:** AWS Console → CloudFormation → Stack → Resources tab

**SAY:**
> "Eight resources. All created automatically. All from 200 lines of YAML.
> Click on any resource ID and it takes you directly to that resource."

*(Click on the EC2 Instance ID)*

**SHOW:** AWS Console → EC2 Instances → your running instance

**SAY:**
> "There's our server. Running in `us-east-1`. Public IP assigned.
> Let's see if the website is up."

**SHOW:** CloudFormation → Stack → Outputs tab → click WebsiteURL

*(Browser opens — shows the nginx page: "Hello from my-first-cloud-server on AWS EC2!")*

**SAY:**
> "There it is. We pushed a text file. A server appeared.
> We changed a parameter. The welcome page reflects that change.
> That — is Infrastructure as Code."

**PAUSE for effect.**

**SHOW (bonus):** Copy the `SSMConnectCommand` output, paste it in the VS Code terminal:
```
aws ssm start-session --target i-XXXXXXXXXX --region us-east-1
```

**SAY:**
> "I just opened a terminal directly on that server — no SSH, no key files,
> no port 22 open anywhere. AWS's secure tunnel handles the authentication.
> This is how modern security works: less attack surface, more control."

---

## Step 5 — Serverless: No Server at All (3 minutes)

**SAY:**
> "Stack 3 takes this even further. What if I told you I could run code
> on the internet... with no server at all?"

*(If pre-deployed, skip to showing the URL. If not, trigger deploy-stack3 now.)*

**SHOW:** CloudFormation → Stack 3 → Outputs → click the `ApiUrl` link

*(Browser opens — shows JSON response)*

```json
{
  "message": "Hello from AWS Lambda!",
  "timestamp": "2024-01-15T14:32:01.234Z",
  "region": "us-east-1",
  "requestId": "abc-123-def-456",
  "funFact": "This function ran for milliseconds and cost $0.0000002"
}
```

**SAY:**
> "This is a live API. Right now. Accessible from anywhere in the world.
>
> *(point to the funFact)*
>
> It ran for milliseconds and cost two ten-millionths of a dollar.
> There is no server running. No EC2 instance. Nothing to patch or maintain.
> The code ran, responded, and stopped.
>
> The first one million requests every month are FREE. Forever. Not just the first year.
> Forever. That's serverless."

**KEY POINT:**
> "Stack 2 is a server that's always on — running even when nobody visits.
> Stack 3 is code that runs only when needed — paying only for those moments.
> Different tools. Different tradeoffs. Both valid. Both from the same YAML recipe."

---

## Teardown + Cost Review (1 minute)

**SAY:**
> "Before we finish — let me show you one more important thing."

**SHOW:** GitHub Actions → Teardown workflow → Run workflow → type DELETE

**SAY:**
> "One workflow. Everything we just built — VPC, EC2, Lambda, S3, all 18 resources
> — deleting right now. The recipe still exists in GitHub. We can redeploy
> in under 5 minutes, any time, in any AWS region in the world.
>
> We spent about 20 minutes today. We created real cloud infrastructure.
> We looked at it live in AWS. And now it's gone.
>
> Total cost: approximately zero dollars."

**SHOW:** AWS Console → Billing → Cost Explorer (if available, show $0.00)

---

## Q&A Prompts (use these to spark discussion)

> **"What would break if we skipped the approval gate?"**
>
> *Good answer:* Anyone who pushes code — accidentally or intentionally — can create
> or destroy AWS resources. In a company with 50 developers, that's 50 people who
> could accidentally spin up a $10,000/month database at 2am.

> **"How is this different from clicking in the AWS Console?"**
>
> *Good answer:* Clicking is manual, unrepeatable, and unauditable. IaC is automated,
> repeatable, and every change is recorded in Git with a timestamp and author.

> **"What happens if two developers push at the same time?"**
>
> *Good answer:* CloudFormation processes changesets sequentially. The second push
> waits. Modern teams use branch-based workflows (PR → review → merge) to prevent this.

> **"Could you use this to deploy a real app?"**
>
> *Good answer:* Yes — and companies do, at scale. Netflix, Airbnb, and Slack use
> similar IaC patterns. Add a database stack, a load balancer stack, and a
> container stack — and this demo becomes a production architecture.

> **"What does 'free tier' really mean?"**
>
> *Good answer:* AWS gives every new account 12 months of reduced-cost access
> to popular services (EC2, S3, RDS) and some services free forever (Lambda,
> CloudWatch, DynamoDB within limits). After 12 months, you pay standard rates.

---

## One-Line Takeaways (write these on a whiteboard)

1. **"Infrastructure is code"** — if it can be clicked, it can be written
2. **"Approval gates prevent accidents"** — humans in the loop, at the right moment
3. **"Everything is reversible"** — delete the stack, redeploy in minutes
4. **"Pay for what you use"** — serverless scales from $0 to millions without you
5. **"Logs tell the story"** — everything is observable, auditable, debuggable

---

## Resources to Share After the Demo

| Resource | Link |
|---|---|
| This repository | `https://github.com/YOUR-USERNAME/cloud-in-action` |
| AWS Free Tier | https://aws.amazon.com/free/ |
| CloudFormation docs | https://docs.aws.amazon.com/cloudformation/ |
| AWS Concepts glossary | `docs/aws-concepts.md` in this repo |
| AWS Cloud Practitioner | https://aws.amazon.com/certification/certified-cloud-practitioner/ |

---

*Total demo time: 20 minutes | Audience: anyone curious about cloud | Cost: $0.00*
