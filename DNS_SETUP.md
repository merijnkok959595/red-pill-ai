# DNS Setup for redpillgpt.com

## Step 1: Update Nameservers in Mijndomein

Go to your Mijndomein control panel and update the nameservers to:

```
ns-852.awsdns-42.net
ns-1205.awsdns-22.org
ns-1594.awsdns-07.co.uk
ns-92.awsdns-11.com
```

### Instructions:
1. Login to: https://www.mijndomein.nl
2. Go to your domain: redpillgpt.com
3. Find DNS/Nameserver settings
4. Replace existing nameservers with the 4 above
5. Save changes
6. Wait 5-10 minutes for propagation

---

## Step 2: Verify DNS Propagation

```bash
# Check if nameservers updated
dig redpillgpt.com NS +short

# Should show:
# ns-852.awsdns-42.net.
# ns-1205.awsdns-22.org.
# ns-1594.awsdns-07.co.uk.
# ns-92.awsdns-11.com.
```

---

## Route53 Hosted Zone Details

- **Hosted Zone ID:** Z08774443E5FNJH0UFUYR
- **Domain:** redpillgpt.com
- **Status:** Active
- **AWS Console:** https://console.aws.amazon.com/route53/v2/hostedzones#ListRecordSets/Z08774443E5FNJH0UFUYR

---

## Subdomains to Create

After nameservers propagate, we'll create:

- `app.redpillgpt.com` → AWS Lambda (Main app)
- `api.redpillgpt.com` → API Gateway (Auth + Business APIs)
- `chat.redpillgpt.com` → Lambda URL (AI Chat with streaming)
- `www.redpillgpt.com` → Redirect to app or Shopify

---

**Created:** December 4, 2025

