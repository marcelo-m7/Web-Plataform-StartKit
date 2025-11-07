# Production Deployment Checklist

Use this checklist before deploying to production to ensure everything is configured correctly.

## ✅ Pre-Deployment Checklist

### 🔐 Environment Variables

- [ ] **Convex Self-Hosted Configuration**
  - [ ] `VITE_CONVEX_URL` set to production backend URL
  - [ ] `CONVEX_SELF_HOSTED_URL` set to production backend URL
  - [ ] `CONVEX_SELF_HOSTED_ADMIN_KEY` configured with production key
  - [ ] All environment variables added to Convex dashboard

- [ ] **Clerk Authentication (Production Keys)**
  - [ ] `VITE_CLERK_PUBLISHABLE_KEY` using `pk_live_*` (not `pk_test_*`)
  - [ ] `CLERK_SECRET_KEY` using `sk_live_*` (not `sk_test_*`)
  - [ ] `VITE_CLERK_FRONTEND_API_URL` pointing to production domain
  - [ ] Allowed redirect URLs configured in Clerk dashboard
  - [ ] Production domains added to Clerk allowed origins

- [ ] **Polar.sh Payments**
  - [ ] `POLAR_SERVER=production` (not `sandbox`)
  - [ ] `POLAR_ACCESS_TOKEN` using production token
  - [ ] `POLAR_ORGANIZATION_ID` verified correct
  - [ ] `POLAR_WEBHOOK_SECRET` matches webhook configuration
  - [ ] Webhook URL configured: `https://backend.boteco.pt/webhook/polar`
  - [ ] Test subscription created and verified

- [ ] **Application Configuration**
  - [ ] `FRONTEND_URL=https://boteco.pt` (production URL, not localhost)
  - [ ] `OPENAI_API_KEY` configured and has sufficient credits
  - [ ] No hardcoded localhost URLs in code

### 🌐 DNS & Domains

- [ ] Frontend domain (boteco.pt) points to hosting provider
- [ ] Backend domain (backend.boteco.pt) points to Convex server
- [ ] Clerk domain (clerk.boteco.pt) configured (if using custom domain)
- [ ] SSL certificates valid and auto-renewing
- [ ] All domains resolve correctly (test with `nslookup` or `dig`)

### 🔧 Service Configurations

#### Convex

- [ ] Self-hosted Convex instance running and accessible
- [ ] All environment variables set in Convex dashboard
- [ ] Convex functions deployed successfully
- [ ] HTTP routes accessible at backend URL
- [ ] Test endpoint: `https://backend.boteco.pt/api/chat` (OPTIONS should work)

#### Clerk

- [ ] Production application created
- [ ] Email/SMS providers configured
- [ ] Sign-in and sign-up flows tested
- [ ] Session duration configured appropriately
- [ ] User metadata fields set up if needed
- [ ] Webhook endpoints configured (if using Clerk webhooks)

#### Polar.sh

- [ ] Organization verified and active
- [ ] Products and pricing created
- [ ] Webhook endpoint added and verified
- [ ] Webhook events subscribed:
  - [ ] `subscription.created`
  - [ ] `subscription.updated`
  - [ ] `subscription.canceled`
  - [ ] `subscription.revoked`
  - [ ] `checkout.created`
  - [ ] `checkout.updated`
- [ ] Test payment completed successfully
- [ ] Customer portal accessible

### 🧪 Testing

- [ ] **Build Tests**
  - [ ] `npm run typecheck` passes with no errors
  - [ ] `npm run build` completes successfully
  - [ ] No critical warnings in build output

- [ ] **Authentication Flow**
  - [ ] Sign-up creates new user in Clerk
  - [ ] Sign-in works with existing credentials
  - [ ] Sign-out clears session properly
  - [ ] Protected routes redirect to sign-in when not authenticated
  - [ ] User sync to Convex database works

- [ ] **Subscription Flow**
  - [ ] Pricing page loads products from Polar
  - [ ] Checkout redirects to Polar correctly
  - [ ] Success page shows after payment
  - [ ] Webhook receives subscription events
  - [ ] User gains access to dashboard after subscription
  - [ ] Subscription status updates in real-time

- [ ] **Dashboard Access**
  - [ ] Dashboard requires active subscription
  - [ ] Users without subscription redirected to subscription-required page
  - [ ] All dashboard features load correctly
  - [ ] Chat functionality works with OpenAI

- [ ] **Webhooks**
  - [ ] Polar webhook receives events (check webhook logs in Polar dashboard)
  - [ ] Webhook signature verification passes
  - [ ] Events stored in Convex `webhookEvents` table
  - [ ] Subscription status updates correctly in database

### 🔒 Security

