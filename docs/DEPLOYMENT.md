# Deployment Guide

Complete step-by-step guide for deploying both Shopify theme and AWS backend.

---

## 🎯 Pre-Deployment Checklist

- [ ] Node.js 20+ installed
- [ ] Serverless Framework v3 installed
- [ ] AWS credentials configured (`aws configure`)
- [ ] GitHub account set up
- [ ] Shopify store access
- [ ] AWS Bedrock model access requested

---

## 📦 Part 1: Shopify Theme Deployment

### Method 1: Manual Upload (Fastest)

```bash
# From project root
cd shopify-theme

# Create zip
zip -r ../SHADOWIFY.zip . -x "*.DS_Store" -x "__MACOSX/*" -x ".git/*"

# Upload steps:
# 1. Go to: https://screentimejourney.myshopify.com/admin/themes
# 2. Click "Add theme" → "Upload zip file"
# 3. Select SHADOWIFY.zip
# 4. Wait for upload to complete
# 5. Click "Customize" to preview
# 6. Click "Publish" when ready
```

### Method 2: GitHub Integration (Auto-deploy)

```bash
# Theme is already on GitHub:
# https://github.com/merijnkok959595/shadowify_redpillai

# To update:
cd shopify-theme
git add .
git commit -m "Update theme: [describe changes]"
git push origin main

# In Shopify Admin:
# 1. Go to Online Store → Themes
# 2. Click "Connect from GitHub"
# 3. Select: merijnkok959595/shadowify_redpillai
# 4. Branch: main
# 5. Enable "Auto-publish" for instant updates
```

### Theme Customization

After deployment, customize via Shopify admin:

1. **Colors & Typography**
   - Theme settings → Colors
   - Adjust brand colors

2. **Logo & Branding**
   - Theme settings → Logo
   - Upload your logo

3. **Product Pages**
   - Customize → Templates → Product
   - Adjust layout

4. **Homepage**
   - Customize → Home page
   - Add/remove sections

---

## ☁️ Part 2: AWS Backend Deployment

### Step 1: AWS Setup

```bash
# Configure AWS credentials if not done
aws configure
# AWS Access Key ID: [your-key]
# AWS Secret Access Key: [your-secret]
# Default region: eu-west-1
# Default output format: json

# Verify credentials
aws sts get-caller-identity

# Expected output:
# {
#   "UserId": "...",
#   "Account": "218638337917",
#   "Arn": "arn:aws:iam::218638337917:root"
# }
```

### Step 2: Enable AWS Bedrock

**CRITICAL:** Do this before deployment!

```bash
# Open Bedrock console
open https://eu-west-1.console.aws.amazon.com/bedrock/home?region=eu-west-1#/models

# Or manually:
# 1. Go to AWS Console
# 2. Navigate to Bedrock
# 3. Click "Model access"
# 4. Request access to: "Meta Llama 3 70B Instruct"
# 5. Wait for approval email (2-5 minutes)

# Verify access
aws bedrock list-foundation-models --region eu-west-1 \
  --query 'modelSummaries[?modelId==`meta.llama3-70b-instruct-v1:0`]'
```

### Step 3: Install Dependencies

```bash
cd app_redpillai

# Install all service dependencies
npm install

# This installs dependencies for:
# - auth/
# - ai-chat-api/
# - business-api/
# - business-worker/
# - website/
```

### Step 4: Deploy All Services

```bash
# From app_redpillai directory
serverless deploy --stage dev

# Deployment order (automatic):
# 1. event-bus        (~45s)
# 2. auth             (~120s)
# 3. ai-chat-api      (~90s)
# 4. business-api     (~80s)
# 5. business-worker  (~140s)
# 6. website          (~85s)

# Total time: ~6-8 minutes
```

### Step 5: Save Deployment Info

```bash
# After deployment, save these URLs to a file:
cd app_redpillai

# Create endpoints file
cat > ENDPOINTS.txt << 'EOF'
# Red Pill AI - AWS Endpoints

Website URL:
https://ldqvn0td0m.execute-api.eu-west-1.amazonaws.com

Auth API URL:
https://35nr4almb9.execute-api.eu-west-1.amazonaws.com

AI Chat API URL:
https://etokvqj53wptcw43i4y332vqhu0dfjmb.lambda-url.eu-west-1.on.aws/

Business API URL:
https://wd8caaa827.execute-api.eu-west-1.amazonaws.com

DynamoDB Tables:
- user-table-dev
- ai-chat-api-usage-table-dev

EventBridge:
arn:aws:events:eu-west-1:218638337917:event-bus/event-bus-dev

Deployed: $(date)
EOF

# View your endpoints
cat ENDPOINTS.txt
```

---

## ✅ Post-Deployment Verification

### Test 1: Website Access

```bash
# Visit the website
open https://ldqvn0td0m.execute-api.eu-west-1.amazonaws.com

# Expected: React app loads with login/register page
```

### Test 2: User Registration

```bash
export AUTH_URL="https://35nr4almb9.execute-api.eu-west-1.amazonaws.com"

curl -X POST $AUTH_URL/auth/register \
  -H 'Content-Type: application/json' \
  -d '{
    "email": "test@redpillai.com",
    "password": "TestPassword123!"
  }'

# Expected response:
# {
#   "message": "User registered successfully",
#   "userId": "...",
#   "token": "eyJhbGc..."
# }
```

### Test 3: User Login

```bash
curl -X POST $AUTH_URL/auth/login \
  -H 'Content-Type: application/json' \
  -d '{
    "email": "test@redpillai.com",
    "password": "TestPassword123!"
  }'

# Save token from response
export TOKEN="eyJhbGc..."
```

