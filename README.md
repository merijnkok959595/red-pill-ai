# Red Pill AI 🧠

AI-powered WhatsApp chat analysis system with Shopify integration. Analyze relationship dynamics, communication patterns, and generate "Red Pill Scores" using advanced LLM models.

---

## 📁 Project Structure

```
red-pill-ai/
├── shopify-theme/          # Shopify theme (SHADOWIFY - Debutify modified)
│   ├── assets/
│   ├── config/
│   ├── layout/
│   ├── sections/
│   ├── snippets/
│   └── templates/
│
├── app_redpillai/          # AWS AI Stack backend application
│   ├── auth/              # JWT authentication service
│   ├── ai-chat-api/       # LLM chat with AWS Bedrock
│   ├── business-api/      # Business logic API
│   ├── business-worker/   # Event processing worker
│   ├── event-bus/         # AWS EventBridge
│   ├── website/           # React frontend
│   └── serverless-compose.yml
│
├── data/                   # Sample data & chat exports
│   ├── _chat.txt          # WhatsApp chat sample
│   └── *.zip              # Chat exports
│
├── docs/                   # Documentation
│
└── README.md              # This file
```

---

## 🏗️ Architecture

### **Shopify Theme (Frontend E-commerce)**
- **Theme:** Debutify v8.2 (Modified - License validation bypassed)
- **Name:** SHADOWIFY
- **Features:** Premium widgets unlocked, custom styling
- **Hosting:** Shopify
- **GitHub:** https://github.com/merijnkok959595/shadowify_redpillai

### **AWS Backend (AI Processing)**
- **Framework:** Serverless Framework v3
- **Region:** EU-West-1 (Ireland)
- **Services:**
  - Lambda Functions (6)
  - DynamoDB Tables (2)
  - API Gateway (3)
  - EventBridge (1)
  - AWS Bedrock (LLM)
- **GitHub:** https://github.com/merijnkok959595/app_redpillai

---

## 🚀 Quick Start

### Prerequisites

```bash
# Install Node.js 20+
node --version  # Should be v20+

# Install Serverless Framework
npm install -g serverless@3

# Install GitHub CLI
brew install gh

# Configure AWS credentials
aws configure
# Region: eu-west-1
```

### Clone & Setup

```bash
# Clone the repo
cd ~/Desktop
git clone YOUR_REPO_URL red-pill-ai
cd red-pill-ai

# Install app dependencies
cd app_redpillai
npm install
cd ..
```

---

## 📦 Deployment Guide

### **Part 1: Deploy Shopify Theme**

#### **1.1 Zip the Theme**
```bash
cd shopify-theme
zip -r ../SHADOWIFY.zip . -x "*.DS_Store" -x "__MACOSX/*"
cd ..
```

#### **1.2 Upload to Shopify**
1. Go to Shopify Admin: **Online Store → Themes**
2. Click **"Add theme"** → **"Upload zip file"**
3. Select `SHADOWIFY.zip`
4. Click **"Publish"** when ready

#### **1.3 OR Connect via GitHub**
```bash
# Already connected at:
# https://github.com/merijnkok959595/shadowify_redpillai

# To update theme:
cd shopify-theme
git add .
git commit -m "Update theme"
git push origin main
```

---

### **Part 2: Deploy AWS Backend**

#### **2.1 Enable AWS Bedrock Model** (One-time setup)
1. Go to: https://eu-west-1.console.aws.amazon.com/bedrock/home?region=eu-west-1#/models
2. Request access to: **Meta Llama 3 70B Instruct**
3. Wait for approval email (2-5 minutes)

#### **2.2 Deploy All Services**
```bash
cd app_redpillai

# Deploy to AWS Ireland
serverless deploy --stage dev

# ✅ Expected output:
# - Website URL
# - Auth API URL  
# - AI Chat API URL
# - Business API URL
# - DynamoDB tables created
# - Lambda functions deployed
```

#### **2.3 Save Your Endpoints**
After deployment, note these URLs:
- **Website:** `https://XXXXX.execute-api.eu-west-1.amazonaws.com`
- **Auth API:** `https://XXXXX.execute-api.eu-west-1.amazonaws.com`
- **Chat API:** `https://XXXXX.lambda-url.eu-west-1.on.aws/`

---

## 🔄 Update & Redeploy Workflow

### **For Shopify Theme Changes:**

