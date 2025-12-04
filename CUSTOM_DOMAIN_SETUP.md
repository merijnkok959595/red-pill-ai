# Custom Domain Setup: app.redpillgpt.com

Complete guide to set up your custom domain for the Red Pill AI application.

---

## 🎯 Goal

Transform ugly AWS URLs into beautiful custom domains:

**Before:**
```
https://ldqvn0td0m.execute-api.eu-west-1.amazonaws.com
https://35nr4almb9.execute-api.eu-west-1.amazonaws.com
https://etokvqj53wptcw43i4y332vqhu0dfjmb.lambda-url.eu-west-1.on.aws/
```

**After:**
```
https://app.redpillgpt.com      ← Main application
https://api.redpillgpt.com      ← Auth & Business APIs
https://chat.redpillgpt.com     ← AI Chat (streaming)
```

---

## ✅ What's Already Done

- ✅ Route53 hosted zone created
- ✅ ACM certificate requested
- ✅ DNS validation record added
- ✅ Serverless config updated

---

## 📋 **STEP 1: Update Nameservers in Mijndomein**

### **Action Required: Update Your Domain**

1. **Login to Mijndomein:**
   - Go to: https://www.mijndomein.nl
   - Login with your credentials

2. **Find Your Domain:**
   - Navigate to: redpillgpt.com
   - Click on "DNS" or "Nameservers"

3. **Update Nameservers:**
   Replace existing nameservers with these **4 AWS nameservers**:
   
   ```
   ns-852.awsdns-42.net
   ns-1205.awsdns-22.org
   ns-1594.awsdns-07.co.uk
   ns-92.awsdns-11.com
   ```

4. **Save Changes**

5. **Wait 5-30 minutes** for DNS propagation

---

## 🔍 **STEP 2: Verify DNS Propagation**

After updating nameservers, verify they've propagated:

```bash
# Check nameservers
dig redpillgpt.com NS +short

# Expected output:
# ns-852.awsdns-42.net.
# ns-1205.awsdns-22.org.
# ns-1594.awsdns-07.co.uk.
# ns-92.awsdns-11.com.

# Or use online tool:
open https://dnschecker.org/#NS/redpillgpt.com
```

**Status Indicators:**
- 🔴 Not propagated yet (still showing old nameservers)
- 🟡 Partially propagated (some regions show new nameservers)
- 🟢 Fully propagated (all regions show AWS nameservers)

---

## ⏳ **STEP 3: Wait for Certificate Validation**

The SSL certificate is automatically validating. Check status:

```bash
# Check certificate status
aws acm describe-certificate \
  --certificate-arn arn:aws:acm:eu-west-1:218638337917:certificate/2b52c9f6-7dbc-4eed-b2bb-d0a70d6e5fab \
  --region eu-west-1 \
  --query 'Certificate.Status' \
  --output text

# Expected:
# PENDING_VALIDATION  → Wait longer
# ISSUED              → Ready to deploy!
```

**Or check in AWS Console:**
```
https://eu-west-1.console.aws.amazon.com/acm/home?region=eu-west-1#/certificates/2b52c9f6-7dbc-4eed-b2bb-d0a70d6e5fab
```

**Typical timeline:**
- Nameservers propagated: 5-30 minutes
- Certificate issued: 1-5 minutes after propagation

---

## 🚀 **STEP 4: Deploy with Custom Domain**

Once certificate shows **"ISSUED"** status:

```bash
cd /Users/merijnkok/Desktop/red-pill-ai/app_redpillai

# Deploy all services with custom domain
serverless deploy --stage dev

# This will:
# ✅ Create custom domain names in API Gateway
# ✅ Create CloudFront distribution for chat API
# ✅ Set up DNS records automatically
# ✅ Configure SSL certificates
# ✅ Deploy all Lambda functions

# Time: ~15-20 minutes (first deployment with custom domain)
```

---

## 📊 **STEP 5: Verify Deployment**

After deployment completes, your custom domains will be live!

### **Test Each Endpoint:**

#### **1. Main App (Website)**
```bash
# Should show React app
open https://app.redpillgpt.com
```

#### **2. Auth API**
```bash
# Register a user
curl -X POST https://api.redpillgpt.com/auth/register \
  -H 'Content-Type: application/json' \
  -d '{"email": "test@redpillgpt.com", "password": "TestPass123!"}'
```

#### **3. Login**
```bash
# Login and get token
curl -X POST https://api.redpillgpt.com/auth/login \
  -H 'Content-Type: application/json' \
  -d '{"email": "test@redpillgpt.com", "password": "TestPass123!"}'

# Save token
export TOKEN="eyJhbGc..."
```

#### **4. AI Chat API**
```bash
# Test AI chat
curl -N -X POST https://chat.redpillgpt.com \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer $TOKEN" \
  -d '[{"role":"user","content":[{"text":"Hello! Can you help analyze a WhatsApp chat?"}]}]'
```

---

## 🗺️ **Domain Mapping**

| Domain | Service | Purpose |
|--------|---------|---------|
| `app.redpillgpt.com` | Website (Lambda) | Main application UI |
| `api.redpillgpt.com` | Auth + Business API | Authentication, business logic |
| `chat.redpillgpt.com` | AI Chat (Lambda URL) | LLM streaming chat |
| `redpillgpt.com` | (Optional) | Can redirect to app |
| `www.redpillgpt.com` | (Optional) | Can redirect to app |