- [ ] `.env.local` not committed to git (check `.gitignore`)
- [ ] No secrets exposed in client-side code
- [ ] No API keys in frontend bundle
- [ ] CORS headers configured correctly
- [ ] Rate limiting considered for AI chat endpoint
- [ ] Webhook signature verification enabled
- [ ] HTTPS enforced on all domains
- [ ] Security headers configured (if using Vercel/custom server)

### 📊 Monitoring & Logging

- [ ] Convex logs accessible and monitoring configured
- [ ] Error tracking set up (e.g., Sentry, LogRocket)
- [ ] OpenAI usage monitoring configured
- [ ] Polar webhook delivery logs reviewed
- [ ] Alerts configured for:
  - [ ] Failed webhook deliveries
  - [ ] High error rates
  - [ ] OpenAI API errors
  - [ ] Payment failures

### 🚀 Deployment

- [ ] **Pre-Deploy**
  - [ ] All changes committed and pushed to repository
  - [ ] Production branch up to date
  - [ ] Database migrations run (if any)
  - [ ] Convex schema deployed

- [ ] **Deploy Steps**
  - [ ] Frontend deployed to hosting provider
  - [ ] Environment variables configured in deployment platform
  - [ ] Build successful in production environment
  - [ ] Health check endpoint responds correctly

- [ ] **Post-Deploy Verification**
  - [ ] Homepage loads correctly
  - [ ] Sign-in/Sign-up functional
  - [ ] Pricing page shows products
  - [ ] Test purchase flow end-to-end
  - [ ] Dashboard accessible to subscribed users
  - [ ] No console errors in browser
  - [ ] All API calls using HTTPS

### 📱 Performance & UX

- [ ] Page load times acceptable (< 3s)
- [ ] Images optimized and lazy-loaded
- [ ] Font loading optimized (swap display)
- [ ] Mobile responsive on all pages
- [ ] Tested on multiple browsers (Chrome, Firefox, Safari)
- [ ] Tested on mobile devices (iOS, Android)
- [ ] Analytics tracking configured (Vercel Analytics enabled)

### 📝 Documentation

- [ ] `README.md` updated with production setup instructions
- [ ] `ENVIRONMENT.md` reviewed and accurate
- [ ] `.env.example` contains all required variables
- [ ] Deployment procedures documented
- [ ] Troubleshooting guide available
- [ ] Team members have access to necessary credentials

### 🔄 Backup & Recovery

- [ ] Convex data backup strategy in place
- [ ] Ability to rollback deployment if needed
- [ ] Database restore procedure tested
- [ ] Environment variable backup stored securely

---

## 🎯 Go-Live Day

1. **Final Verification** (30 minutes before)
   - [ ] Run full test suite
   - [ ] Verify all environment variables one last time
   - [ ] Check Convex, Clerk, and Polar dashboards for any issues
   - [ ] Confirm webhook is active and receiving test events

2. **Deploy**
   - [ ] Deploy frontend to production
   - [ ] Verify deployment success
   - [ ] Check build logs for errors

3. **Immediate Post-Deploy** (within 15 minutes)
   - [ ] Test sign-up flow
   - [ ] Test sign-in flow
   - [ ] Complete test purchase
   - [ ] Verify webhook received subscription event
   - [ ] Check user appears in dashboard
   - [ ] Test AI chat functionality

4. **Monitoring** (first 24 hours)
   - [ ] Monitor error logs
   - [ ] Watch webhook delivery success rate
   - [ ] Track user sign-ups
   - [ ] Monitor OpenAI API usage
   - [ ] Check for any failed payments

---

## ⚠️ Known Issues / Warnings

- Sourcemap warnings during build are **harmless** (informational only)
- Markdown lint warnings in documentation are **cosmetic only**

---

## 📞 Emergency Contacts

- **Convex Support:** [convex.dev/support](https://www.convex.dev/support)
- **Clerk Support:** [clerk.com/support](https://clerk.com/support)
- **Polar Support:** [polar.sh/support](https://polar.sh/support)
- **OpenAI Support:** [help.openai.com](https://help.openai.com)

---

## 🔐 Credential Storage

**NEVER** store credentials in this file. Keep production credentials:

- In 1Password/LastPass/similar password manager
- In deployment platform (Vercel, etc.) environment variables
- In Convex dashboard environment settings
- Encrypted and accessible only to authorized team members

---

**Last Updated:** November 7, 2025

**Deployment Status:** ☐ Not Started | ☐ In Progress | ☐ Completed

---

## Post-Deployment Notes

(Add notes here after deployment)

- Deployment Date: _______________
- Deployed By: _______________
- Issues Encountered: _______________
- Resolutions: _______________
