# Convex Environment Setup Guide

## 🔴 CRITICAL: Server Errors Detected

Your application is experiencing **"Server Error"** messages because the Convex backend at `https://backend.boteco.pt` does not have the required environment variables configured.

### Errors Observed:
```
Failed to fetch plans: Error: [Request ID: ...] Server Error
Failed to fetch subscription data: Error: [Request ID: ...] Server Error
```

---

## ✅ Solution: Configure Convex Environment Variables

You need to set environment variables in your **Convex Dashboard** for your self-hosted deployment.

### Step 1: Access Convex Dashboard

1. Go to your Convex admin panel at: **https://backend.boteco.pt/_admin**
2. Or access via the Convex dashboard if you have a project link

### Step 2: Add Environment Variables

Navigate to **Settings** → **Environment Variables** and add the following:

#### Required Variables for Local Development:

```bash
# Clerk Authentication (TEST ENVIRONMENT)
VITE_CLERK_PUBLISHABLE_KEY=pk_test_c3Ryb25nLXF1ZXR6YWwtMTUuY2xlcmsuYWNjb3VudHMuZGV2JA
VITE_CLERK_FRONTEND_API_URL=https://strong-quetzal-15.clerk.accounts.dev
CLERK_SECRET_KEY=sk_test_cohw8pF3iC9dboyBmq3cdNcahWf969EhaSM4BCYfir

# Polar.sh (SANDBOX MODE for local dev)
POLAR_ACCESS_TOKEN=polar_at_wHMw46zK4iKSsQk48Xk4pW5Q9jXf4RIvYCWAl6-7a_AcXRD8iEqfBJqT3Y5V0Zw0eiWC0yI
POLAR_ORGANIZATION_ID=41ab4ff3-e22f-4a78-86b4-75bcb50d37d8
POLAR_WEBHOOK_SECRET=whsec_OqDTFg3TxO_Z5HfFdNS_NslTkEpErbqy
POLAR_SERVER=sandbox

# Frontend URL (for redirects)
FRONTEND_URL=http://localhost:5173

# OpenAI (if using chat feature)
OPENAI_API_KEY=your-openai-key-here
```

### Step 3: Verify Configuration

After adding the environment variables:

1. **Restart your Convex deployment** (if needed)
2. **Refresh your local dev server**
3. **Test the homepage** - pricing should load without errors
4. **Test dashboard access** - subscription check should work

---

## 🔍 How to Verify It's Working

### Before Fix:
```
❌ Failed to fetch plans: Error: Server Error
❌ Failed to fetch subscription data: Error: Server Error
❌ Dashboard shows empty/redirects to subscription-required
```

### After Fix:
```
✅ Homepage loads pricing plans from Polar
✅ Subscription status queries work
✅ Dashboard accessible (may show "Subscription Required" but no errors)
```

---

## 🚨 Important Notes

### About the "Subscription Required" Page

Even after fixing Convex errors, you will see **"Subscription Required"** when accessing `/dashboard` because:

1. ✅ **This is EXPECTED behavior**
2. Your test user doesn't have an active subscription
3. The code in `app/routes/dashboard/layout.tsx` checks for subscriptions:

```tsx
// This code runs on every dashboard access
const subscriptionStatus = await convexClient.query(
  api.subscriptions.checkUserSubscriptionStatus, 
  { userId }
);

// Redirects if no subscription
if (!subscriptionStatus?.hasActiveSubscription) {
  throw redirect("/subscription-required");
}
```

### To Access Dashboard Content:

**Option 1: Disable Subscription Check (Local Dev Only)**

Temporarily comment out the subscription check in `app/routes/dashboard/layout.tsx`:

```tsx
// Comment out these lines for testing:
// if (!subscriptionStatus?.hasActiveSubscription) {
//   throw redirect("/subscription-required");
// }
```

**Option 2: Create Test Subscription (Recommended)**

1. Go to http://localhost:5173/pricing
2. Click on a plan
3. Complete checkout in Polar sandbox mode
4. Return to dashboard - should now work

---

## 📋 Environment Variables Explanation

### Why These Are Needed in Convex:

| Variable | Used By | Purpose |
|----------|---------|---------|
| `CLERK_SECRET_KEY` | `convex/users.ts` | Sync user data from Clerk to Convex |
| `POLAR_ACCESS_TOKEN` | `convex/subscriptions.ts` | Fetch pricing plans, create checkouts |
| `POLAR_ORGANIZATION_ID` | `convex/subscriptions.ts` | Identify your Polar organization |
| `POLAR_WEBHOOK_SECRET` | `convex/http.ts` | Verify webhook authenticity |
| `POLAR_SERVER` | `convex/subscriptions.ts` | Use "sandbox" or "production" |
| `FRONTEND_URL` | `convex/subscriptions.ts` | Redirect URLs for checkout success |
| `OPENAI_API_KEY` | `convex/http.ts` | Power the chat feature |
| `VITE_CLERK_*` | `convex/auth.config.ts` | Validate Clerk tokens |

---

## 🔧 Self-Hosted Convex vs Convex Cloud

You are using **self-hosted Convex** at `https://backend.boteco.pt`.

### Differences from Convex Cloud:

- ❌ No web dashboard UI (you manage via admin API or config files)
- ✅ Full control over deployment
- ⚠️ Must manually configure environment variables
- ⚠️ Must handle deployments yourself

### How to Set Environment Variables (Self-Hosted):

If you don't have a web UI, you may need to:

1. **Use Convex CLI:**
   ```bash
   npx convex env set CLERK_SECRET_KEY "sk_test_..."
   npx convex env set POLAR_ACCESS_TOKEN "polar_at_..."
   # etc for all variables
   ```

2. **Or configure via deployment config** (check your Convex setup docs)

3. **Or access admin API** at `https://backend.boteco.pt/_admin`

---

## 🎯 Next Steps

### Immediate Actions:

1. ✅ Add all environment variables to Convex
2. ✅ Restart Convex deployment (if needed)
3. ✅ Refresh http://localhost:5173/
4. ✅ Check browser console - no more "Server Error"
5. ✅ Test `/pricing` - plans should load
6. ✅ Test `/dashboard` - should show "Subscription Required" (not errors)

### For Full Testing:

1. Disable subscription check temporarily OR
2. Create a test subscription via Polar sandbox
3. Verify dashboard content displays correctly

---

## 📞 Need Help?

### If errors persist after setting env vars:

1. **Check Convex logs** - look for deployment or runtime errors
2. **Verify env var names** - must match exactly (case-sensitive)
3. **Confirm deployment restarted** - changes may require restart
4. **Test individual functions** - use Convex dashboard/CLI to test queries directly

### Common Issues:

| Issue | Solution |
|-------|----------|
| Still see "Server Error" | Convex deployment didn't restart |
| "Invalid token" errors | `CLERK_SECRET_KEY` is wrong |
| "Unauthorized" from Polar | `POLAR_ACCESS_TOKEN` is wrong |
| Webhook failures | `POLAR_WEBHOOK_SECRET` is wrong |
| Wrong pricing data | `POLAR_SERVER` should be "sandbox" for dev |

---

## ✅ Success Criteria

You'll know it's working when:

- ✅ No "Server Error" messages in terminal
- ✅ Homepage `/pricing` loads with actual plans from Polar
- ✅ Dashboard redirects to "Subscription Required" (not blank page)
- ✅ Browser console has no Convex-related errors

---

**Last Updated:** November 7, 2025  
**Environment:** Local Development with Self-Hosted Convex  
**Status:** ⏳ Awaiting Convex Environment Variable Configuration
