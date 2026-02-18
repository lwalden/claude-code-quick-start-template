# Demo: Planning and Building a Recipe API

A walkthrough of three sessions using AIAgentMinder. Shows `/plan`, `/handoff`, and the resume cycle.

---

## Session 1: Planning

> **User:** /plan

> **Claude:** Where are you with your project idea?
> - A) Rough concept — I have a vague idea but need help figuring everything out
> - B) Clear idea, no details — I know what I want but haven't worked out specifics
> - C) Partial plan or spec — I have some docs/notes, help me fill in gaps
> - D) Detailed plan or spec — I have a writeup, translate it into a roadmap

> **User:** B

> **Claude:** Great. Let's work through this in a few rounds.
>
> **Round 1 — Core understanding:**
> - What does this project do? (elevator pitch)
> - Who will use it?
> - What's the core problem it solves?
> - What makes it different from alternatives?

> **User:** It's a REST API for saving and sharing recipes. I want to be able to search by ingredient — like "what can I make with chicken and lemon?" The main users are home cooks. Most recipe apps search by name or category, not by what's actually in your fridge.

> **Claude:** Clear. Moving to scope.
>
> **Round 2 — Scope & technical:**
> - 3–5 must-have features for v1?
> - Features that can wait?
> - What is explicitly OUT of scope?
> - Stack preferences or constraints?
> - Target launch date?

> **User:** Must-have: user auth, recipe CRUD, ingredient search, favorites. Can wait: recipe import from URLs, images, social features. Out of scope: mobile app, meal planning, nutrition data. Stack: Node/Express/Postgres, I'm comfortable with those. No hard deadline.

> **Claude:** One more round — testing and quality.
>
> **Round 3 — Quality & testing:**
> How important is reliability? Any users beyond yourself?

> **User:** I want reasonable test coverage — this is a portfolio project so I want it done right, but I'm not building a production SaaS. Maybe Standard tier.

> **Claude:** Standard it is — unit + integration tests, CI on PRs. Confirming scope before I generate the roadmap:
>
> MVP: auth (JWT), recipe CRUD, ingredient search, favorites, basic rate limiting.
> Out of scope: URL import, image uploads, social, mobile, nutrition, meal planning.
> Stack: Node.js / Express / PostgreSQL. Standard quality tier.
>
> Does this capture it?

> **User:** Yes, add rate limiting to must-haves.

[Claude generates `docs/strategy-roadmap.md` — see `examples/strategy-roadmap.md` for the full output]

> **Claude:** Roadmap written to `docs/strategy-roadmap.md`. One question: do you prefer **lightweight one-liner ADRs** or **formal ADRs** with full context/consequences sections?

> **User:** Lightweight.

[Claude records `Format: Lightweight` in DECISIONS.md, populates MVP Goals in CLAUDE.md]

> **Claude:** Your roadmap is ready. Tell me to start Phase 1 when you're ready.

> **User:** /handoff

> **Claude:** *(rewrites PROGRESS.md)*

```
Phase: 1 - MVP
Last Updated: 2026-02-13

## Active Tasks
- None yet — planning complete, ready to scaffold

## Current State
- docs/strategy-roadmap.md generated. CLAUDE.md populated with MVP goals. No code written yet.

## Blockers
- None

## Next Priorities
1. Scaffold Express app — src/app.ts, src/db/pool.ts, .env.example, basic project structure
2. Implement auth endpoints — POST /auth/register, POST /auth/login with JWT
3. Set up Jest + Supertest with a test database schema

---
- 2026-02-13 Phase 1: Ran /plan, generated strategy-roadmap.md, chose Standard quality tier. Key files: docs/strategy-roadmap.md, CLAUDE.md. → Begin scaffold
```

> Session handoff complete.
> Next session should: scaffold the Express app and set up the test database.
> Blockers for human: create GitHub repo, provision PostgreSQL for staging.

---

## Session 2: Building

*(New Claude Code session. SessionStart hook fires — PROGRESS.md injected automatically.)*

> **Claude (hook output):**
> ```
> --- PROGRESS.md (session context) ---
> [contents of PROGRESS.md shown above]
>
> --- Tasks from PROGRESS.md ---
> [priority] Scaffold Express app — src/app.ts, src/db/pool.ts...
> [priority] Implement auth endpoints...
> [priority] Set up Jest + Supertest...
> ```

> **User:** Start on the next priority.

> **Claude:** I can see from PROGRESS.md that we're starting Phase 1 — planning is done, no code yet. I'll scaffold the Express app first, then move to auth.

[Claude creates src/app.ts, src/db/pool.ts, src/routes/index.ts, .env.example, package.json with dependencies]

[Claude runs `npm install` — express, pg, jsonwebtoken, zod, jest, supertest]

[Claude scaffolds auth routes in src/routes/auth.ts, writes failing tests in tests/auth.test.ts]

[Claude implements POST /auth/register and POST /auth/login — tests pass]

> **User:** Looks good. Now the recipe routes.

[Claude creates src/routes/recipes.ts with GET /recipes and GET /recipes/:id, writes integration tests, implements against real test DB — tests pass]

> **User:** /handoff

> **Claude:** *(rewrites PROGRESS.md)*