---

## 📝 **DNS Records Created**

After deployment, Route53 will have:

```
redpillgpt.com
├── NS records              → AWS nameservers
├── SOA record              → Zone authority
├── _2cec...                → Certificate validation (CNAME)
├── app.redpillgpt.com      → API Gateway (A record)
├── api.redpillgpt.com      → API Gateway (A record)
└── chat.redpillgpt.com     → CloudFront (A record)
```

View in AWS Console:
```
https://console.aws.amazon.com/route53/v2/hostedzones#ListRecordSets/Z08774443E5FNJH0UFUYR
```

---

## 🔧 **Configuration Details**

### **Route53 Hosted Zone**
- **ID:** Z08774443E5FNJH0UFUYR
- **Domain:** redpillgpt.com
- **Region:** Global
- **Cost:** $0.50/month

### **ACM Certificate**
- **ARN:** `arn:aws:acm:eu-west-1:218638337917:certificate/2b52c9f6-7dbc-4eed-b2bb-d0a70d6e5fab`
- **Domain:** redpillgpt.com
- **SANs:** *.redpillgpt.com (wildcard)
- **Region:** eu-west-1
- **Cost:** Free

### **Custom Domain Settings**
- **customDomainNameEnabled:** true
- **customDomainName:** redpillgpt.com
- **customDomainCertificateARN:** (see above)

---

## 🛠️ **Troubleshooting**

### **Issue: Nameservers not updating**

**Symptom:** DNS checker still shows old nameservers after 30+ minutes

**Solution:**
1. Clear DNS cache: `sudo killall -HUP mDNSResponder` (Mac)
2. Check Mijndomein dashboard - confirm changes saved
3. Contact Mijndomein support if stuck

### **Issue: Certificate stuck in PENDING_VALIDATION**

**Symptom:** Certificate not issuing after 30+ minutes

**Solution:**
1. Verify nameservers propagated first
2. Check validation record exists in Route53
3. Wait up to 72 hours (usually faster)
4. Try requesting new certificate if truly stuck

### **Issue: Custom domain returns 404**

**Symptom:** `https://app.redpillgpt.com` shows 404 error

**Solution:**
1. Wait 10-20 minutes for CloudFront propagation
2. Check API Gateway custom domain status in AWS Console
3. Verify DNS records created
4. Clear browser cache

### **Issue: SSL certificate error**

**Symptom:** Browser shows "Certificate invalid" warning

**Solution:**
1. Wait for CloudFront distribution to fully deploy (~15 min)
2. Hard refresh browser (Cmd+Shift+R)
3. Check certificate matches domain in browser

---

## 🔄 **Future Updates**

When you need to redeploy:

```bash
cd app_redpillai

# Redeploy all services (keeps custom domain)
serverless deploy --stage dev

# Redeploy single service
serverless auth deploy --stage dev
```

**Custom domain persists** across deployments! ✅

---

## 💡 **Optional: Add Root Domain Redirect**

Want `redpillgpt.com` (without subdomain) to redirect to `app.redpillgpt.com`?

```bash
# Create S3 redirect bucket
aws s3 mb s3://redpillgpt.com --region eu-west-1
aws s3 website s3://redpillgpt.com \
  --index-document index.html \
  --error-document error.html

# Add CloudFront distribution
# (Full instructions in separate guide)
```

---

## 📊 **DNS Propagation Checker**

Use these tools to monitor propagation:

- **DNSChecker:** https://dnschecker.org
- **WhatsMyDNS:** https://www.whatsmydns.net
- **DNS Propagation:** https://www.dnspropagation.net

---

## ✅ **Checklist**

- [ ] Update nameservers in Mijndomein
- [ ] Wait for DNS propagation (5-30 min)
- [ ] Verify nameservers with `dig` command
- [ ] Wait for certificate to show "ISSUED" status
- [ ] Deploy with custom domain: `serverless deploy`
- [ ] Test all endpoints
- [ ] Update documentation with new URLs
- [ ] Update Shopify theme with new API URLs (if needed)

---

## 📞 **Support**

**AWS Console Links:**
- Route53: https://console.aws.amazon.com/route53
- ACM: https://console.aws.amazon.com/acm
- CloudFormation: https://console.aws.amazon.com/cloudformation

**Mijndomein Support:**
- Website: https://www.mijndomein.nl
- Help: https://www.mijndomein.nl/klantenservice

---

## 💰 **Cost Breakdown**

| Service | Cost | Billing |
|---------|------|---------|
| Route53 Hosted Zone | $0.50/month | Per zone |
| Route53 Queries | $0.40/1M queries | Per million |
| ACM Certificate | **FREE** | Always free |
| API Gateway Custom Domain | **FREE** | Included |
| CloudFront (Chat API) | $0.085/GB | Data transfer |

**Estimated total:** ~$1-2/month for custom domains

---

**Setup Guide Version:** 1.0  
**Created:** December 4, 2025  
**Domain:** redpillgpt.com  
**Status:** ⏳ Awaiting nameserver update