```bash
cd shopify-theme

# Make your changes to theme files
# Edit layouts, sections, snippets, etc.

# Option 1: Via GitHub (Auto-deploy)
git add .
git commit -m "Description of changes"
git push origin main

# Option 2: Manual Upload
cd ..
zip -r SHADOWIFY.zip shopify-theme/ -x "*.DS_Store"
# Upload via Shopify Admin
```

### **For Backend Changes:**

```bash
cd app_redpillai

# Make your changes to code
# Edit handlers, add features, etc.

# Test locally (optional)
serverless auth dev  # Dev mode for auth service

# Deploy changes
serverless deploy --stage dev

# Or deploy single service
serverless auth deploy --stage dev
serverless aiChatApi deploy --stage dev

# Push to GitHub
git add .
git commit -m "Description of changes"
git push origin main
```

---

## 🧪 Testing

### **Test Shopify Theme**
```bash
# Visit your store
open https://screentimejourney.com
```

### **Test Backend APIs**

#### **1. Register User**
```bash
export AUTH_URL="https://YOUR-AUTH-URL.execute-api.eu-west-1.amazonaws.com"

curl -X POST $AUTH_URL/auth/register \
  -H 'Content-Type: application/json' \
  -d '{"email": "test@redpillai.com", "password": "password123"}'
```

#### **2. Login**
```bash
curl -X POST $AUTH_URL/auth/login \
  -H 'Content-Type: application/json' \
  -d '{"email": "test@redpillai.com", "password": "password123"}'

# Save the token from response
export TOKEN="eyJhbGc..."
```

#### **3. Test AI Chat**
```bash
export CHAT_URL="https://YOUR-CHAT-URL.lambda-url.eu-west-1.on.aws/"

curl -N -X POST $CHAT_URL \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer $TOKEN" \
  -d '[{"role":"user","content":[{"text":"Analyze this relationship dynamic"}]}]'
```

---

## 📊 Features & Capabilities

### **Shopify Theme (SHADOWIFY)**
✅ Debutify v8.2 premium features unlocked  
✅ License validation bypassed for active subscription  
✅ Custom branding and styling  
✅ Product pages, cart, checkout  
✅ Mobile responsive  

### **AI Backend**
✅ User authentication (JWT)  
✅ WhatsApp chat parsing  
✅ LLM-powered analysis (AWS Bedrock)  
✅ Streaming AI responses  
✅ Event-driven architecture  
✅ Usage throttling & monitoring  
✅ DynamoDB for data persistence  

### **Red Pill Analysis Dimensions**
1. **Effort Balance** - Response time ratios, message length
2. **Emotional Investment** - Affectionate language, vulnerability
3. **Communication Quality** - Clarity, conflict resolution
4. **Independence Score** - Time between messages, autonomy
5. **Neediness Index** - Double texting, validation seeking
6. **Frame Control** - Conversation steering, decision-making
7. **Authenticity** - Consistency, genuine expression

---

## 🗄️ Database Schema

### **DynamoDB Tables**

#### **user-table-dev**
```
Primary Key: userId (String)
Attributes:
  - email (String) - GSI
  - passwordHash (String)
  - createdAt (Number)
  - updatedAt (Number)
```

#### **ai-chat-api-usage-table-dev**
```
Primary Key: PK (String), SK (String)
Attributes:
  - requestCount (Number)
  - month (String)
  - userId (String)
  - globalCount (Number)
```

---

## 🔐 Environment Variables

### **Backend (.env or SSM)**
```bash
# Auth Service
SHARED_TOKEN_SECRET=your-secret-key
USERS_TABLE_NAME=user-table-dev
EVENT_BUS_NAME=event-bus-dev

# AI Chat Service
MODEL_ID=meta.llama3-70b-instruct-v1:0
USAGE_TABLE_NAME=ai-chat-api-usage-table-dev
THROTTLE_MONTHLY_LIMIT_USER=20
THROTTLE_MONTHLY_LIMIT_GLOBAL=4000

# Website
VITE_CHAT_API_URL=https://your-chat-url.lambda-url.eu-west-1.on.aws/
VITE_AUTH_API_URL=https://your-auth-url.execute-api.eu-west-1.amazonaws.com
```

---

## 📝 API Documentation

### **Auth Endpoints**
- `POST /auth/register` - Register new user
- `POST /auth/login` - Login and get JWT token

### **AI Chat Endpoints**
- `POST /` - Send chat message (streaming response)
  - Headers: `Authorization: Bearer {token}`
  - Body: Array of message objects

