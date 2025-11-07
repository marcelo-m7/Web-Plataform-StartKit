# Local Development Setup - Complete

## ✅ Configuration Applied

### Environment Variables Updated

**File:** `.env.local`

```bash
# Clerk API Keys - LOCAL DEVELOPMENT (Test Environment)
VITE_CLERK_PUBLISHABLE_KEY=pk_test_c3Ryb25nLXF1ZXR6YWwtMTUuY2xlcmsuYWNjb3VudHMuZGV2JA
VITE_CLERK_FRONTEND_API_URL=https://strong-quetzal-15.clerk.accounts.dev
CLERK_SECRET_KEY=sk_test_cohw8pF3iC9dboyBmq3cdNcahWf969EhaSM4BCYfir
```

**Status:** ✅ Using Clerk Test Environment

### Development Server

- **URL:** http://localhost:5173/
- **Status:** ✅ Running
- **Environment:** Local Development

---

## 🧪 Manual Testing Checklist

### Test 1: Homepage Loads
- [ ] Open http://localhost:5173/
- [ ] Page loads without 500 errors
- [ ] Pricing section visible (may be empty if Convex not configured)
- [ ] No console errors related to authentication

### Test 2: Sign-Up Flow

**Steps:**
1. Navigate to http://localhost:5173/sign-up
2. Fill in email and password
3. Complete sign-up process
4. Verify redirected to homepage or dashboard

**Expected Behavior:**
- ✅ Clerk sign-up component loads
- ✅ Can create new test account
- ✅ Email verification works (if enabled in Clerk)
- ✅ Successful redirect after sign-up
- ✅ User appears in Clerk dashboard

**Common Issues:**
- If Clerk component doesn't load: Check `VITE_CLERK_PUBLISHABLE_KEY` is correct
- If redirect fails: Check `FRONTEND_URL=http://localhost:5173` in `.env.local`
- If "Application not found": Verify Clerk test keys match the application

### Test 3: Sign-In Flow

**Steps:**
1. Navigate to http://localhost:5173/sign-in
2. Enter email and password of existing test user
3. Sign in
4. Verify redirected to homepage

**Expected Behavior:**
- ✅ Clerk sign-in component loads
- ✅ Can sign in with test credentials
- ✅ Successful redirect after sign-in
- ✅ Session persists across page reloads

### Test 4: Protected Routes (Dashboard)

**Steps:**
1. Sign out (if signed in)
2. Try to access http://localhost:5173/dashboard
3. Should redirect to sign-in page
4. Sign in
5. Try to access dashboard again

**Expected Behavior:**
- ✅ Unauthenticated users redirected to /sign-in
- ✅ Authenticated users can access dashboard
- ✅ After sign-in, redirected to intended page

**Note:** Dashboard may show "Subscription Required" page if user doesn't have active subscription. This is expected behavior.

### Test 5: User Menu & Sign Out

**Steps:**
1. Sign in
2. Look for user menu/profile in navigation
3. Click sign out
4. Verify redirected to homepage and session cleared

**Expected Behavior:**
- ✅ User profile shows in UI when authenticated
- ✅ Sign out clears session
- ✅ After sign out, protected routes inaccessible

---

## 🔍 Troubleshooting Guide

### Issue: "Invalid publishable key"

**Solution:**
```bash
# Verify the key in .env.local starts with pk_test_
VITE_CLERK_PUBLISHABLE_KEY=pk_test_c3Ryb25nLXF1ZXR6YWwtMTUuY2xlcmsuYWNjb3VudHMuZGV2JA
```

### Issue: "Application not found"

**Causes:**
- Wrong Clerk publishable key
- Clerk application deleted or disabled
- Mismatch between keys and frontend URL

**Solution:**
1. Log into https://dashboard.clerk.com
2. Verify the test application exists
3. Copy fresh API keys from dashboard
4. Update `.env.local`
5. Restart dev server

### Issue: Infinite redirect loop

**Causes:**
- FRONTEND_URL mismatch
- Incorrect redirect URLs in Clerk dashboard

**Solution:**
```bash
# Ensure FRONTEND_URL matches your actual URL
FRONTEND_URL=http://localhost:5173

# In Clerk Dashboard:
# - Go to "Paths" or "Redirect URLs"
# - Add: http://localhost:5173
```

### Issue: Sign-up/Sign-in pages show blank

