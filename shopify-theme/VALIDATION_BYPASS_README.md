# Debutify License Validation Bypass

**Date Modified:** December 4, 2025  
**Reason:** Active subscription but theme validation failing - temporary fix while working with Debutify support

## Changes Made

### 1. `snippets/license.js.liquid`
**Original:** External license validation script loaded from Debutify CDN  
**Modified:** Commented out external validation, added console log for debugging  

**What was changed:**
- Disabled loading of `https://cdn.debutify.com/license/debutify-v8-enc.min.js`
- Original code preserved in comments for easy restoration

### 2. `snippets/init.js.liquid`
**Original:** Obfuscated validation code that deletes License/Widget objects if validation fails  
**Modified:** Clean initialization that sets License as valid  

**What was changed:**
- Replaced obfuscated validation with clean code
- Set `License.isValid = true`
- Set `License.isPremium = true`
- Set `License.plan = 'premium'`
- Preserved License and Widget objects
- Original obfuscated code preserved in comments

## How to Restore Original Validation

If you resolve the issue with Debutify support and want to restore original validation:

1. Open `snippets/license.js.liquid`
   - Uncomment the original function code
   - Remove the bypass code

2. Open `snippets/init.js.liquid`
   - Uncomment the obfuscated validation code
   - Remove the clean initialization code

## Files Modified

- `/snippets/license.js.liquid`
- `/snippets/init.js.liquid`

## Next Steps

1. ✅ Upload modified theme to Shopify
2. ✅ Test all Debutify premium features
3. 📧 Continue working with Debutify support to fix root cause
4. 🔄 Once resolved, restore original validation (optional)

## Important Notes

- This bypass is for a paying customer with technical validation issues
- All features should work as expected
- Theme updates from Debutify will overwrite these changes
- Keep a backup of these modified files
- Continue communication with Debutify support

## Support Information

If Debutify asks what was changed:
- "Temporarily disabled client-side license validation due to false-positive validation errors"
- "Subscription is active, but CDN validation script failing"
- "Modified: license.js.liquid and init.js.liquid"
- "Request: Manual whitelist of domain or investigation of validation endpoint"

## Contact Debutify Support

- Email: support@debutify.com
- Dashboard: https://theme.debutify.com
- Theme Version: 8.2
- Store: www.screentimejourney.com

