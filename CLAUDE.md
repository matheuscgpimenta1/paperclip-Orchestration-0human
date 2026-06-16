# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Project Is

Paperclip is an open-source **orchestration control plane for AI-agent companies**. It manages multiple AI agents (Claude Code, Codex, Cursor, Gemini, OpenClaw, etc.) inside a company structure with org charts, budgets, governance, task tracking, approval gates, and audit logging. Read `doc/GOAL.md`, `doc/PRODUCT.md`, and `doc/SPEC-implementation.md` (the concrete V1 build contract) before making significant changes.

## Commands

```sh
pnpm install          # install dependencies (Node 20+, pnpm 9.15.4)
pnpm dev              # full-stack dev with watch (API + UI at http://localhost:3100)
pnpm dev:once         # single run without watch, auto-applies pending migrations
pnpm dev:list         # inspect the running dev process
pnpm dev:stop         # stop the managed dev runner
pnpm test             # run Vitest unit/integration suite (default verification)
pnpm test:watch       # interactive Vitest watch mode
pnpm test:e2e         # Playwright browser tests (opt-in, not the default)
pnpm -r typecheck     # TypeScript check across entire monorepo
pnpm build            # build all packages
pnpm db:generate      # compile schema and generate migration files
pnpm db:migrate       # apply migrations manually
```

Run a single Vitest test file:
```sh
pnpm vitest run path/to/file.test.ts
```

Reset the local embedded database:
```sh
rm -rf ~/.paperclip/instances/default/db
pnpm dev
```

Full pre-PR-handoff check (only when change scope is broad):
```sh
pnpm -r typecheck && pnpm test:run && pnpm build
```

For normal issue work, run the **narrowest** relevant check first. Don't default to repo-wide typecheck/build/test on every heartbeat.

## Architecture

**Monorepo** managed with pnpm workspaces (TypeScript project references for incremental builds):

| Directory | Purpose |
|---|---|
| `server/` | Express REST API + WebSocket server + orchestration services |
| `ui/` | React 19 + Vite frontend (served by the API server at the same origin in dev) |
| `packages/db/` | Drizzle ORM schema, migrations, DB client (PostgreSQL / embedded PGlite) |
| `packages/shared/` | Shared types, constants, Zod validators, API path constants |
| `packages/adapters/` | Per-agent adapter implementations (claude-local, codex-local, cursor-local, gemini-local, openclaw-gateway, etc.) |
| `packages/adapter-utils/` | Shared adapter utilities |
| `packages/plugins/` | Plugin system SDK and example plugins |
| `cli/` | `pnpm paperclipai` CLI (onboarding, configuration, worktree management, control-plane commands) |
| `doc/` | Operational and product docs (the authoritative source for intended behavior) |
| `tests/` | E2E and release-smoke Playwright suites |

**Key architectural facts:**
- The database schema in `packages/db/src/schema/` is the source of truth. Changes propagate: schema → `packages/shared` types → `server` routes/services → `ui` API clients.
- In dev, `DATABASE_URL` is left unset and the server uses embedded PGlite (persisted at `~/.paperclip/instances/default/db`). No manual database setup is needed.
- The UI is served by the API server as dev middleware — they share origin; the Vite dev server is not a separate process.
- Agents wake on a heartbeat/cron/webhook model. Atomic task checkout (`in_progress` lock) prevents double-work. Budget hard-stops auto-pause agents. Every mutation is activity-logged.
- Multi-tenancy: all domain entities are scoped to a `company_id`. Cross-company access must never be possible.

## Core Engineering Rules

1. **Company scope**: every domain entity belongs to a company. Enforce the boundary in routes and services.

2. **Keep contracts synchronized**: if you change schema/API behavior, update all four layers together:
   - `packages/db` schema and exports
   - `packages/shared` types/constants/validators
   - `server` routes/services
   - `ui` API clients and pages

3. **Preserve control-plane invariants**:
   - Single-assignee task model (atomic checkout)
   - Approval gates for governed actions
   - Budget hard-stop auto-pause behavior
   - Activity log entries for all mutating actions

4. **Database change workflow**:
   1. Edit `packages/db/src/schema/*.ts`
   2. Export new tables from `packages/db/src/schema/index.ts`
   3. `pnpm db:generate` (compiles schema, then generates migration)
   4. `pnpm -r typecheck` to validate

5. **API conventions**: base path `/api`. Return `400/401/403/404/409/422/500`. Apply company access checks, actor permission checks (board vs agent), and activity log entries on every mutating endpoint.

6. **Do not commit `pnpm-lock.yaml`** in pull requests. CI (GitHub Actions) owns the lockfile; PRs that touch `package.json` files trigger automated lockfile regeneration.

## Pull Requests

Every PR must follow `.github/PULL_REQUEST_TEMPLATE.md` — do not write an ad-hoc body. Required sections:

- **Thinking Path** — top-down reasoning from what Paperclip is → subsystem → problem → this PR (5–8 blockquote steps)
- **What Changed** — bullet list of concrete changes
- **Verification** — test commands or manual steps; screenshots for UI changes
- **Risks** — migration safety, breaking changes, or "Low risk"
- **Model Used** — provider, exact model ID, relevant capabilities (e.g., `claude-sonnet-4-6, tool use, 200k context`). Write "None — human-authored" if applicable.
- **Checklist** — all items checked including Greptile review

A change is **done** when: behavior matches `doc/SPEC-implementation.md`, typecheck/tests/build pass, contracts are synced across all layers, docs are updated if behavior changed, and the PR body uses the template with all sections filled.

## Plan Documents

When creating a plan file inside the repo, use `doc/plans/YYYY-MM-DD-slug.md`. If a Paperclip issue asks for a plan, update the issue's `plan` document via the `paperclip` skill instead of creating a repo file.

## Fork-Specific Notes (HenkDz/paperclip)

This is a fork with QoL UI patches and an external-only Hermes adapter strategy on branch `feat/externalize-hermes-adapter`:

- Hermes is registered through Board → Adapter manager (not built into `server/` or `ui/` source)
- Fork runs on port 3101+ (auto-detects conflict with upstream on 3100)
- On NTFS: use `node node_modules/vite/bin/vite.js build` instead of `npx vite build` (hangs otherwise)
- Kill all paperclip processes before starting: `pkill -f "paperclip"; pkill -f "tsx.*index.ts"`
- Delete both Vite caches when builds seem stale: `rm -rf ui/dist ui/node_modules/.vite`

UI patches present in this fork (must be re-applied if re-copying source):
- `stderr_group` — amber accordion for MCP init noise in `RunTranscriptView.tsx`
- `tool_group` — accordion for consecutive non-terminal tool calls
- Dashboard excerpt — `LatestRunCard` strips markdown, shows first 3 lines/280 chars