```
Phase: 1 - MVP
Last Updated: 2026-02-15

## Active Tasks
- Recipe mutation routes (POST, PUT, DELETE /recipes) — not yet started

## Current State
- Express app scaffolded and running. Auth endpoints complete (register, login, JWT issued).
- GET /recipes and GET /recipes/:id implemented with integration tests passing.
- Auth middleware exists (src/middleware/auth.ts) but not applied to mutation routes yet.

## Blockers
- Need DATABASE_URL for staging — waiting on human to provision RDS/Railway instance

## Next Priorities
1. Implement POST /recipes with Zod validation — schema stub in src/schemas/recipe.ts, wire into route
2. Apply auth middleware to POST/PUT/DELETE routes — only owner can mutate
3. Implement PUT /recipes/:id and DELETE /recipes/:id with ownership check (return 403 if not owner)

---
- 2026-02-15 Phase 1: Scaffolded Express app, auth endpoints, GET recipe routes with tests. Key files: src/app.ts, src/routes/auth.ts, src/routes/recipes.ts, src/middleware/auth.ts. → Recipe mutation routes
- 2026-02-13 Phase 1: Ran /plan, generated strategy-roadmap.md, chose Standard quality tier. Key files: docs/strategy-roadmap.md, CLAUDE.md. → Begin scaffold
```

> **Claude:** Also logging the Zod decision — you mentioned wanting to use it for validation.

[Claude adds to DECISIONS.md:]

```
### Input Validation | 2026-02-15 | Status: Active
Chose: Zod over Joi and express-validator. Why: TypeScript-first — schemas double as type
definitions, eliminating runtime/compile mismatches. Tradeoff: developer is new to Zod
(slight learning curve). Joi considered but less idiomatic with TypeScript.
```

> Session handoff complete.
> This session: scaffolded app, implemented auth and read-only recipe routes with tests.
> Next session should: implement POST /recipes with Zod validation, then PUT/DELETE with auth.
> Blockers for human: provision staging database (Railway or RDS).

---

## Session 3: Continuing

*(Fresh Claude Code session. Hook injects PROGRESS.md and DECISIONS.md.)*

> **Claude (hook output):**
> ```
> --- PROGRESS.md (session context) ---
> [PROGRESS.md as written above]
>
> --- Tasks from PROGRESS.md ---
> [active] Recipe mutation routes (POST, PUT, DELETE /recipes) — not yet started
> [state] Express app scaffolded and running. Auth endpoints complete...
> [state] GET /recipes and GET /recipes/:id implemented with integration tests passing.
> [priority] Implement POST /recipes with Zod validation...
> [priority] Apply auth middleware to POST/PUT/DELETE routes...
> [priority] Implement PUT /recipes/:id and DELETE /recipes/:id...
>
> --- DECISIONS.md (architectural decisions — do not re-debate) ---
> [DECISIONS.md contents]
> ```

> **User:** Resume.

> **Claude:** Picking up from the handoff. I can see:
> - GET routes are done with tests
> - Auth middleware exists but isn't applied to mutation routes yet
> - POST /recipes needs Zod validation wired in (schema stub already exists)
>
> I'll start with the failing test for POST /recipes, then implement.

[Claude writes failing test: POST /recipes without auth returns 401, with auth + valid body returns 201]

[Claude wires auth middleware into POST/PUT/DELETE routes]

[Claude connects Zod schema to POST /recipes route — validation errors return 400 with field-level messages]

[Tests pass — commits on feature branch]

> **User:** Good. Wire the same pattern into PUT and DELETE.

[Claude implements PUT /recipes/:id with ownership check — returns 403 if req.user.id !== recipe.owner_id]

[Claude implements DELETE /recipes/:id with same ownership check]

[All tests pass — Claude commits]

> **User:** /handoff

> **Claude:** *(rewrites PROGRESS.md)*

```
Phase: 1 - MVP
Last Updated: 2026-02-17

## Active Tasks
- Ingredient search endpoint (GET /recipes?ingredients=...) — not yet started

## Current State
- Full recipe CRUD complete with auth protection and ownership checks. All integration tests passing.
- Zod validation on POST/PUT — invalid bodies return 400 with field-level errors.
- Auth middleware applied to all mutation routes. GET routes remain public.

## Blockers
- Need DATABASE_URL for staging before deploy — still waiting on Railway provisioning

## Next Priorities
1. Implement ingredient search — GET /recipes?ingredients=chicken,garlic should return recipes containing ALL listed ingredients; use SQL array overlap or unnest join on ingredients table
2. Implement favorites — POST/DELETE /users/me/favorites/:recipeId, GET /users/me/favorites
3. Add rate limiting middleware (express-rate-limit) — 100 req/min per IP, 429 + Retry-After header

---
- 2026-02-17 Phase 1: Implemented POST/PUT/DELETE /recipes with Zod validation and auth/ownership checks. Key files: src/routes/recipes.ts, src/schemas/recipe.ts, src/middleware/auth.ts. → Ingredient search
- 2026-02-15 Phase 1: Scaffolded Express app, auth endpoints, GET recipe routes with tests. Key files: src/app.ts, src/routes/auth.ts, src/routes/recipes.ts. → Recipe mutation routes
- 2026-02-13 Phase 1: Ran /plan, generated strategy-roadmap.md, chose Standard quality tier. Key files: docs/strategy-roadmap.md, CLAUDE.md. → Begin scaffold
```

> Session handoff complete.
> This session: completed recipe CRUD with auth and Zod validation.
> Next session should: implement ingredient search (the core differentiating feature), then favorites, then rate limiting.
> Blockers for human: provision Railway PostgreSQL for staging.