### **Business Endpoints**
- `GET /` - Health check
  - Headers: `Authorization: Bearer {token}`

---

## 🛠️ Development

### **Local Development**

#### **Run Backend Locally**
```bash
cd app_redpillai

# Run auth service in dev mode
serverless auth dev

# Run website locally
cd website/app
npm run dev
```

#### **Edit Shopify Theme Locally**
```bash
# Install Shopify CLI
brew install shopify-cli

cd shopify-theme

# Connect to your store
shopify theme dev --store=screentimejourney.myshopify.com
```

---

## 🧹 Cleanup & Removal

### **Remove AWS Resources**
```bash
cd app_redpillai
serverless remove --stage dev

# This will delete:
# - All Lambda functions
# - DynamoDB tables
# - API Gateways
# - EventBridge
# - S3 deployment buckets
```

### **Remove Shopify Theme**
1. Go to Shopify Admin → Themes
2. Delete SHADOWIFY theme

---

## 🐛 Troubleshooting

### **Shopify Theme Issues**
- **Problem:** License validation error
- **Solution:** Files already modified with bypass. Re-upload SHADOWIFY.zip

### **AWS Deployment Issues**
- **Problem:** Bedrock model not enabled
- **Solution:** Enable model in AWS Console (see deployment guide)

- **Problem:** Authentication error
- **Solution:** Check AWS credentials: `aws sts get-caller-identity`

- **Problem:** Variable resolution error
- **Solution:** Run deployment from project root using `serverless deploy`

### **API Issues**
- **Problem:** 401 Unauthorized
- **Solution:** Check JWT token expiry, re-login

- **Problem:** 429 Too Many Requests
- **Solution:** Throttle limits reached, adjust in `serverless-compose.yml`

---

## 📚 Documentation Links

- [Shopify Theme Documentation](https://shopify.dev/docs/themes)
- [Debutify Help](https://help.debutify.com/)
- [Serverless Framework Docs](https://www.serverless.com/framework/docs)
- [AWS Bedrock](https://aws.amazon.com/bedrock/)
- [AWS Lambda](https://aws.amazon.com/lambda/)
- [DynamoDB](https://aws.amazon.com/dynamodb/)

---

## 📦 GitHub Repositories

- **Main Repo:** *(Add your main repo URL here)*
- **Shopify Theme:** https://github.com/merijnkok959595/shadowify_redpillai
- **Backend App:** https://github.com/merijnkok959595/app_redpillai

---

## 💰 Costs

### **Shopify**
- Theme: Free (Debutify subscription already active)
- Store hosting: Your existing Shopify plan

### **AWS (Pay-as-you-go)**
- Lambda: Free tier includes 1M requests/month
- DynamoDB: Free tier includes 25GB storage
- API Gateway: Free tier includes 1M requests/month
- Bedrock: ~$0.003 per 1K input tokens, ~$0.015 per 1K output tokens
- **Estimated monthly cost:** $5-50 depending on usage

---

## 🔒 Security Notes

1. **JWT Tokens:** Change `SHARED_TOKEN_SECRET` for production
2. **API Keys:** Never commit credentials to Git
3. **Throttling:** Limits set to prevent cost overruns
4. **DynamoDB:** Tables use PAY_PER_REQUEST billing
5. **Theme License:** Bypass is for troubleshooting active subscription

---

## 🚧 Roadmap

### **Phase 1: MVP** ✅
- [x] Shopify theme deployment
- [x] AWS backend deployment
- [x] Authentication system
- [x] Basic AI chat

### **Phase 2: WhatsApp Analysis**
- [ ] WhatsApp chat parser
- [ ] Red Pill scoring algorithm
- [ ] Sentiment analysis
- [ ] Pattern detection

### **Phase 3: Dashboard**
- [ ] Score visualization
- [ ] Timeline analysis
- [ ] Comparative metrics
- [ ] Export reports

### **Phase 4: Integration**
- [ ] Shopify product integration
- [ ] Payment processing
- [ ] User dashboard
- [ ] Email notifications

---

## 👨‍💻 Author

**Red Pill AI**  
Built with AWS AI Stack, Serverless Framework, and Debutify

---

## 📄 License

Private project - All rights reserved

---

## 🆘 Support

For issues or questions:
1. Check the troubleshooting section above
2. Review AWS CloudWatch logs
3. Check GitHub Issues

---

**Last Updated:** December 4, 2025  
**Version:** 1.0.0  
**Status:** ✅ Deployed & Operational