### Test 4: AI Chat (Bedrock)

```bash
export CHAT_URL="https://etokvqj53wptcw43i4y332vqhu0dfjmb.lambda-url.eu-west-1.on.aws/"

curl -N -X POST $CHAT_URL \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer $TOKEN" \
  -d '[{
    "role": "user",
    "content": [{
      "text": "Hello! Can you help me analyze a WhatsApp conversation?"
    }]
  }]'

# Expected: Streaming response from Llama 3 model
```

### Test 5: Check DynamoDB

```bash
# List tables
aws dynamodb list-tables --region eu-west-1

# Get user count
aws dynamodb scan \
  --table-name user-table-dev \
  --region eu-west-1 \
  --select COUNT

# Check AI usage
aws dynamodb scan \
  --table-name ai-chat-api-usage-table-dev \
  --region eu-west-1 \
  --select COUNT
```

### Test 6: Check Lambda Logs

```bash
# View auth logs
serverless auth logs -f api --stage dev --tail

# View AI chat logs  
serverless aiChatApi logs -f api --stage dev --tail

# Or via AWS Console:
open https://eu-west-1.console.aws.amazon.com/cloudwatch/home?region=eu-west-1#logsV2:log-groups
```

---

## 🔄 Update Deployment

### Update Single Service

```bash
cd app_redpillai

# Update auth service only
serverless auth deploy --stage dev

# Update AI chat service only
serverless aiChatApi deploy --stage dev

# Update website only
serverless web deploy --stage dev
```

### Update All Services

```bash
cd app_redpillai

# Redeploy everything
serverless deploy --stage dev
```

### Rollback Deployment

```bash
# List deployments
serverless deploy list --stage dev

# Rollback to timestamp
serverless rollback --timestamp 1234567890 --stage dev
```

---

## 🏭 Production Deployment

### Setup Production Environment

```bash
# 1. Create production secret in AWS SSM
aws ssm put-parameter \
  --name "/serverless-ai-service/shared-token" \
  --value "YOUR-SUPER-SECRET-KEY" \
  --type "SecureString" \
  --region eu-west-1

# 2. Update serverless-compose.yml prod section
# 3. Deploy to production
serverless deploy --stage prod
```

### Production Checklist

- [ ] Change JWT secret in SSM
- [ ] Set up custom domain name
- [ ] Configure ACM certificate
- [ ] Update DNS records
- [ ] Enable CloudWatch alarms
- [ ] Set up cost alerts
- [ ] Configure backup for DynamoDB
- [ ] Enable AWS WAF for API Gateway
- [ ] Set up CloudFront for website
- [ ] Configure CORS properly
- [ ] Enable API throttling
- [ ] Set up monitoring dashboard

---

## 🧹 Cleanup / Remove Deployment

### Remove All AWS Resources

```bash
cd app_redpillai

# Remove all services and resources
serverless remove --stage dev

# This deletes:
# ✅ All Lambda functions
# ✅ API Gateways
# ✅ DynamoDB tables (DATA LOSS!)
# ✅ EventBridge
# ✅ S3 deployment buckets
# ✅ CloudWatch log groups
# ✅ IAM roles

# Time: ~5 minutes
```

### Remove Shopify Theme

1. Go to Shopify Admin → Themes
2. Find SHADOWIFY theme
3. Click "Actions" → "Delete"
4. Confirm deletion

---

## 📊 Monitoring Deployment

### CloudWatch Dashboard

```bash
# Create custom dashboard
aws cloudwatch put-dashboard \
  --dashboard-name RedPillAI-Dev \
  --dashboard-body file://cloudwatch-dashboard.json \
  --region eu-west-1
```

### Cost Monitoring

```bash
# Check current month costs
aws ce get-cost-and-usage \
  --time-period Start=$(date -u +%Y-%m-01),End=$(date -u +%Y-%m-%d) \
  --granularity MONTHLY \
  --metrics UnblendedCost \
  --filter file://cost-filter.json
```

### Performance Monitoring

- **Lambda Duration:** CloudWatch Metrics
- **API Gateway Latency:** CloudWatch Metrics
- **DynamoDB Throttles:** CloudWatch Alarms
- **Bedrock Token Usage:** CloudWatch Logs

---

## ❗ Troubleshooting Deployment

### Issue: Bedrock Model Not Available

```bash
# Error: "AccessDeniedException: Could not resolve the foundation model"

# Solution:
# 1. Go to Bedrock console
# 2. Request model access
# 3. Wait for approval email
# 4. Redeploy
```

### Issue: DynamoDB Table Already Exists

```bash
# Error: "Table already exists"

# Solution:
# 1. Remove existing deployment
serverless remove --stage dev

# 2. Wait 2 minutes
# 3. Redeploy
serverless deploy --stage dev
```

### Issue: API Gateway 502 Error

```bash
# Check Lambda logs
serverless auth logs -f api --stage dev --tail

# Common causes:
# - Cold start timeout (increase timeout in serverless.yml)
# - Memory limit exceeded (increase memory)
# - Unhandled exception (check logs)
```

### Issue: CORS Error

```bash
# Add CORS headers to Lambda responses
# Update handler.js in each service to include:
# headers: {
#   'Access-Control-Allow-Origin': '*',
#   'Access-Control-Allow-Credentials': true
# }
```

---

## 📞 Support

If deployment fails:
1. Check logs: `serverless logs -f functionName --tail`
2. Verify AWS credentials: `aws sts get-caller-identity`
3. Check AWS service quotas
4. Review CloudFormation events in AWS Console

---

**Deployment Guide Version:** 1.0.0  
**Last Updated:** December 4, 2025

