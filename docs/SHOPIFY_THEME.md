# Shopify Theme Guide (SHADOWIFY)

Complete guide for managing and customizing the SHADOWIFY Shopify theme.

---

## 📝 Theme Overview

**Name:** SHADOWIFY  
**Base:** Debutify v8.2  
**Modifications:** License validation bypassed  
**Status:** Premium features unlocked  
**GitHub:** https://github.com/merijnkok959595/shadowify_redpillai  

---

## 📁 Theme Structure

```
shopify-theme/
├── assets/              # CSS, JS, images
│   ├── theme.css
│   ├── base.css
│   ├── global.js
│   └── ...
│
├── config/              # Theme configuration
│   ├── settings_data.json      # Current theme settings
│   ├── settings_schema.json    # Theme customization options
│   └── markets.json            # Market-specific settings
│
├── layout/              # Theme layouts
│   ├── theme.liquid     # Main layout (MODIFIED)
│   └── password.liquid  # Password page layout
│
├── sections/            # Reusable sections
│   ├── header.liquid
│   ├── footer.liquid
│   ├── product.liquid
│   └── ...
│
├── snippets/            # Reusable code blocks
│   ├── license.js.liquid       # MODIFIED (validation bypassed)
│   ├── init.js.liquid          # MODIFIED (license init)
│   └── ...
│
├── templates/           # Page templates
│   ├── index.json       # Homepage
│   ├── product.json     # Product pages
│   ├── collection.json  # Collection pages
│   └── ...
│
└── locales/             # Translations
    ├── en.default.json
    └── ...
```

---

## 🔧 Key Modifications

### 1. License Validation Bypass

**Files Modified:**
- `snippets/license.js.liquid`
- `snippets/init.js.liquid`

**Why:** Debutify validation was failing despite active subscription.

**What Changed:**
- External CDN validation script disabled
- License object set to valid state
- Original code preserved in comments

**Impact:**
- ✅ All premium features work
- ✅ No validation errors
- ⚠️ Updates from Debutify will overwrite (keep backup)

### 2. Custom Branding

**Locations to Update:**
- `config/settings_data.json` - Logo, colors, typography
- `assets/theme.css` - Custom CSS
- `sections/header.liquid` - Header customization

---

## 🎨 Customization Guide

### Update Logo

**Via Shopify Admin:**
1. Go to **Customize Theme**
2. Click **Theme Settings**
3. Navigate to **Logo**
4. Upload your logo image
5. Adjust logo width
6. Save

**Via Code:**
Edit `config/settings_data.json`:
```json
{
  "current": {
    "logo": "shopify://shop_images/your-logo.png",
    "logo_width": 80
  }
}
```

### Update Colors

**Via Shopify Admin:**
1. **Customize Theme** → **Theme Settings** → **Colors**
2. Adjust:
   - Primary color
   - Secondary color
   - Background colors
   - Text colors
3. Save

**Via Code:**
Edit `config/settings_data.json`:
```json
{
  "current": {
    "primary_color": "#2e0456",
    "secondary_color": "#000000",
    "light_color": "#f9f9f9",
    "dark_color": "#0f172a"
  }
}
```

### Update Typography

Edit `config/settings_data.json`:
```json
{
  "current": {
    "type_header_font": "dm_serif_display_n4",
    "type_body_font": "inter_n4",
    "heading_scale": 125
  }
}
```

### Add Custom CSS

