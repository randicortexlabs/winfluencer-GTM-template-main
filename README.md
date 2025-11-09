# Winfluencer Pixel Tag for Google Tag Manager

**Container Type:** WEB  
**Container Contexts:** WEB  


Track page views, purchases, leads, and add-to-cart events with optional first-party routing via a CNAME endpoint. The template collects standard UTM parameters and Winfluencer IDs for campaign attribution and supports optional debug logging.

---

## Table of Contents

• [Quick Start](#quick-start)  
• [Installation](#installation)  
• [Configuration](#configuration)  
• [Field Reference](#field-reference)  
• [Example Setups](#example-setups)  
• [Data Layer Examples](#data-layer-examples)  
• [Attribution Parameters](#attribution-parameters)  
• [CNAME Setup](#cname-setup)  
• [Permissions](#permissions)  
• [Consent & Privacy](#consent--privacy)  
• [Security](#security)  
• [Troubleshooting](#troubleshooting)  
• [Versioning & Updates](#versioning--updates)  
• [Documentation & Support](#documentation--support)

---

## Quick Start

1. In GTM go to **Templates → Search Gallery**, add **Winfluencer Pixel Tag**
2. **Create a New Tag:**
   - **Pixel ID** (required): `wi-123456` format
   - **CNAME endpoint** (optional): hostname only (e.g., `win.yourbrand.com`)
     - If not set, the default endpoint is used
   - **Event type**: `pageView`, `purchase`, `lead`, or `add_to_cart`
   - **(Optional)** Custom Parameters table (e.g., `transaction_id`, `value`, `currency`)
3. Add a trigger (e.g., All Pages for page views) and use Preview to test

---

## Installation

### Option A: Install from GTM Community Template Gallery (Recommended)

1. In Google Tag Manager, navigate to **Templates** in the left sidebar
2. Click **Search Gallery** button (top right)
3. Search for "**Winfluencer Pixel Tag**"
4. Click on the template card
5. Click **Add to workspace**
6. The template is now available in your workspace

### Option B: Manual Import

1. Download the `template.tpl` file from [GitHub](https://github.com/randicortexlabs/winfluencer-gtm-template)
2. In GTM, navigate to **Templates → Tag Templates → New**
3. Click the **⋮** menu (top right) → **Import**
4. Select the downloaded `template.tpl` file
5. Click **Save**

---

## Configuration

### Creating a Tag

1. In GTM, navigate to **Tags → New**
2. Click **Tag Configuration**
3. Select **Winfluencer Pixel Tag**
4. Configure the required and optional fields (see [Field Reference](#field-reference))
5. Add an appropriate trigger
6. Save and test in Preview mode

---

## Field Reference

### Required Fields

#### Pixel ID

**Format:** `wi-[0-9]+` (e.g., `wi-123456`)

**Description:** Your unique Winfluencer tracking identifier

**Where to find:** Contact randi@cortexlabs.digital to obtain your Pixel ID

**Validation:**
- ✅ **Correct:** `wi-123456`
- ❌ **Incorrect:** `123456` (missing prefix)
- ❌ **Incorrect:** `wi-abc123` (contains letters)

---

#### Event Type

**Description:** The type of user action to track

**Available options:**

| Value | Description | Typical Use |
|-------|-------------|-------------|
| `pageView` | Page navigation | Every page load |
| `purchase` | Completed transaction | Order confirmation |
| `lead` | Form submission/signup | Lead generation |
| `add_to_cart` | Product added to cart | E-commerce funnel |

---

### Optional Fields

#### CNAME Endpoint

**Format:** Hostname only (no `https://`, no path)

**Examples:**
- ✅ **Correct:** `win.yourbrand.com`
- ❌ **Incorrect:** `https://win.yourbrand.com`
- ❌ **Incorrect:** `win.yourbrand.com/collect`

**Description:** Enables first-party tracking by routing via your hostname (subject to browser policies and your DNS setup)

**Benefits:**
- Reduced likelihood of ad blocker interference
- Tracking data routed through your domain
- Better compatibility with privacy policies

**If not configured:** The default endpoint (`api.winflncr.com`) is used

---

#### Custom Parameters

**Description:** Additional key-value pairs sent with each event

**Format:** Table with two columns: Parameter Name and Parameter Value

**⚠️ Privacy Warning:** Do not include personally identifiable information (PII) such as email addresses, phone numbers, or names without hashing them first. See [Privacy Best Practices](#privacy-best-practices) for guidance.

**Parameter mapping:** Parameter names on the left (e.g., `transaction_id`) are what the Winfluencer endpoint expects. GTM variables on the right (e.g., `{{Transaction ID}}`) read from your dataLayer keys. Create Data Layer Variables in GTM that match your data layer structure.

**Common parameters:**

| Parameter Name | Description | Example GTM Variable |
|----------------|-------------|----------------------|
| `transaction_id` | Unique order ID | `{{Transaction ID}}` (reads `transactionId` from data layer) |
| `value` | Transaction amount | `{{Order Total}}` (reads `transactionTotal` from data layer) |
| `currency` | Currency code | `USD` (static) or `{{Currency Code}}` (reads `transactionCurrency`) |
| `product_ids` | Product identifiers | `{{Product IDs}}` (reads `productIds` from data layer) |
| `form_name` | Form identifier | `{{Form Name}}` (reads `formName` from data layer) |
| `lead_type` | Type of lead | `contact_form` (static value) |

**Using GTM Variables:** Reference any GTM variable using `{{Variable Name}}` syntax

---

#### Enable Debug Mode

**Description:** Writes diagnostic logs to the browser console for troubleshooting

**When to enable:**
- During initial setup
- When troubleshooting issues
- In development/staging environments

**When to disable:**
- Production environments (for performance)

**Console output example:**
```
[GTM Template Debug] GTM Template loaded
[GTM Template Debug] Configuration loaded
[GTM Template Debug] SUCCESS: Both init and track completed
```

---

## Example Setups

### Page View Tracking

**Configuration:**
- **Event Type:** `pageView`
- **Trigger:** All Pages
- **Custom Parameters:** (none required)

**Use for:** Understanding user navigation, measuring campaign landing pages

**Note for SPAs:** For single-page applications (React, Vue, Angular), fire `pageView` events on route changes by pushing a custom event to the data layer when your app navigates.

---

### Purchase Tracking

**Configuration:**
- **Event Type:** `purchase`
- **Trigger:** Custom Event named `purchase` (recommended)
- **Custom Parameters:**
  - `transaction_id`: `{{Transaction ID}}`
  - `value`: `{{Order Total}}`
  - `currency`: `{{Currency Code}}`
  - `product_ids`: `{{Product IDs}}`

**Alternative trigger:** Page View on `/thank-you` or `/order-confirmation`

**Use for:** E-commerce conversion tracking, ROI measurement

---

### Lead Tracking

**Configuration:**
- **Event Type:** `lead`
- **Trigger:** Form Submission or Custom Event named `lead`
- **Custom Parameters:**
  - `form_name`: `{{Form Name}}`
  - `lead_type`: `contact_form`
  - `lead_source`: `{{Page Path}}`

**Form trigger settings:**
- Check validation: ✅
- Wait for tags: ✅

**Use for:** Lead generation tracking, B2B conversions

---

### Add to Cart Tracking

**Configuration:**
- **Event Type:** `add_to_cart`
- **Trigger:** Custom Event named `add_to_cart` (recommended)
- **Custom Parameters:**
  - `product_id`: `{{Product ID}}`
  - `product_name`: `{{Product Name}}`
  - `product_price`: `{{Product Price}}`
  - `quantity`: `{{Quantity}}`
  - `currency`: `USD`

**Alternative trigger:** Click trigger on add-to-cart buttons

**Use for:** Funnel analysis, cart abandonment tracking

---

## Data Layer Examples

### Purchase Event

```javascript
// Push transaction data to data layer
dataLayer.push({
  event: 'purchase',
  transactionId: 'ORD-12345',
  transactionTotal: 149.99,
  transactionCurrency: 'USD',
  productIds: ['PROD-001', 'PROD-002']
});
```

**GTM Variables needed:**
- Create Data Layer Variable **Transaction ID** reading `transactionId`
- Create Data Layer Variable **Order Total** reading `transactionTotal`
- Create Data Layer Variable **Currency Code** reading `transactionCurrency`
- Create Data Layer Variable **Product IDs** reading `productIds`

---

### Lead Event

```javascript
// Push lead data to data layer
dataLayer.push({
  event: 'lead',
  formName: 'Contact Form',
  leadType: 'contact_form',
  leadSource: window.location.pathname
});
```

**GTM Variables needed:**
- Create Data Layer Variable **Form Name** reading `formName`
- Use built-in variable **Page Path** for `lead_source`, or create Data Layer Variable reading `leadSource`

---

### Add to Cart Event

```javascript
// Push cart data to data layer
dataLayer.push({
  event: 'add_to_cart',
  productId: 'PROD-12345',
  productName: 'Blue Widget',
  productPrice: 89.99,
  quantity: 1,
  currency: 'USD'
});
```

**GTM Variables needed:**
- Create Data Layer Variable **Product ID** reading `productId`
- Create Data Layer Variable **Product Name** reading `productName`
- Create Data Layer Variable **Product Price** reading `productPrice`
- Create Data Layer Variable **Quantity** reading `quantity`

---

## Attribution Parameters

The template automatically captures these URL parameters when present:

### UTM Parameters

- `utm_source` - Traffic source (e.g., `instagram`, `youtube`)
- `utm_medium` - Marketing medium (e.g., `influencer`, `social`)
- `utm_campaign` - Campaign name
- `utm_content` - Content differentiation
- `utm_term` - Keyword term

### Winfluencer Parameters

- `win_campaign_id` - Campaign identifier
- `win_influencer_id` - Influencer identifier
- `win_content_id` - Content/post identifier

**How it works:** When a user arrives with these parameters, the template reads them from the URL and forwards them with subsequent events for attribution tracking.

**Example tracking URL:**
```
https://yoursite.com/products?win_campaign_id=CAMP123&win_influencer_id=INF456&utm_source=instagram&utm_medium=influencer
```

---

## CNAME Setup

### Why Use CNAME?

Routing via your hostname (subject to browser policies) provides:
- Reduced likelihood of ad blocker interference
- First-party data routing through your domain
- Better alignment with privacy policies

### DNS Configuration

#### Step 1: Choose a subdomain

**Examples:** `win.yourbrand.com`, `tracking.yourbrand.com`, `analytics.yourbrand.com`

#### Step 2: Add CNAME record

In your DNS provider, create a CNAME record:

| Field | Value |
|-------|-------|
| Type | CNAME |
| Name | `win` (or your chosen subdomain) |
| Target | `api.winflncr.com` |
| TTL | 3600 (or default) |

**Important for Cloudflare users:** Use **DNS only** mode (gray cloud, not orange/proxied)

#### Step 3: Wait for DNS propagation

**Typical time:** 1-24 hours (varies by provider)

**Verify propagation:**
```bash
nslookup win.yourdomain.com
# Should return: api.winflncr.com
```

Or check online: [whatsmydns.net](https://www.whatsmydns.net)

#### Step 4: Update GTM tag

In your Winfluencer tag, set **CNAME Endpoint** to:
```
win.yourdomain.com
```
(hostname only, no `https://`, no path - the template validates this format)

#### Step 5: Verify

1. Visit your website
2. Open browser DevTools (F12) → Application/Storage → Cookies
3. Verify requests route via your hostname and that any cookies set are scoped to `.yourdomain.com` (subject to browser policies)

---

### CNAME Troubleshooting

#### Issue: DNS not propagating

- Verify CNAME record exists in DNS provider
- Check target is `api.winflncr.com`
- Wait 24-48 hours for full propagation
- Clear local DNS cache
- Check propagation status: [whatsmydns.net](https://www.whatsmydns.net)

#### Issue: Cloudflare blocking

- Ensure gray cloud (DNS only), not orange (proxied)
- Orange proxy mode interferes with tracking

#### Issue: HTTPS/SSL errors

- Wait for automatic SSL provisioning (15-30 minutes)
- Contact randi@cortexlabs.digital for manual SSL setup

#### Issue: Still using default endpoint

- Verify CNAME field in GTM has no `https://` prefix (template validates this)
- Ensure no trailing slashes
- Re-publish GTM container
- Clear browser cache

---

## Permissions

The template requires the following sandboxed permissions:

| Permission | Purpose | Justification |
|------------|---------|---------------|
| `get_url` | Read page URL to capture attribution parameters | Standard permission for tracking templates. Required to read UTM and Winfluencer campaign parameters from the URL. |
| `get_referrer` | Read referrer for source attribution | Standard permission for tracking templates. Required to track traffic sources. |
| `inject_script` | Load the tracking script when required | Required to load the Winfluencer core tracking script (`core-script.js`) from `tm.winflncr.com`. Script is loaded once per page. |
| `send_pixel` | Send HTTP beacon requests when script fallback is used | Fallback mechanism only. Used when script injection fails to ensure tracking continues via pixel beacon. Only sends to verified Winfluencer endpoints. |
| `logging` | Optional console diagnostics in Debug Mode | Debug logging only. Permission is only used when "Enable Debug Mode" is checked. Disabled by default in production. |
| **`access_globals` (`win` - execute)** | **Call the `win()` tracking function** | **The `win()` function is our tracking API (similar to Facebook's `fbq()` or Google Analytics' `gtag()`). Our template follows the standard tracking pixel pattern: (1) Load external tracking script via `injectScript`, (2) Script creates global `win()` function, (3) Template calls `win('init', pixelId)` to initialize, (4) Template calls `win('track', eventType, data)` to send events. This is identical to how Facebook Pixel, Google Analytics, and other approved tracking solutions work. The execute permission is required to call the `win()` function with these commands. Alternative approaches (using `sendPixel` directly) would lose client-side session management, automatic retry logic, device fingerprinting, and require significantly more complex template code. The `win` function is defined by our own script (`tm.winflncr.com`), not by third parties, and we control its behavior.** |
| `access_globals` (`__win_config` - read/write) | Pass configuration to the tracking script | Configuration object only. Used to pass CNAME endpoint, debug mode, and other settings from the GTM template to the tracking script before it initializes. Read/write access allows the script to read configuration and update status flags. |

### Permission Scope

All permissions are minimized and only requested when the corresponding feature is enabled:

- **Debug logging permission** is only used when Debug Mode is enabled
- **Script loading** only occurs when needed
- **Pixel fallback** activates only if script injection fails
- **Global access** is restricted to our namespaced variables only (`win`, `__win_config`)

### Technical Justification for `win` Execute Permission

**For Google Template Gallery Reviewers:**

The `win` global is a function (similar to Facebook's `fbq()` or Google Analytics' `gtag()`) that serves as our tracking API. Our template follows the standard tracking pixel pattern used by all major analytics platforms:

1. **Load external tracking script** via `injectScript` permission
2. **Script creates global `win()` function** in the browser window
3. **Template calls `win('init', pixelId)`** to initialize the tracking session
4. **Template calls `win('track', eventType, data)`** to send event data

This is identical to how Facebook Pixel, Google Analytics 4, LinkedIn Insight Tag, and other approved Gallery templates work. The execute permission is required to call the `win()` function with these commands.

**Why not use `sendPixel` directly?**

Alternative approaches (like using `sendPixel` API directly without the tracking script) would result in:
- ❌ Loss of client-side session management (users counted multiple times)
- ❌ Loss of automatic retry logic (events lost on network errors)
- ❌ Loss of device fingerprinting (reduced attribution accuracy)
- ❌ Significantly more complex template code (harder to maintain and audit)
- ❌ No client-side deduplication (duplicate events)

**Security considerations:**

- ✅ The `win` function is defined by our own script (`tm.winflncr.com`), not by third parties
- ✅ We control the script's behavior and security
- ✅ Template validates that `win` is a function before calling it
- ✅ Template only calls `win` with validated, sanitized parameters
- ✅ No arbitrary code execution - only our predefined API commands

This permission follows the established pattern of all major tracking pixels in the GTM ecosystem.

---

## Consent & Privacy

### GTM Consent Mode

The template respects GTM Consent Mode if enabled in your container or Consent Management Platform (CMP).

**Template settings:** Required consent types are declared in the Template Editor so GTM enforces them at runtime when Consent Mode is enabled.

**Required Consent Checks:** Enabled in the Template Editor so the tag only runs after the necessary consents are granted.

**Runtime behavior:** The tag only fires when the required consent types are granted in GTM/CMP.

**Consent types:**
- Analytics Storage
- Ad Storage
- Ad User Data
- Ad Personalization

**Configuration:** In GTM tag, go to **Advanced Settings → Consent Settings** and select required consent types for your use case.

---

### Privacy Best Practices

#### Do not send raw PII:

- ❌ **Never send:** Social Security Numbers, credit cards, passwords, medical info
- ⚠️ **Hash before sending:** Email addresses, phone numbers
- ✅ **Safe to send:** Order IDs, product IDs, anonymous user IDs

#### Hashing example (for site code or a separate custom tag):

```javascript
async function hashEmail(email) {
  const normalized = email.toLowerCase().trim();
  const encoder = new TextEncoder();
  const data = encoder.encode(normalized);
  const hashBuffer = await crypto.subtle.digest('SHA-256', data);
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
}

const emailHash = await hashEmail(userEmail);
// Use emailHash in custom parameters
```

#### GDPR/CCPA Compliance:

- Obtain proper consent before tracking
- Honor opt-out requests
- Provide data access upon request
- Support data deletion requests
- Document data collection in privacy policy

---

## Security

**Sandboxed environment:** This template runs in GTM's sandboxed template environment with strictly controlled permissions.

**Permission minimization:** All permissions are minimized to only what is necessary for the template's functionality. Permissions are only invoked when the corresponding features are actively used.

**No external dependencies:** The template only loads scripts from verified Winfluencer endpoints and does not depend on third-party libraries.

**Data transmission:** All data is transmitted over HTTPS. When using CNAME, traffic is routed through your domain (subject to browser policies and your DNS configuration).

**Open source:** Source code is available in this repository; issues are enabled for security disclosure and fixes.

---

## Troubleshooting

### Tag Not Firing

**Symptoms:** No events in dashboard, tag shows "Not Fired" in GTM Preview

**Solutions:**
1. Check trigger configuration in GTM Preview
2. Verify Pixel ID format: `wi-123456`
3. Ensure event type is selected
4. Confirm GTM container is published
5. Check for JavaScript errors in console

---

### Events Not Appearing in Dashboard

**Symptoms:** Tag fires successfully, network shows 200 OK, but no data in dashboard

**Solutions:**
1. Wait 2-5 minutes for data processing
2. Verify Pixel ID matches your account
3. Check date range filter in dashboard
4. Contact support: randi@cortexlabs.digital

---

### Missing Parameters

**Symptoms:** Events track but custom parameters are empty/undefined

**Solutions:**
1. Verify GTM Data Layer Variables are configured correctly
2. Check data layer timing - ensure data is pushed before event:
   ```javascript
   // Correct order:
   dataLayer.push({ 
     transactionId: '123', 
     transactionTotal: 99.99 
   });
   dataLayer.push({ event: 'purchase' });
   ```
3. Use DOM Ready or Window Loaded trigger timing
4. Test variables in GTM Preview mode (Variables tab shows values)
5. Add trigger condition to exclude undefined values

---

### CNAME Not Working

**Symptoms:** Tracking still uses default endpoint (`api.winflncr.com`), cookies on wrong domain

**Solutions:**
1. Verify DNS propagation (use [whatsmydns.net](https://www.whatsmydns.net))
2. Check CNAME field format: hostname only, no `https://`, no path (template validates this)
3. Cloudflare users: Turn OFF proxy (gray cloud, DNS only mode)
4. Clear browser cache, test in incognito
5. Contact support: randi@cortexlabs.digital

---

### Duplicate Events

**Symptoms:** Same event tracked multiple times

**Solutions:**
1. Check for multiple Winfluencer tags with overlapping triggers
2. Use GTM's built-in "Once per page" limit in trigger advanced settings (recommended first)
3. Verify the template's built-in deduplication is functioning
4. As a last resort (after GTM "Once per page" and the template's built-in dedup), implement client-side deduplication:
   ```javascript
   if (!window._winEventTracked) {
     window._winEventTracked = true;
     dataLayer.push({ event: 'purchase', ... });
   }
   ```

---

### Campaign Parameters Not Captured

**Symptoms:** Attribution data missing despite parameters in URL

**Solutions:**
1. Verify parameter spelling (case-sensitive)
   - ✅ Correct: `win_campaign_id`
   - ❌ Incorrect: `win-campaign-id`
2. Ensure page view tag fires on initial page load (not just on subsequent actions)
3. Enable debug mode and check console logs for parameter detection
4. Test with: `yoursite.com?win_campaign_id=TEST&win_influencer_id=TEST`

---

### Debug Mode

Enable debug mode to see detailed console logs:

1. Edit your Winfluencer tag
2. Check ☑ **Enable Debug Mode**
3. Save and use Preview mode
4. Open browser console (F12)
5. Look for `[GTM Template Debug]` messages

**Expected success logs:**
```
[GTM Template Debug] GTM Template loaded
[GTM Template Debug] Configuration { pixelId: "wi-123456", ... }
[GTM Template Debug] Script injection successful
[GTM Template Debug] SUCCESS: Both init and track completed
```

---

## Versioning & Updates

This template is published via the Community Template Gallery using `metadata.yaml` with commit SHAs and change notes. When updates are released, GTM will offer an in-container update notice allowing you to review changes and accept the update with one click.

**Version history:** See the [releases page](https://github.com/randicortexlabs/winfluencer-gtm-template/releases) for detailed change notes.

**Current version:** Check the template's Info tab in GTM or see `metadata.yaml` in this repository.

---

## Documentation & Support

### Primary Contact

**Email:** randi@cortexlabs.digital

**For:**
- Account setup and Pixel ID requests
- CNAME configuration assistance
- Technical troubleshooting
- Implementation guidance
- Privacy/compliance questions

**Response Time:** 24-48 hours

---

### GitHub Resources

**Repository:** https://github.com/randicortexlabs/winfluencer-gtm-template

**Issues:** https://github.com/randicortexlabs/winfluencer-gtm-template/issues - Report bugs or technical issues

**Discussions:** https://github.com/randicortexlabs/winfluencer-gtm-template/discussions - Ask questions, share implementations

---

### Before Contacting Support

Please include:
1. Pixel ID (if you have one)
2. Website URL
3. GTM Container ID
4. Issue description
5. Steps to reproduce
6. Screenshots (console logs, GTM Preview, network requests)
7. What you've already tried

---

## Implementation Checklist

### Pre-Implementation
- [ ] Winfluencer account requested
- [ ] Pixel ID obtained
- [ ] GTM access confirmed
- [ ] DNS access confirmed (for CNAME)

### Installation
- [ ] Template installed from GTM Gallery
- [ ] Page view tag created and tested
- [ ] Purchase tag created (if e-commerce)
- [ ] Lead tag created (if applicable)
- [ ] Add to cart tag created (if applicable)
- [ ] CNAME DNS record added (optional but recommended)

### Testing
- [ ] Tested in GTM Preview mode
- [ ] Verified tag firing for each event type
- [ ] Checked console logs with debug mode
- [ ] Confirmed network requests (status 200)
- [ ] Validated data in Winfluencer dashboard
- [ ] Verified CNAME working (if configured)
- [ ] Confirmed first-party cookies set

### Production
- [ ] GTM container published
- [ ] Debug mode disabled
- [ ] Monitoring enabled
- [ ] Team trained on dashboard
- [ ] Documentation saved
- [ ] Support contact saved

---

## Contributing

We welcome contributions! To contribute:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

---

© 2025 Winfluencer. All rights reserved.  
**Support:** randi@cortexlabs.digital
