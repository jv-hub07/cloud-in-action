# Setting Up AWS Credentials for GitHub Actions

> **For non-technical readers:** This guide walks you through giving GitHub
> permission to create resources in your AWS account. It takes about 10 minutes.

---

## What We're Doing and Why

GitHub Actions needs to talk to AWS on your behalf. To do that, it needs
credentials — specifically, an **Access Key ID** and a **Secret Access Key**
for an AWS IAM user that has permission to create CloudFormation stacks.

We store these credentials as **GitHub Secrets** — encrypted values that
GitHub Actions can read but no human can see after they're saved.

**Security note:** For production environments, use
[OIDC authentication](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)
instead of long-lived access keys. OIDC is more secure because it generates
temporary credentials per-deployment with no long-lived secrets to leak.
For this demo, access keys are used for simplicity.

---

## Step 1: Create an IAM User in AWS

1. Sign in to the [AWS Console](https://console.aws.amazon.com)
2. In the search bar at the top, type **IAM** and click it
3. In the left sidebar, click **Users**
4. Click the orange **Create user** button
5. **User name:** `cloud-in-action-deployer`
6. Click **Next**
7. On the "Set permissions" screen, choose **Attach policies directly**
8. Search for and check each of these policies:
   - `AWSCloudFormationFullAccess`
   - `AmazonS3FullAccess`
   - `AmazonEC2FullAccess`
   - `AWSLambda_FullAccess`
   - `IAMFullAccess`
   - `AmazonAPIGatewayAdministrator`
   - `CloudWatchLogsFullAccess`
9. Click **Next** → **Create user**

> **Security note:** `IAMFullAccess` is broad. For a tighter security posture,
> create a custom policy that only allows `iam:CreateRole`, `iam:AttachRolePolicy`,
> `iam:PassRole`, `iam:DeleteRole`, and `iam:DetachRolePolicy`.

---

## Step 2: Create an Access Key

1. Click on the user you just created (`cloud-in-action-deployer`)
2. Click the **Security credentials** tab
3. Scroll to **Access keys** section → click **Create access key**
4. Select **Command Line Interface (CLI)**
5. Check the confirmation box → click **Next** → **Create access key**
6. **IMPORTANT:** Copy both values now — the Secret Access Key is only shown ONCE:
   - Access key ID (looks like: `AKIAIOSFODNN7EXAMPLE`)
   - Secret access key (looks like: `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY`)

> ⚠️ Never share these keys or commit them to any file. If compromised,
> go to IAM → Users → Security credentials → Deactivate the key immediately.

---

## Step 3: Add Secrets to GitHub

1. Go to your forked repository on GitHub
2. Click **Settings** (top menu bar of the repo)
3. In the left sidebar: **Secrets and variables → Actions**
4. Click **New repository secret** for each of the following:

   **Secret 1:**
   - Name: `AWS_ACCESS_KEY_ID`
   - Secret: *(paste your Access key ID)*
   - Click **Add secret**

   **Secret 2:**
   - Name: `AWS_SECRET_ACCESS_KEY`
   - Secret: *(paste your Secret access key)*
   - Click **Add secret**

   **Secret 3:**
   - Name: `AWS_REGION`
   - Secret: `us-east-1` *(or your preferred region)*
   - Click **Add secret**

You should now see all three secrets listed (their values are hidden — that's correct).

---

## Step 4: Set Up the Approval Gate (Environment)

1. In your GitHub repo: **Settings → Environments**
2. Click **New environment**
3. Name it exactly: `production`
4. Click **Configure environment**
5. Under **Deployment protection rules**: enable **Required reviewers**
6. Add your GitHub username as a reviewer
7. Click **Save protection rules**

Done! Now every deployment will pause and wait for your approval before
touching AWS.

---

## Verification

To confirm everything is set up correctly:

1. Go to **Actions** tab in your repo
2. Click on **☁️ Validate CloudFormation Templates**
3. Click **Run workflow** → **Run workflow**
4. Watch the workflow run — it should connect to AWS and validate all templates
5. A green checkmark = credentials are working correctly ✅

---

## OIDC Alternative (More Secure)

For production use, replace access keys with OIDC:

1. In AWS IAM, create an **Identity Provider** for `token.actions.githubusercontent.com`
2. Create an IAM Role that trusts the GitHub OIDC provider
3. Replace the `aws-actions/configure-aws-credentials` step with:

```yaml
- name: Configure AWS credentials (OIDC)
  uses: aws-actions/configure-aws-credentials@e3dd6a429d7300a6a4c196c26e071d42e0343502
  with:
    role-to-assume: arn:aws:iam::YOUR-ACCOUNT-ID:role/GitHubActionsRole
    aws-region: us-east-1
```

This generates temporary credentials per-deployment — no long-lived secrets to manage.

Full guide: https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services
