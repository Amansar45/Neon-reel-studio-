# ReelForge

AI-powered short video generator that turns any text prompt into vertical 9:16 reels for TikTok, Instagram Reels, and YouTube Shorts.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/reel-generator run dev` — run the frontend (port 25846)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Tailwind CSS + Framer Motion
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — single source of truth for all API contracts
- `lib/db/src/schema/` — Drizzle ORM table definitions (users, reel_jobs, example_reels, sessions)
- `artifacts/api-server/src/routes/` — Express route handlers (auth, reels, user)
- `artifacts/reel-generator/src/pages/` — React pages (home, generator, dashboard, pricing, login, register)
- `artifacts/reel-generator/src/components/` — Shared components (Navbar, Footer)

## Architecture decisions

- Token-based auth stored in localStorage with `rf_token` key; tokens are attached as `Authorization: Bearer <token>` on API calls
- Video generation is simulated server-side with time-based progress (completed at ~30 seconds after creation)
- Job status polling uses Orval-generated hooks with `refetchInterval` that stops on terminal states
- The `useGetMe` hook is enabled only when `rf_token` exists in localStorage
- Example reels are seeded via `executeSql` at setup time; content uses Unsplash for thumbnails

## Product

- **Home** (`/`) — Hero, style cards (Sigma/Anime Motivation/Sad Aesthetic), example reels gallery, features, CTA
- **Generator** (`/generate`) — Prompt input, style selector, music selector, image upload, generate button, 9:16 video preview with progress animation, download button
- **Dashboard** (`/dashboard`) — Total reels, credits, plan, style breakdown, recent reel history
- **Pricing** (`/pricing`) — Free ($0/3 reels) / Pro ($12/50 reels) / Ultra ($29/unlimited) plans
- **Auth** (`/login`, `/register`) — Full auth forms with JWT-like token sessions

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Run `pnpm --filter @workspace/api-spec run codegen` after every OpenAPI spec change before touching frontend or backend code
- The DB `lib/db/src/index.ts` throws if `DATABASE_URL` is not set — ensure the database is provisioned
- Video generation uses a time-simulation approach; real FFmpeg/Remotion would replace `simulateProgress()` in `routes/reels.ts`

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
