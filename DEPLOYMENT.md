# Deployment Guide - Parliament Watch

## Deploying to Netlify

### Prerequisites

Before deploying, ensure you have:
- ✅ A GitHub account with your repository
- ✅ All environment variables ready
- ✅ A Netlify account (free tier works great)

---

## Option 1: Deploy via Netlify Web Interface (Recommended)

### Step 1: Sign Up / Log In to Netlify

1. Go to [https://www.netlify.com](https://www.netlify.com)
2. Click **"Sign up"** or **"Log in"**
3. Choose **"Sign up with GitHub"** for easy integration

### Step 2: Import Your Repository

1. From your Netlify dashboard, click **"Add new site"** → **"Import an existing project"**
2. Choose **"Deploy with GitHub"**
3. Authorize Netlify to access your GitHub account
4. Search for and select your repository: **`Nik-Dev21/Hackathon`**

### Step 3: Configure Build Settings

Netlify should auto-detect these settings, but verify:

```
Build command: npm run build
Publish directory: dist
```

**Site settings:**
- **Branch to deploy:** `main`
- **Base directory:** (leave empty)
- **Build command:** `npm run build`
- **Publish directory:** `dist`

### Step 4: Add Environment Variables

**CRITICAL:** Before deploying, add your environment variables:

1. Click **"Show advanced"** → **"New variable"**
2. Add each of these variables:

```env
VITE_SUPABASE_URL=your_supabase_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
VITE_GEMINI_API_KEY=your_gemini_api_key
```

**Where to find these values:**
- **Supabase URL & Key**: Supabase Dashboard → Project Settings → API
- **Gemini API Key**: Your `.env` file (do NOT commit this file)

### Step 5: Deploy

1. Click **"Deploy site"**
2. Wait 2-5 minutes for the build to complete
3. Netlify will provide you with a URL like: `https://random-name-123456.netlify.app`

### Step 6: Custom Domain (Optional)

1. Go to **Site settings** → **Domain management**
2. Click **"Add custom domain"**
3. Follow the instructions to configure DNS

---

## Option 2: Deploy via Netlify CLI

### Step 1: Install Netlify CLI

```bash
npm install -g netlify-cli
```

### Step 2: Login to Netlify

```bash
netlify login
```

This will open a browser window to authenticate.

### Step 3: Initialize Your Site

From your project directory:

```bash
cd /Users/sukhman/Documents/Projects/Hackathon
netlify init
```

Follow the prompts:
- **What would you like to do?** → "Create & configure a new site"
- **Team:** Choose your team
- **Site name:** (optional) Enter a custom name or press Enter
- **Build command:** `npm run build`
- **Directory to deploy:** `dist`

### Step 4: Set Environment Variables

```bash
netlify env:set VITE_SUPABASE_URL "your_supabase_url"
netlify env:set VITE_SUPABASE_ANON_KEY "your_supabase_anon_key"
netlify env:set VITE_GEMINI_API_KEY "your_gemini_api_key"
```

### Step 5: Deploy

For a production deployment:

```bash
netlify deploy --prod
```

Or for a draft preview:

```bash
netlify deploy
```

---

## Post-Deployment Configuration

### 1. Configure Redirects for React Router

Create a file called `netlify.toml` in your project root:

```toml
[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

This ensures React Router works correctly with client-side routing.

**Alternative:** Create `public/_redirects` file:

```
/* /index.html 200
```

### 2. Update CORS Settings in Supabase

If you experience API issues:

1. Go to Supabase Dashboard
2. Navigate to **Project Settings** → **API**
3. Scroll to **CORS Settings**
4. Add your Netlify URL: `https://your-site.netlify.app`

### 3. Test Your Deployment

Visit your Netlify URL and test:
- ✅ News feed loads
- ✅ Bills page displays data
- ✅ MPs page shows politicians
- ✅ Chatbot responds to queries
- ✅ All images load correctly
- ✅ Navigation works (no 404 errors)

---

## Continuous Deployment

Once set up, Netlify automatically deploys when you push to GitHub:

1. Make changes to your code
2. Commit: `git commit -am "Update feature"`
3. Push: `git push origin main`
4. Netlify automatically rebuilds and deploys (2-5 minutes)

You'll receive build notifications via email.

---

## Troubleshooting

### Build Fails

**Check the build log:**
1. Go to Netlify Dashboard → Your Site → Deploys
2. Click on the failed deploy
3. Read the error messages

**Common issues:**
- Missing environment variables
- Node version mismatch
- Dependency installation failures

**Fix:** Set Node version in `netlify.toml`:

```toml
[build.environment]
  NODE_VERSION = "18"
```

### Environment Variables Not Working

1. Ensure they start with `VITE_` prefix
2. Redeploy after adding variables (Netlify → Deploys → Trigger deploy)
3. Check for typos in variable names

### 404 Errors on Routes

Add the redirect rule mentioned above in `netlify.toml` or `public/_redirects`.

### API Requests Failing

1. Verify environment variables are set correctly
2. Check browser console for CORS errors
3. Ensure Supabase allows your Netlify domain

### Slow Build Times

Netlify builds can take 2-5 minutes. This is normal. If builds take longer:
- Check for large dependencies
- Consider implementing build caching

---

## Performance Optimization

### Enable Build Plugins

In `netlify.toml`:

```toml
[[plugins]]
  package = "@netlify/plugin-lighthouse"

[[plugins]]
  package = "netlify-plugin-cache"
```

Install plugins:
```bash
npm install --save-dev @netlify/plugin-lighthouse netlify-plugin-cache
```

### Asset Optimization

Netlify automatically:
- ✅ Minifies HTML, CSS, JS
- ✅ Serves assets via global CDN
- ✅ Enables HTTP/2
- ✅ Provides automatic SSL certificates

### Custom Headers

Add to `netlify.toml`:

```toml
[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"
```

---

## Monitoring Your Site

### Netlify Analytics (Paid Feature)

Enable in Netlify Dashboard → Analytics to track:
- Page views
- Unique visitors
- Traffic sources
- Popular pages

### Free Alternatives

1. **Google Analytics**
   - Add tracking code to `index.html`
   
2. **Plausible Analytics**
   - Privacy-focused alternative
   
3. **Netlify Logs**
   - Free function logs and deploy notifications

---

## Useful Netlify Features

### Deploy Previews

Every pull request gets a unique preview URL:
- Test changes before merging
- Share with team for review
- No impact on production

### Branch Deploys

Deploy specific branches:
- `main` → Production
- `staging` → Staging environment
- `feature-xyz` → Feature preview

Configure in: Site settings → Build & deploy → Deploy contexts

### Forms

If you add contact forms later, Netlify handles submissions:

```html
<form name="contact" method="POST" data-netlify="true">
  <input type="text" name="name" />
  <input type="email" name="email" />
  <button type="submit">Send</button>
</form>
```

### Functions

Netlify supports serverless functions. Your Supabase Edge Functions work great, but you could also use Netlify Functions if needed.

---

## Cost Considerations

### Free Tier Includes:
- ✅ 100 GB bandwidth/month
- ✅ Unlimited sites
- ✅ HTTPS certificates
- ✅ Continuous deployment
- ✅ Deploy previews
- ✅ Serverless functions (125k requests/month)

### Your App Usage:
Based on your application:
- **Estimated bandwidth:** 5-20 GB/month (for moderate traffic)
- **Build minutes:** ~5 minutes per deploy (300 minutes/month free)
- **Function calls:** Minimal (using Supabase Edge Functions primarily)

**Conclusion:** Free tier should be sufficient unless you get viral-level traffic!

---

## Quick Command Reference

```bash
# Install Netlify CLI
npm install -g netlify-cli

# Login
netlify login

# Initialize site
netlify init

# Deploy to draft
netlify deploy

# Deploy to production
netlify deploy --prod

# Open site in browser
netlify open:site

# Open admin dashboard
netlify open:admin

# View site logs
netlify logs

# Link existing site
netlify link

# Check build status
netlify status
```

---

## Final Checklist

Before going live:

- [ ] All environment variables are set in Netlify
- [ ] Redirect rule is configured for React Router
- [ ] Site builds successfully
- [ ] All features work on the deployed site
- [ ] Custom domain configured (if desired)
- [ ] CORS settings updated in Supabase
- [ ] Analytics/monitoring set up
- [ ] Team members have access to Netlify dashboard
- [ ] Git repository has proper branch protection
- [ ] README.md updated with live site URL

---

## Next Steps

1. Deploy your site following the steps above
2. Test thoroughly on the live URL
3. Share the link with your team or users
4. Monitor the Netlify dashboard for issues
5. Set up custom domain if needed

**Your app is production-ready!** 🚀

---

## Support Resources

- **Netlify Documentation:** https://docs.netlify.com
- **Netlify Community:** https://answers.netlify.com
- **Netlify Status:** https://www.netlifystatus.com
- **Vite Deployment Guide:** https://vitejs.dev/guide/static-deploy.html#netlify

---

## Example: Complete netlify.toml File

Here's a production-ready configuration:

```toml
[build]
  command = "npm run build"
  publish = "dist"

[build.environment]
  NODE_VERSION = "18"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"

[[headers]]
  for = "/assets/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"
```

Save this as `netlify.toml` in your project root and commit it.

---

**You're all set!** Follow the steps above and your Parliament Watch application will be live on Netlify in minutes. 🎉