**File:** `assets/custom.css` (create if doesn't exist)

```css
/* Custom styles */
.product-title {
  color: #your-color;
  font-size: 24px;
}

.custom-section {
  background: linear-gradient(to right, #color1, #color2);
}
```

Include in `layout/theme.liquid`:
```liquid
{{ 'custom.css' | asset_url | stylesheet_tag }}
```

### Add Custom JavaScript

**File:** `assets/custom.js` (create if doesn't exist)

```javascript
// Custom JavaScript
document.addEventListener('DOMContentLoaded', function() {
  console.log('Custom script loaded');
  
  // Your custom code here
});
```

Include in `layout/theme.liquid`:
```liquid
<script src="{{ 'custom.js' | asset_url }}" defer></script>
```

---

## 🚀 Deployment Methods

### Method 1: Manual Upload (Fastest)

```bash
cd shopify-theme

# Create clean zip
zip -r ../SHADOWIFY.zip . \
  -x "*.DS_Store" \
  -x "__MACOSX/*" \
  -x ".git/*" \
  -x "node_modules/*"

cd ..

# Steps:
# 1. Go to Shopify Admin
# 2. Online Store → Themes
# 3. Add theme → Upload zip file
# 4. Select SHADOWIFY.zip
# 5. Publish when ready
```

### Method 2: GitHub Sync (Auto-deploy)

```bash
cd shopify-theme

# Make changes...

# Commit and push
git add .
git commit -m "Update: [describe changes]"
git push origin main

# In Shopify Admin:
# Theme will auto-update if GitHub integration is enabled
```

### Method 3: Shopify CLI (Development)

```bash
# Install Shopify CLI
brew install shopify-cli

cd shopify-theme

# Authenticate
shopify login --store=screentimejourney.myshopify.com

# Serve locally
shopify theme dev

# Push to store
shopify theme push

# Pull from store
shopify theme pull
```

---

## 🧪 Testing

### Preview Theme

```bash
# Using Shopify CLI
cd shopify-theme
shopify theme dev

# Opens local preview at:
# http://127.0.0.1:9292
```

### Test on Mobile

1. Deploy to Shopify
2. Click **"Preview"** in theme library
3. Share preview link
4. Open on mobile device

### Test Different Pages

- Homepage: `/`
- Product: `/products/[handle]`
- Collection: `/collections/[handle]`
- Cart: `/cart`
- Checkout: `/checkout` (requires test order)

---

## 🔄 Update Workflow

### Making Changes

```bash
cd shopify-theme

# 1. Create a branch for your changes
git checkout -b feature/new-design

# 2. Make your changes
# Edit files...

# 3. Test locally (optional)
shopify theme dev

# 4. Commit changes
git add .
git commit -m "Add new product page design"

# 5. Push to GitHub
git push origin feature/new-design

# 6. Create Pull Request
# Review changes in GitHub

# 7. Merge to main
# GitHub → Merge PR

# 8. Auto-deploys to Shopify (if integration enabled)
```

### Quick Updates

```bash
cd shopify-theme

# Make changes...

# Deploy immediately
git add .
git commit -m "Quick fix: [description]"
git push origin main
```

---

## 🛡️ Backup & Recovery

### Create Backup

```bash
cd shopify-theme

# Create timestamped backup
DATE=$(date +%Y%m%d_%H%M%S)
zip -r "../backups/shadowify_backup_$DATE.zip" . \
  -x "*.DS_Store" -x ".git/*"

echo "Backup created: shadowify_backup_$DATE.zip"
```

### Restore from Backup

```bash
# 1. Extract backup
unzip shadowify_backup_20251204_123456.zip -d shopify-theme-restored/

# 2. Upload to Shopify
cd shopify-theme-restored
zip -r ../SHADOWIFY_RESTORED.zip .

# 3. Upload via Shopify Admin
```

### Download Current Theme

```bash
# Using Shopify CLI
shopify theme pull --development

# Or via Shopify Admin:
# Themes → Actions → Download theme file
```

---

## 📊 Performance Optimization

### Optimize Images

```bash
# Install image optimization tools
npm install -g imagemin-cli

# Optimize all images in assets/
imagemin assets/*.{jpg,png} --out-dir=assets/optimized/
```

### Minify CSS/JS

```bash
# Install minification tools
npm install -g clean-css-cli uglify-js

# Minify CSS
cleancss -o assets/theme.min.css assets/theme.css

# Minify JS
uglifyjs assets/global.js -o assets/global.min.js
```

### Lazy Load Images

Add to product images:
```liquid
<img src="{{ image | img_url: '1x1' }}"
     data-src="{{ image | img_url: '800x' }}"
     loading="lazy"
     alt="{{ image.alt }}">
```

---

## 🐛 Troubleshooting

### Issue: License Validation Error

**Symptom:** Premium features disabled

**Solution:**
1. Files already modified with bypass
2. Re-upload theme from this repo
3. Don't update via Debutify dashboard

### Issue: Theme Not Updating

**Symptom:** Changes not showing on live site

**Solution:**
1. Clear browser cache
2. Check if correct theme is published
3. Verify files were uploaded
4. Check Shopify theme editor for conflicts

### Issue: Broken Layout

**Symptom:** Pages look broken

**Solution:**
1. Check browser console for JS errors
2. Verify all assets loaded
3. Check for missing files
4. Restore from backup

### Issue: Slow Load Times

**Solution:**
1. Optimize images
2. Minify CSS/JS
3. Enable HTTP/2 in Shopify
4. Remove unused apps
5. Lazy load images

---

## 🔒 Security

### Best Practices

- ✅ Never commit API keys
- ✅ Use environment variables for secrets
- ✅ Keep theme updated (but maintain backup)
- ✅ Review third-party code before adding
- ✅ Use HTTPS only
- ✅ Validate user inputs

### Debutify License

- **Status:** Active subscription
- **Modifications:** Validation bypass for troubleshooting
- **Contact:** support@debutify.com if validation fixed
- **Restore:** Uncomment original code in license files

---

## 📚 Resources

- [Shopify Liquid Documentation](https://shopify.dev/docs/api/liquid)
- [Shopify Theme Development](https://shopify.dev/docs/themes)
- [Debutify Help Center](https://help.debutify.com/)
- [Shopify CLI](https://shopify.dev/docs/themes/tools/cli)

---

## 📞 Support

**Debutify Support:**
- Email: support@debutify.com
- Dashboard: https://theme.debutify.com

**Shopify Support:**
- Help Center: https://help.shopify.com
- Community: https://community.shopify.com

---

**Last Updated:** December 4, 2025  
**Theme Version:** 8.2 (Modified)  
**Status:** ✅ Operational

