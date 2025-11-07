# Environment Variables Configuration Guide

This document provides detailed information about all environment variables required for the application.

## 📋 Table of Contents

- [Convex Backend](#convex-backend)
- [Clerk Authentication](#clerk-authentication)
- [Application URLs](#application-urls)
- [OpenAI Integration](#openai-integration)
- [Polar.sh Payments](#polarsh-payments)
- [Local Development Setup](#local-development-setup)
- [Production Deployment](#production-deployment)

---

## 🗄️ Convex Backend

### Self-Hosted Convex (Recommended for Production)

```bash
VITE_CONVEX_URL=https://backend.boteco.pt
CONVEX_SELF_HOSTED_URL=https://backend.boteco.pt
CONVEX_SELF_HOSTED_ADMIN_KEY=self-hosted-convex|your_admin_key_here
```

**Description:**
- `VITE_CONVEX_URL`: Client-side URL for Convex backend (prefixed with `VITE_` for Vite exposure)
- `CONVEX_SELF_HOSTED_URL`: Server-side URL for Convex backend
- `CONVEX_SELF_HOSTED_ADMIN_KEY`: Admin authentication key for self-hosted Convex

**Where to get:**
1. Deploy your self-hosted Convex instance
2. Configure your domain to point to the Convex backend
3. Generate admin key from Convex dashboard

### Cloud-Hosted Convex (Alternative)

```bash
# Use this instead of self-hosted if using Convex cloud
CONVEX_DEPLOYMENT=prod:your-deployment-name
VITE_CONVEX_URL=https://your-deployment.convex.cloud
```

---

## 🔐 Clerk Authentication

```bash
VITE_CLERK_PUBLISHABLE_KEY=pk_live_Y2xlcmsuYm90ZWNvLnB0JA
VITE_CLERK_FRONTEND_API_URL=https://clerk.boteco.pt
CLERK_SECRET_KEY=sk_live_your_clerk_secret_key
```

**Description:**
- `VITE_CLERK_PUBLISHABLE_KEY`: Public key for client-side Clerk integration
- `VITE_CLERK_FRONTEND_API_URL`: Custom domain for Clerk authentication UI
- `CLERK_SECRET_KEY`: Secret key for server-side Clerk API calls

**Where to get:**
1. Sign up at [clerk.com](https://clerk.com)
2. Create a new application
3. Go to **API Keys** in dashboard
4. Copy publishable key and secret key
5. Configure custom domain in **Domains** section (optional)

**Production Setup:**
- Use `pk_live_*` and `sk_live_*` keys (not `pk_test_*`)
- Configure allowed redirect URLs in Clerk dashboard
- Set up production domains

---

## 🌐 Application URLs

```bash
FRONTEND_URL=https://boteco.pt
# For local development use:
# FRONTEND_URL=http://localhost:5173
```

**Description:**
- Used for CORS configuration
- Redirect URLs after authentication
- Success URLs after payment

**Important:** 
- Must match your actual deployment URL
- Used in webhook callbacks
- Required for proper CORS headers

---

## 🤖 OpenAI Integration

```bash
OPENAI_API_KEY=sk-proj-your_openai_api_key
```

**Description:**
- Powers the AI chat feature in `/dashboard/chat`
- Uses GPT-4o model by default

**Where to get:**
1. Create account at [platform.openai.com](https://platform.openai.com)
2. Go to **API Keys** section
3. Create new secret key
4. Copy the key (starts with `sk-proj-` or `sk-`)

**Usage limits:**
- Monitor usage in OpenAI dashboard
- Set up billing limits
- Consider implementing rate limiting for production

---

## 💳 Polar.sh Payments

```bash
POLAR_SERVER=production
POLAR_ACCESS_TOKEN=polar_oat_your_access_token
POLAR_ORGANIZATION_ID=your_organization_id
POLAR_WEBHOOK_SECRET=polar_whs_your_webhook_secret
```

**Description:**
- `POLAR_SERVER`: Environment mode (`sandbox` or `production`)
- `POLAR_ACCESS_TOKEN`: API access token for Polar.sh
- `POLAR_ORGANIZATION_ID`: Your organization UUID
- `POLAR_WEBHOOK_SECRET`: Secret for webhook signature verification

**Where to get:**
1. Sign up at [polar.sh](https://polar.sh)
2. Create an organization
3. Go to **Settings** → **API**
4. Generate access token
5. Copy organization ID from URL or settings
6. Get webhook secret from **Webhooks** section

**Webhook Configuration:**
- **URL:** `https://backend.boteco.pt/webhook/polar`
- **Events to subscribe:**
  - `subscription.created`
  - `subscription.updated`
  - `subscription.canceled`
  - `subscription.revoked`
  - All checkout events

**Testing:**
- Use `POLAR_SERVER=sandbox` for development
- Use test payment methods in sandbox
- Switch to `production` for live payments

---

## 🔧 Local Development Setup

### Step 1: Copy Environment Template

```bash
cp .env.example .env.local
```

### Step 2: Configure for Local Development

```bash
# .env.local for local development

# Convex - Use your self-hosted URL or local Convex dev
VITE_CONVEX_URL=https://backend.boteco.pt
CONVEX_SELF_HOSTED_URL=https://backend.boteco.pt
CONVEX_SELF_HOSTED_ADMIN_KEY=self-hosted-convex|your_dev_key

# Clerk - Can use test keys for development
VITE_CLERK_PUBLISHABLE_KEY=pk_test_your_test_key
VITE_CLERK_FRONTEND_API_URL=https://clerk.boteco.pt
CLERK_SECRET_KEY=sk_test_your_test_key

# Local frontend URL
FRONTEND_URL=http://localhost:5173

# OpenAI - Use actual key
OPENAI_API_KEY=sk-proj-your_key

# Polar - Use sandbox for testing
POLAR_SERVER=sandbox
POLAR_ACCESS_TOKEN=polar_oat_sandbox_token
POLAR_ORGANIZATION_ID=your_org_id
POLAR_WEBHOOK_SECRET=polar_whs_sandbox_secret
```

### Step 3: Start Development Servers

```bash
# Terminal 1: Start Convex
npx convex dev

# Terminal 2: Start Frontend
npm run dev
```

### Local Testing Tips:

1. **Test webhooks locally:**
   - Use ngrok or similar: `ngrok http 3000`
   - Update Polar webhook URL to ngrok URL
   - Don't forget `/webhook/polar` path

2. **Environment sync:**
   - Set environment variables in Convex dashboard
   - Keep .env.local in sync with Convex env vars

3. **Authentication testing:**
   - Use Clerk test mode for local development
   - Sign up with test emails

---

## 🚀 Production Deployment

### Pre-Deployment Checklist

- [ ] All environment variables set to production values
- [ ] `POLAR_SERVER=production` 
- [ ] Using `pk_live_*` and `sk_live_*` Clerk keys
- [ ] `FRONTEND_URL` points to production domain
- [ ] Convex environment variables configured in Convex dashboard
- [ ] Polar webhook configured with production URL
- [ ] OpenAI API key has sufficient credits
- [ ] DNS configured for all custom domains

### Environment Variables Location

**For Self-Hosted Convex:**
- Set all variables in Convex dashboard environment settings
- Variables are available to all Convex functions
- .env.local is NOT used in production (git-ignored)

**For Frontend (Vercel/Cloud):**
- Set `VITE_*` prefixed variables in deployment platform
- Non-VITE variables are server-side only
- Never expose secrets in client bundle

### Production Webhook Setup

1. Configure Polar.sh webhook:
   - URL: `https://backend.boteco.pt/webhook/polar`
   - Verify webhook secret matches `POLAR_WEBHOOK_SECRET`

2. Test webhook delivery:
   - Create test subscription in Polar
   - Check Convex logs for webhook events
   - Verify data in `webhookEvents` table

### Security Best Practices

1. **Never commit secrets:**
   - .env.local is git-ignored
   - Use .env.example as template only

2. **Rotate keys regularly:**
   - Especially after team member changes
   - Polar access tokens
   - Clerk secret keys

3. **Monitor API usage:**
   - Set up alerts for unusual activity
   - Monitor OpenAI token usage
   - Check Polar API rate limits

4. **Environment isolation:**
   - Use separate Clerk applications for dev/prod
   - Use Polar sandbox for development
   - Never mix production and development data

---

## 🆘 Troubleshooting

### Issue: Convex functions can't access environment variables

**Solution:** 
- Set variables in Convex dashboard, not just .env.local
- Variables in Convex dashboard take precedence
- Redeploy Convex functions after changing env vars

### Issue: Webhook not receiving events

**Solution:**
1. Verify webhook URL is correct: `https://backend.boteco.pt/webhook/polar`
2. Check webhook secret matches environment variable
3. Review Polar webhook delivery logs
4. Check Convex HTTP route is configured correctly

### Issue: Authentication redirects fail

**Solution:**
1. Verify `FRONTEND_URL` matches actual domain
2. Check Clerk allowed redirect URLs
3. Ensure `VITE_CLERK_FRONTEND_API_URL` is accessible

### Issue: CORS errors

**Solution:**
1. `FRONTEND_URL` must match the origin making requests
2. Check Convex HTTP route CORS headers
3. Verify no trailing slash in URLs

---

## 📚 Additional Resources

- [Convex Documentation](https://docs.convex.dev)
- [Clerk Documentation](https://clerk.com/docs)
- [Polar.sh API Reference](https://api.polar.sh/docs)
- [OpenAI API Documentation](https://platform.openai.com/docs)
- [Vite Environment Variables](https://vite.dev/guide/env-and-mode.html)

---

**Last Updated:** November 7, 2025