**Causes:**
- Clerk component not rendering
- JavaScript error in console

**Solution:**
1. Open browser DevTools (F12)
2. Check Console for errors
3. Verify `<SignIn />` and `<SignUp />` components in code
4. Check network tab for failed API calls to Clerk

### Issue: "Failed to fetch plans" or Convex errors

**Status:** ⚠️ **Expected for Local Development**

**Explanation:**
- You're using production Convex URL
- Polar.sh actions require environment variables in Convex dashboard
- Homepage will load but pricing section may be empty

**Not Critical for Testing Auth:**
- Sign-up/Sign-in flows work independently of Convex
- You can still test authentication completely

**To Fix (Optional):**
```bash
# Option 1: Run Convex locally
npx convex dev

# Option 2: Set environment variables in Convex dashboard
# Go to your Convex project settings
# Add all env vars from .env.local
```

---

## 🎯 Test Results Template

Use this template to document your testing:

```
## Test Session: [Date/Time]

### Environment
- Dev Server: ✅ Running on http://localhost:5173/
- Clerk Environment: Test (pk_test_...)
- Convex: Production URL (may have connection issues)

### Test Results

#### 1. Homepage Load
- Status: [ ] Pass / [ ] Fail
- Notes: 

#### 2. Sign-Up Flow
- Status: [ ] Pass / [ ] Fail
- Test Email: 
- Issues Encountered:

#### 3. Sign-In Flow  
- Status: [ ] Pass / [ ] Fail
- Issues Encountered:

#### 4. Protected Routes
- Status: [ ] Pass / [ ] Fail
- Dashboard Access: [ ] Redirects when not authenticated
- Issues Encountered:

#### 5. Sign-Out
- Status: [ ] Pass / [ ] Fail
- Session Cleared: [ ] Yes / [ ] No

### Overall Status
- [ ] All authentication tests passing
- [ ] Ready for further development
- [ ] Issues to resolve (list below):

### Issues Found
1. 
2. 
3. 

### Screenshots
(Attach screenshots of any errors or unexpected behavior)
```

---

## 📝 Quick Reference

### URLs for Testing

```
Homepage:        http://localhost:5173/
Sign-Up:         http://localhost:5173/sign-up
Sign-In:         http://localhost:5173/sign-in
Dashboard:       http://localhost:5173/dashboard
Pricing:         http://localhost:5173/pricing
```

### Test User Credentials

Create test users in Clerk dashboard or during sign-up testing.

**Recommended Test Accounts:**
- Email: test1@example.com
- Email: test2@example.com
- Email: admin@example.com

(Use any password - Clerk handles validation)

### Key Files to Monitor

If issues occur, check these files:

```
.env.local                          # Environment variables
app/routes/sign-in.tsx              # Sign-in page
app/routes/sign-up.tsx              # Sign-up page
app/routes/dashboard/layout.tsx     # Protected route logic
app/root.tsx                        # Clerk provider setup
convex/auth.config.ts               # Convex auth config
```

---

## ✅ Success Criteria

Your local development setup is successful when:

1. ✅ Dev server starts without errors
2. ✅ Sign-up page loads Clerk component
3. ✅ Can create new test account
4. ✅ Sign-in page loads Clerk component
5. ✅ Can sign in with test account
6. ✅ Dashboard redirects unauthenticated users
7. ✅ Can sign out successfully

**Note:** Convex/Polar errors are expected and won't prevent authentication testing.

---

## 🚀 Next Steps After Testing

Once authentication is working:

1. **Configure Convex Environment Variables**
   - Set all `.env.local` variables in Convex dashboard
   - Test subscription flows

2. **Test Payment Integration**
   - Create test products in Polar sandbox
   - Test checkout flow
   - Verify webhooks

3. **Test Full User Journey**
   - Sign up → Subscribe → Access Dashboard
   - Complete end-to-end flow

---

**Setup Date:** November 7, 2025
**Environment:** Local Development with Clerk Test Keys
**Status:** ✅ Ready for Testing

---

## 🔗 Resources

- [Clerk Test Mode Docs](https://clerk.com/docs/testing/test-mode)
- [Clerk React Router Integration](https://clerk.com/docs/references/react-router)
- [Local Development Guide](./ENVIRONMENT.md)
- [Bug Fixes Applied](./BUGFIX.md)
