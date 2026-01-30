# Week 0 — Billing and Architecture


## 🛠️ Environment Setup
I established a local development environment on Windows to ensure persistence and direct control over the AWS CLI.

Install & Verify AWS CLI
The AWS CLI v2 was installed locally and verified:
```bash
aws sts get-caller-identity
```

## Environment Variables Persistence
Instead of using Gitpod's temporary environment, I configured the PowerShell $PROFILE to permanently store my AWS credentials and region:

```bash
$env:AWS_ACCOUNT_ID = "YOUR_ACCOUNT_ID"
$env:AWS_DEFAULT_REGION = "ca-central-1"
```
## 🛡️ Billing Protection (IaC)
I deployed billing protection using Infrastructure as Code (IaC) by executing CLI commands with nested JSON configurations.

1. SNS Topic & Email Subscription
Created an SNS topic for billing alerts and subscribed my email.

```bash
# Create Topic
aws sns create-topic --name billing-alarm

# Subscribe (Confirm via email afterwards)
aws sns subscribe `
    --topic-arn "arn:aws:sns:ca-central-1:$($env:AWS_ACCOUNT_ID):billing-alarm" `
    --protocol email `
    --notification-endpoint Abdulsamad.alhamwi@gmail.com
```
## 2. CloudWatch Billing Alarm
Deployed a daily alarm via CLI to monitor estimated charges with a $1 USD threshold. The alarm was successfully triggered as verified in the CloudWatch console.
```bash
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm-config.json
```
## 3. AWS Budgets
Set a monthly budget of $1 USD with an 80% notification threshold using dedicated JSON configuration files.

```bash 
aws budgets create-budget `
    --account-id $env:AWS_ACCOUNT_ID `
    --budget file://aws/json/budget.json `
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```

⚙️ PowerShell Syntax Adjustments
To run Linux-based instructions on Windows, I made the following adjustments:

Multi-line Commands: Used the backtick (`) instead of backslash (\).

Environment Variables: Used the $env: prefix for variable access.

File Paths: Utilized the file:// prefix for local JSON referencing.
