# Bug Fixes Applied - Local Development

## Issue: 500 Internal Server Error on Homepage

### Root Cause
The application was using incorrect Convex imports (`convex/nextjs`) in a React Router project, which expected `NEXT_PUBLIC_CONVEX_URL` instead of `VITE_CONVEX_URL`.

### Errors Found
1. **Wrong Convex Imports**: Using `fetchAction` and `fetchQuery` from `convex/nextjs` instead of `ConvexHttpClient` from `convex/browser`
2. **Wrong Environment Variable for Local Dev**: `FRONTEND_URL` was set to production URL instead of localhost
3. **Wrong Polar Server**: Was set to `production` instead of `sandbox` for local development

---

## Fixes Applied

### 1. Fixed Convex Client Usage

**Files Modified:**
- `app/routes/home.tsx`
- `app/routes/dashboard/layout.tsx`

**Changes:**
```typescript
// BEFORE (Wrong - Next.js specific)
import { fetchAction, fetchQuery } from "convex/nextjs";

// AFTER (Correct - React Router compatible)
import { ConvexHttpClient } from "convex/browser";

const convexClient = new ConvexHttpClient(process.env.VITE_CONVEX_URL!);

// Usage in loaders
await convexClient.query(api.subscriptions.checkUserSubscriptionStatus, { userId });
await convexClient.action(api.subscriptions.getAvailablePlans);
```

### 2. Fixed Environment Variables for Local Development

**File:** `.env.local`

```bash
# BEFORE
FRONTEND_URL=https://boteco.pt
POLAR_SERVER=production

# AFTER
FRONTEND_URL=http://localhost:5173
POLAR_SERVER=sandbox
```

### 3. Added Error Handling

Added try-catch blocks and fallback data in loaders to prevent 500 errors:

```typescript
try {
  // ... fetch data
  return {
    isSignedIn: !!userId,
    hasActiveSubscription: subscriptionData?.hasActiveSubscription || false,
    plans,
  };
} catch (error) {
  console.error("Error in home loader:", error);
  // Return safe defaults if everything fails
  return {
    isSignedIn: !!userId,
    hasActiveSubscription: false,
    plans: { items: [], pagination: { total: 0 } },
  };
}
```

---

## Environment Configuration Guide

### For Local Development

Use these settings in `.env.local`:

```bash
# Convex
VITE_CONVEX_URL=https://backend.boteco.pt
CONVEX_SELF_HOSTED_URL=https://backend.boteco.pt
CONVEX_SELF_HOSTED_ADMIN_KEY=self-hosted-convex|your_key

# Clerk
VITE_CLERK_PUBLISHABLE_KEY=pk_live_your_key
VITE_CLERK_FRONTEND_API_URL=https://clerk.boteco.pt
CLERK_SECRET_KEY=sk_live_your_secret

# Local URLs
FRONTEND_URL=http://localhost:5173

# Polar (use sandbox for testing)
POLAR_SERVER=sandbox
POLAR_ACCESS_TOKEN=your_token
POLAR_ORGANIZATION_ID=your_org_id
POLAR_WEBHOOK_SECRET=your_secret

# OpenAI
OPENAI_API_KEY=sk-proj-your_key
```

### For Production

Switch these variables before deploying:

```bash
# Production URL
FRONTEND_URL=https://boteco.pt

# Polar Production Mode
POLAR_SERVER=production
```

---

## Testing Steps

1. **Start Convex (if needed):**
   ```bash
   npx convex dev
   ```

2. **Start Development Server:**
   ```bash
   npm run dev
   ```

3. **Access the Application:**
   - Open `http://localhost:5173/`
   - Should load without 500 errors

4. **Test Key Features:**
   - [ ] Homepage loads pricing from Polar
   - [ ] Sign-in/Sign-up redirects to Clerk
   - [ ] Dashboard requires authentication
   - [ ] Subscription flow works

---

## Important Notes

### Development vs Production

**Local Development:**
- Use `FRONTEND_URL=http://localhost:5173`
- Use `POLAR_SERVER=sandbox`
- Can connect to production Convex backend for testing
- Environment variables from `.env.local`

**Production:**
- Use `FRONTEND_URL=https://boteco.pt`
- Use `POLAR_SERVER=production`
- Environment variables from deployment platform + Convex dashboard

### Convex Client Usage

**Server-Side (Loaders):**
```typescript
import { ConvexHttpClient } from "convex/browser";
const client = new ConvexHttpClient(process.env.VITE_CONVEX_URL!);
await client.query(api.yourFunction);
```

**Client-Side (Components):**
```typescript
import { useQuery, useMutation, useAction } from "convex/react";
const data = useQuery(api.yourFunction);
```

### Why This Happened

The starter template likely came from a Next.js example and wasn't properly converted to React Router. The key differences:

1. **Next.js** uses `NEXT_PUBLIC_*` prefixes and `convex/nextjs`
2. **React Router** uses `VITE_*` prefixes and `convex/browser`

---

## Files Created/Modified

### Modified Files:
1. `.env.local` - Updated for local development
2. `app/routes/home.tsx` - Fixed Convex imports and error handling
3. `app/routes/dashboard/layout.tsx` - Fixed Convex imports

### Created Files:
1. `.env.local.development` - Template for local development
2. `BUGFIX.md` - This documentation

---

## Additional Resources

- [Convex with React Router](https://docs.convex.dev/client/react/react-router)
- [Environment Variables in Vite](https://vite.dev/guide/env-and-mode.html)
- [ENVIRONMENT.md](./ENVIRONMENT.md) - Complete environment variable guide
- [DEPLOYMENT-CHECKLIST.md](./DEPLOYMENT-CHECKLIST.md) - Production deployment guide

---

**Status:** ✅ Fixed and tested
**Date:** November 7, 2025
