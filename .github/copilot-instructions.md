## Quick context

This repository is a React Router v7 full-stack starter with Convex backend functions, Clerk auth, Polar.sh billing, and OpenAI-based chat. Key places to look:

- Frontend entry + layout: `app/root.tsx` (Convex client configured via `VITE_CONVEX_URL`, Clerk integration).
- Route map: `app/routes.ts` — explicit route-to-file mapping (use `index`, `route`, `layout`).
- Routes/components: `app/routes/*.tsx` and `app/components/*` (UI primitives live in `app/components/ui`).
- Backend functions and HTTP routes: `convex/*` (see `convex/http.ts` for webhook and chat endpoints).
- Schema & indexes: `convex/schema.ts` (users, subscriptions, webhookEvents) — indexes are relied on by queries.
- Build/dev: `package.json` scripts (`dev`, `build`, `start`, `typecheck`).

## What matters for coding tasks

- Routing is not file-system automatic; `app/routes.ts` maps URLs to `app/routes/*.tsx`. Add a route there and then create the corresponding file.
- Client ↔ Convex connection uses `ConvexReactClient(import.meta.env.VITE_CONVEX_URL)` in `app/root.tsx`. Runtime expects `VITE_` prefixed env for client-side usage.
- Server-side Convex functions live in `convex/`. HTTP endpoints are declared in `convex/http.ts` (e.g., `/api/chat`, `/payments/webhook`). Respect CORS headers defined there (they reference `FRONTEND_URL`).
- Sensitive values: put secrets in `.env.local` (do not commit). Environment names used across code:
  - `VITE_CONVEX_URL`, `CONVEX_DEPLOYMENT`
  - `VITE_CLERK_PUBLISHABLE_KEY`, `CLERK_SECRET_KEY`
  - `POLAR_ACCESS_TOKEN`, `POLAR_ORGANIZATION_ID`, `POLAR_WEBHOOK_SECRET`
  - `OPENAI_API_KEY`, `FRONTEND_URL`

## Local dev & verification steps (explicit)

1. Install deps: `npm install`.
2. Run Convex locally if iterating on backend functions: `npx convex dev` (README/convex hints). Keep this running while testing Convex endpoints.
3. Start frontend dev server: `npm run dev` (uses `react-router dev`). App expects Convex dev or a deployed `VITE_CONVEX_URL`.
4. Type generation and checks: `npm run typecheck` (runs `react-router typegen && tsc`). Run this after adding routes or changing types.
5. Build for production: `npm run build`. Serve with `npm run start` (uses `react-router-serve`).

If testing webhooks locally, use a tunnel (ngrok or similar) and set `POLAR_WEBHOOK_SECRET` and the webhook URL in Polar.sh. Webhook path in code: `/payments/webhook`.

## Common coding patterns & conventions

- UI: shadcn-style components live under `app/components/ui/*.tsx` — follow existing props and className patterns.
- Data access: use Convex queries/mutations under `convex/*` and call them from client via generated `api.*` helpers (see `convex/_generated`).
- Server actions (chat streaming): `convex/http.ts` uses `@ai-sdk/openai` and `ai.streamText`. Respect streaming pattern when returning responses.
- Indexes in `convex/schema.ts` are relied upon by queries — avoid renaming indexes without updating queries.
- DNS prefetch and resource preload are handled in `app/root.tsx` (Links function) — be careful when editing head/link behavior for performance-sensitive code.

## Where to implement common changes (examples)

- Add a new top-level page: edit `app/routes.ts`, add `route("foo", "routes/foo.tsx")` and create `app/routes/foo.tsx`.
- Add a dashboard subroute: extend the `layout("routes/dashboard/layout.tsx", [...])` array in `app/routes.ts`.
- Add a Convex HTTP route: update `convex/http.ts` with `http.route({ path, method, handler })` and implement handler in `convex/*.ts`.
- Wire a new env var to client: add `VITE_NEW_KEY` and reference it with `import.meta.env.VITE_NEW_KEY` (client-side only if prefixed with `VITE_`).

## Safety & review notes for AI agents

- Never expose secret values in PRs (check `.env.local`, remove before committing). The codebase uses `FRONTEND_URL` and `VITE_*` envs—keep secrets out of commits.
- When changing data schema (`convex/schema.ts`), verify migration impact: check any queries/mutations that rely on field names and indexes.
- For API or webhook changes, run Convex locally (`npx convex dev`) and exercise endpoints (e.g., `/api/chat` and `/payments/webhook`) with realistic payloads.

## Quick file map (high-signal)

- `app/root.tsx` — providers (Clerk, Convex), error boundary, Links (font preconnects).
- `app/routes.ts` — route declarations (source of truth for URLs).
- `app/components/ui/` — UI primitives and shadcn-derived components.
- `convex/http.ts` — HTTP router with chat and webhook endpoints (CORS implemented here).
- `convex/schema.ts` — DB tables and indexes (users, subscriptions, webhookEvents).
- `package.json` — scripts and deps (run `npm run dev`, `npx convex dev`, `npm run typecheck`).

If anything here is unclear or you'd like me to expand any section (example PR templates, testing scripts, or typegen steps), say which area and I'll iterate.  
---
Small ask: confirm whether you'd like AI agent instructions to include a short PR checklist (tests, typegen, env checks) and I will add it. 
