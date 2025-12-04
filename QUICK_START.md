# 🚀 Quick Start Guide

Get up and running in 5 minutes!

---

## 📁 Folder Structure

```
red-pill-ai/
├── shopify-theme/     # Shopify storefront theme
├── app_redpillai/     # AWS serverless backend  
├── data/              # WhatsApp chat samples
└── docs/              # Detailed documentation
```

---

## ⚡ Quick Deploy

### 1. Shopify Theme

```bash
cd shopify-theme
zip -r ../SHADOWIFY.zip . -x "*.DS_Store"
```
→ Upload to Shopify Admin → Themes → Add theme

### 2. AWS Backend

```bash
cd app_redpillai
npm install
serverless deploy --stage dev
```
→ Save the URLs from output

---

## 🔗 Live URLs

After deployment, your app will be available at:

- **Shopify Store:** https://screentimejourney.com
- **AWS App:** https://[your-id].execute-api.eu-west-1.amazonaws.com
- **GitHub Theme:** https://github.com/merijnkok959595/shadowify_redpillai
- **GitHub App:** https://github.com/merijnkok959595/app_redpillai

---

## 📚 Full Documentation

- **Main README:** [README.md](README.md)
- **Deployment Guide:** [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md)
- **Shopify Theme:** [docs/SHOPIFY_THEME.md](docs/SHOPIFY_THEME.md)

---

## 🔄 Update & Push

### Theme Changes
```bash
cd shopify-theme
# Make changes...
git add . && git commit -m "Update" && git push
```

### Backend Changes  
```bash
cd app_redpillai
# Make changes...
serverless deploy --stage dev
git add . && git commit -m "Update" && git push
```

---

## 🆘 Help

**Common Commands:**
```bash
# Deploy backend
cd app_redpillai && serverless deploy

# Remove backend
cd app_redpillai && serverless remove

# View logs
serverless auth logs -f api --tail

# Package theme
cd shopify-theme && zip -r ../SHADOWIFY.zip .
```

**Troubleshooting:**
- Check [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md#troubleshooting)
- View AWS CloudWatch logs
- Check Shopify theme editor

---

**Last Updated:** Dec 4, 2025

