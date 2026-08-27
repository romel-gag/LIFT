# LIFT Workflow Project Initialization: Existing Project Onboarding

Version: 2.1 (stack-agnostic + Postman CI sync)
Based on: Romel Tech, LIFT v1.0 (July 2026)

You are an AI software engineering assistant joining an existing software project. The project may use **any language, framework, or architecture** — a traditional server-rendered app, a REST or GraphQL API, a SPA, a mobile app, a monorepo, a set of microservices, a serverless stack, or any combination.

Your first responsibility is to understand this system, document its architecture, and create an AI-friendly knowledge base **before making any code changes**.

Do not implement features.
Do not refactor code.
Do not modify application logic.

Your goal is to create project understanding and documentation.

> **One sanctioned exception:** Phase 6 asks you to create a single CI workflow file (`.github/workflows/sync-postman-collection.yml`). This is an explicit deliverable of this initialization and is the **only** functional (non-documentation) file you may create. You must still not touch application source code.

---

# Guiding Principle: Detect, Then Map

Do not assume any specific technology. For every analysis area below, follow the same procedure:

1. **Detect** what the project actually uses (read manifest/lockfiles, config, folder structure, and imports).
2. **Map** the generic concept to that stack's idiomatic equivalent.
3. **Document** what exists. Skip cleanly anything that does not apply, and note anything that is missing but expected.

Use these signals to identify the stack quickly:

* **Manifest / dependency files** — e.g. `package.json`, `composer.json`, `requirements.txt` / `pyproject.toml`, `go.mod`, `Cargo.toml`, `pom.xml` / `build.gradle`, `Gemfile`, `*.csproj`, `pubspec.yaml`.
* **Lockfiles** — to pin exact versions actually installed.
* **Config & tooling** — framework config files, build tools, linters, formatters, `.env` templates.
* **Runtime / version files** — e.g. `.nvmrc`, `.python-version`, `.tool-versions`, `Dockerfile`, engine fields.
* **Directory conventions** — framework-specific folders reveal architecture (routing dirs, `src/`, `app/`, domain folders, etc.).

Only after detection do you apply the relevant analysis lenses below.

---

# Phase 1: Project Discovery (L — Learn)

Analyze the existing repository top to bottom.

## Project Overview

Identify:

* Application purpose
* Target users
* Main business workflows
* Existing features
* Current development status (prototype, MVP, production, maintenance)
* Technology stack (all detected languages, frameworks, and major libraries with versions)
* Repository shape (single app, monorepo, polyrepo, microservices)
* External integrations (payments, auth providers, email/SMS, storage, analytics, third-party APIs)

---

## Server-Side / Backend Analysis

Only if a backend exists. Detect the language and framework first (e.g. Node/Express/NestJS, Laravel/Symfony, Django/FastAPI/Flask, Rails, Spring, ASP.NET, Go, Rust, etc.), then analyze its idiomatic equivalents of the following. The vocabulary differs per stack; the concepts are universal.

* **Language, runtime, and framework versions**
* **Project / module structure** and architectural style (MVC, layered, hexagonal, clean, modular monolith, microservices, serverless functions)
* **Entry points & routing** — how requests map to handlers (routes, controllers, route handlers, resolvers, endpoints, function triggers)
* **Request handling** — controllers / handlers / resolvers
* **Data models** — ORM entities, schemas, or data classes (e.g. Eloquent, Prisma, TypeORM, Sequelize, Mongoose, Django ORM, ActiveRecord, JPA, EF Core)
* **Input validation** — request validation / DTOs / schema validation (e.g. Form Requests, Zod, class-validator, Pydantic, serializers)
* **Response / serialization layer** — how output is shaped (API Resources, serializers, DTOs, GraphQL types)
* **Business logic organization** — services, use-cases, domain layer, or where logic actually lives
* **Asynchronous / background work** — jobs, queues, workers, cron, scheduled tasks, message consumers
* **Events & messaging** — events, listeners, pub/sub, webhooks, event bus, message brokers
* **Authorization** — policies, guards, middleware, RBAC/ABAC, permission checks
* **Cross-cutting middleware** — middleware, interceptors, filters, decorators
* **Data access & migrations** — repository/query layer, migration tooling
* **Seed / fixture data**
* **Automated tests** — unit, integration, feature/e2e; coverage and gaps
* **Configuration & secrets management**

Document:

* Authentication flow
* Authorization rules
* Business logic organization
* API conventions (REST/GraphQL/RPC, versioning, naming, error format, pagination)

> **Note for Phase 6:** While analyzing routing, explicitly record **where API routes are defined** (the exact directory/glob, e.g. `routes/*.js`, `src/routes/**/*.js`, `src/routes/**/*.ts`) and **how routes are declared** (e.g. Express/Fastify/Koa `app.get(...)` / `router.post(...)`, NestJS `@Get()` decorators, or Next.js file-based API routes). Phase 6 depends on this.

---

## Client / Frontend Application Analysis (Web or Mobile)

Only if a client app exists. Detect the framework first (e.g. React, Vue, Angular, Svelte, SolidJS, Next.js/Nuxt/Remix/TanStack, or mobile: Flutter, React Native, native iOS/Android), then analyze its idiomatic equivalents.

* **Language and framework versions**
* **Folder / project structure**
* **UI & component patterns** — component architecture, design system, shared primitives, styling approach (CSS modules, Tailwind, styled-components, native styles)
* **State management** — local vs global; e.g. Redux, Zustand, Pinia, Vuex, Context, signals, Riverpod/Bloc/Provider (mobile)
* **Data-fetching & caching layer** — e.g. TanStack Query, SWR, Apollo, RTK Query, custom clients, repository pattern
* **API communication** — HTTP/GraphQL clients, generated SDKs, interceptors, auth token handling
* **Routing / navigation** — router configuration, guards, deep linking (mobile)
* **Local / persistent storage** — cookies, localStorage/IndexedDB, secure storage, SQLite, Hive/Isar (mobile)
* **Offline / sync capabilities** (if any)
* **Rendering strategy** (SPA, SSR, SSG, ISR, hybrid) where relevant
* **Build tooling** — bundler/compiler, dev server, environment handling
* **Automated tests** — component, unit, e2e (e.g. Vitest/Jest, Testing Library, Playwright/Cypress, Flutter test)

Document:

* Application / user flow
* Screen or page structure
* Data flow through the app
* How the client communicates with the backend

---

## Database & Data Store Analysis

Detect all persistence layers (relational, document, key-value, search, cache, blob storage), then analyze:

* Engine(s) and version(s)
* Tables / collections / entities
* Relationships (and how they're modeled)
* Indexes
* Constraints and data integrity rules
* Migration strategy and current schema source of truth
* Important business entities and their lifecycle
* Caching layers and what they cache (e.g. Redis, Memcached)

---

## Infrastructure & Operations Analysis

Analyze:

* Local development setup (how a new dev runs it — scripts, containers, Makefile, dev commands)
* Environment variables and configuration strategy
* Containerization / orchestration (Docker, Compose, Kubernetes) if present
* Build process
* Deployment process and target environments
* Hosting / platform (cloud provider, PaaS, serverless, on-prem)
* CI/CD pipelines
* External services and their roles
* Observability (logging, monitoring, error tracking) if present

---

## Existing Problems Analysis

Search the repository for:

* `TODO` comments
* `FIXME` / `HACK` / `XXX` comments
* Deprecated code and dependencies
* Error handling gaps
* Known limitations
* Temporary workarounds
* Missing or outdated documentation
* Security-sensitive patterns (hardcoded secrets, missing validation, unsafe queries)

---

# Phase 2: Create AI Project Memory

After understanding the system, create an `/ai` directory with this structure:

```
/ai
├── project-context.md
├── architecture.md
├── database-schema.md
├── api-contracts.md
├── coding-standards.md
├── deployment.md
│
├── features/
│   └── feature-overview.md
│
├── activity-diagrams/                 # created only when I ask — see "On-Demand Tasks" below
│   └── <feature>/
│       └── activity-diagram-<feature>-0001.html
│
├── test-cases/                        # created only when I ask — see "On-Demand Tasks" below
│   └── <feature>/
│       └── test-cases-<feature>-0001.html
│
├── issues/
│   ├── known-issues.md
│   ├── technical-debt.md
│   ├── bugs-fixed.md
│   └── troubleshooting.md
│
└── decisions/
    └── ADR-001-project-initialization.md
```

Populate every file from your analysis. Keep the documentation **stack-accurate** — describe the tools this project actually uses, not generic placeholders. If a category does not apply (e.g. no mobile app, no queues), state that briefly rather than inventing content.

`activity-diagrams/` and `test-cases/` are the two exceptions: **do not create them or anything inside them during initialization.** They are built only on explicit request — see the *On-Demand Task* sections near the end of this document.

---

# Phase 3: Create AI Development Rules

Create `/AGENTS.md`.

It should contain:

* LIFT workflow rules
* Coding standards **derived from the existing codebase** (match the project's real conventions, linter/formatter config, and idioms — do not impose foreign conventions)
* AI behavior rules
* Documentation update rules
* Activity diagram rules — created **only on explicit request**, never automatically; one subdirectory per feature under `/ai/activity-diagrams/`, self-contained HTML, append-only 4-digit versioning (`activity-diagram-<feature>-0001.html`), never edit or delete an existing version
* Test case rules — created **only on explicit request**, never automatically; one subdirectory per feature under `/ai/test-cases/`, self-contained HTML, append-only 4-digit versioning (`test-cases-<feature>-0001.html`), never edit or delete an existing version
* Issue tracking rules
* Architecture decision rules

The AI must follow this workflow for all future development tasks:

```
Learn
 ↓
Intend
 ↓
Forge
 ↓
Tune
```

* **Learn** — understand the relevant part of the system and existing conventions before writing anything.
* **Intend** — state the plan and intended changes; confirm scope.
* **Forge** — implement, following the project's established patterns.
* **Tune** — test, refine, and update the `/ai` documentation to reflect the change. If the change alters the behaviour of a feature that already has an activity diagram or a test case document, **flag that it is now out of date and stop** — do not create or update either unless I ask.

---

# Phase 4: Review Existing Documentation

Review `README.md`.

If it exists:

* Improve missing sections.
* Keep existing useful information.
* Do not remove project-specific instructions.

If it does not exist, create it, including:

* Project overview
* Technology stack
* Requirements / prerequisites
* Installation steps
* Environment setup
* Running instructions (all runnable apps/services)
* Testing instructions
* Deployment overview

Also add (or update) a **"Required CI secrets"** subsection listing the GitHub secrets needed by the Postman sync workflow created in Phase 6 (names only — never real values):

* `POSTMAN_API_KEY`
* `POSTMAN_COLLECTION_ID`
* `PROD_API_BASE_URL`
* `STAGING_API_BASE_URL`

---

# Phase 5: Create Initial Architecture Decision Record

Create `/ai/decisions/ADR-001-project-initialization.md`.

## Context

The project was analyzed and AI documentation was introduced.

## Decision

The project will use:

* `AGENTS.md` for AI behavior rules
* `/ai` directory for project memory
* The LIFT workflow for the development process
* A GitHub Actions workflow to auto-sync new API endpoints into a shared Postman collection (see Phase 6)

## Consequences

Benefits:

* Better AI understanding of the system
* Easier onboarding for humans and AI
* Consistent, repeatable development workflow
* API endpoints stay reflected in Postman without manual entry

Limitations:

* Documentation must be maintained as the system evolves
* Route auto-detection is diff/grep-based and only as reliable as the route declaration style it is tuned for

---

# Phase 6: CI Automation — Postman Endpoint Sync

Create the file `.github/workflows/sync-postman-collection.yml`.

This is a GitHub Actions workflow. On every push to `main` or `staging`, it diffs the pushed commits, detects newly added API route declarations, and registers each one as a request in a Postman collection via the Postman API. This is the **only** functional file this initialization is allowed to create; do not modify any application source.

**Before writing the file, apply Detect, Then Map** using what Phase 1 recorded:

1. Set the `git diff` path glob to the project's **actual** routes location (default assumption below is `routes/*.js` + `src/routes/**/*.js`).
2. Confirm the route-declaration style. The template below matches **Express/Fastify/Koa**-style method calls (`app.get(...)`, `router.post(...)`). If the project uses **NestJS decorators** (`@Get('/x')`) or **Next.js file-based API routes**, adapt the detection `grep -E` pattern accordingly, or — if reliable detection isn't feasible — write the file anyway but add a comment at the top of the workflow explaining the limitation and documenting the manual fallback in `/ai/deployment.md`.

**Secrets.** These four values must be configured as GitHub repository (or environment) secrets by a human — never hardcode real values into the workflow:

* `POSTMAN_API_KEY` — Postman API key
* `POSTMAN_COLLECTION_ID` — target collection ID
* `PROD_API_BASE_URL` — base URL used for the `main` branch
* `STAGING_API_BASE_URL` — base URL used for the `staging` branch

> The original version of this workflow hardcoded the collection ID and used the same ID for both branches. This version reads it from `secrets.POSTMAN_COLLECTION_ID`. If you actually want a **different** collection per environment, split it into `PROD_POSTMAN_COLLECTION_ID` / `STAGING_POSTMAN_COLLECTION_ID` and set each in the branch block — otherwise leave as-is.

Write the workflow with this content (adjusting the route glob/regex per detection):

```yaml
name: Sync New API Endpoint to Postman Collection

on:
  push:
    branches:
      - main
      - staging

jobs:
  add-to-postman:
    name: Add New Endpoint to Postman Collection
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Set environment variables based on branch
        run: |
          if [ "${{ github.ref_name }}" == "main" ]; then
            echo "ENV_NAME=Production" >> $GITHUB_ENV
            echo "BASE_URL=${{ secrets.PROD_API_BASE_URL }}" >> $GITHUB_ENV
          elif [ "${{ github.ref_name }}" == "staging" ]; then
            echo "ENV_NAME=Staging" >> $GITHUB_ENV
            echo "BASE_URL=${{ secrets.STAGING_API_BASE_URL }}" >> $GITHUB_ENV
          fi

      - name: Detect new API endpoints from diff
        id: detect
        run: |
          echo "Running on branch: ${{ github.ref_name }} (${{ env.ENV_NAME }})"

          BEFORE_SHA="${{ github.event.before }}"
          if [ -z "$BEFORE_SHA" ] || ! git cat-file -e "$BEFORE_SHA" 2>/dev/null; then
            BEFORE_SHA="HEAD~1"
          fi

          # JavaScript route detection (Express/Fastify/Koa style: app.get(...) / router.post(...)).
          # Adjust the path glob to match this project's actual routes location.
          NEW_ROUTES=$(git diff "$BEFORE_SHA" HEAD -- 'routes/*.js' 'routes/**/*.js' 'src/routes/**/*.js' | \
            grep '^+' | \
            grep -v '^+++' | \
            grep -iE "\.(get|post|put|patch|delete)\s*\(" || true)

          echo "Detected new routes:"
          echo "$NEW_ROUTES"

          if [ -z "$NEW_ROUTES" ]; then
            echo "no_new_routes=true" >> $GITHUB_OUTPUT
          else
            echo "no_new_routes=false" >> $GITHUB_OUTPUT
            echo "new_routes<<EOF" >> $GITHUB_OUTPUT
            echo "$NEW_ROUTES" >> $GITHUB_OUTPUT
            echo "EOF" >> $GITHUB_OUTPUT
          fi

      - name: No new routes detected
        if: steps.detect.outputs.no_new_routes == 'true'
        run: echo "No new API routes detected in this push. Skipping Postman sync."

      - name: Push new endpoints to Postman
        if: steps.detect.outputs.no_new_routes == 'false'
        env:
          POSTMAN_API_KEY: ${{ secrets.POSTMAN_API_KEY }}
          POSTMAN_COLLECTION_ID: ${{ secrets.POSTMAN_COLLECTION_ID }}
        run: |
          echo "Syncing to Postman collection: ${POSTMAN_COLLECTION_ID} (${{ env.ENV_NAME }})"

          echo "${{ steps.detect.outputs.new_routes }}" | while IFS= read -r line; do
            # Extract HTTP method from the .method( call (not from a handler name)
            METHOD=$(echo "$line" | grep -oiE "\.(get|post|put|patch|delete)\s*\(" \
              | grep -oiE "(get|post|put|patch|delete)" | head -1 | tr '[:lower:]' '[:upper:]')

            # Extract the first quoted path (supports ' " and backticks)
            PATH_PART=$(echo "$line" | grep -oP "(?<=['\"\`])[^'\"\`]+(?=['\"\`])" | head -1)

            if [ -z "$METHOD" ] || [ -z "$PATH_PART" ]; then
              echo "Skipping unparseable line: $line"
              continue
            fi

            FULL_URL="${{ env.BASE_URL }}${PATH_PART}"
            ENDPOINT_NAME="[${{ env.ENV_NAME }}] ${METHOD} ${PATH_PART}"

            echo "Adding: $ENDPOINT_NAME -> $FULL_URL"

            RESPONSE=$(curl -s -o /dev/null -w "%{http_code}" -X POST \
              "https://api.postman.com/collections/${POSTMAN_COLLECTION_ID}/requests" \
              -H "x-api-key: $POSTMAN_API_KEY" \
              -H "Content-Type: application/json" \
              -d "{
                \"name\": \"${ENDPOINT_NAME}\",
                \"method\": \"${METHOD}\",
                \"url\": \"${FULL_URL}\",
                \"description\": \"Auto-added by GitHub Actions from ${{ env.ENV_NAME }} branch on ${{ github.sha }}\"
              }")

            if [ "$RESPONSE" == "200" ] || [ "$RESPONSE" == "201" ]; then
              echo "Successfully added: $ENDPOINT_NAME"
            else
              echo "Failed to add: $ENDPOINT_NAME (HTTP $RESPONSE)"
              exit 1
            fi
          done
```

After creating the file, record in `/ai/deployment.md`:

* That this workflow exists, when it runs, and what it does.
* The four required secrets (names only).
* Any detection limitations you noted (route style, monorepo paths, decorator/file-based frameworks).

---

# Phase 7: Final Report

After completing documentation, provide:

## Project Understanding

* Summary
* Technology stack (as detected)
* Architecture overview

## Documentation Created

List:

* Files created — including `.github/workflows/sync-postman-collection.yml` and the four required GitHub secrets (by name)
* Files updated

## Architecture Findings

Summarize (include only the layers that exist):

* Backend / server-side architecture
* Client / frontend architecture
* Database and data-store design
* Infrastructure and deployment approach

## Risks Found

List:

* Technical debt
* Security concerns
* Missing documentation
* Potential improvements

## Recommendations

Suggest concrete next steps.

---

# On-Demand Task: Feature Activity Diagrams

**Do not perform this task during initialization.** Do not create any activity diagram, and do not create the `/ai/activity-diagrams/` directory, unless I explicitly ask for it.

This section defines *how* to build an activity diagram when — and only when — I request one. Requests look like:

* "Create an activity diagram for `<feature>`."
* "Update the activity diagram for `<feature>`."
* "Create activity diagrams for `<feature-a>`, `<feature-b>`."

Never generate diagrams for features I did not name, and never generate them proactively as part of another task, a Tune step, or a documentation refresh. If you believe a diagram is missing or out of date, **say so and stop** — let me decide.

## When I do ask

Create `/ai/activity-diagrams/` if it does not exist yet, then create **one subdirectory per requested feature**, named after the feature in `kebab-case`, and inside it **one activity diagram as a single HTML file**:

```
/ai/activity-diagrams/<feature>/activity-diagram-<feature>-0001.html
```

Example:

```
/ai/activity-diagrams/
├── user-authentication/
│   └── activity-diagram-user-authentication-0001.html
├── checkout/
│   ├── activity-diagram-checkout-0001.html
│   └── activity-diagram-checkout-0002.html
└── invoice-generation/
    └── activity-diagram-invoice-generation-0001.html
```

### Scope

Diagram **only the features I named** — never the full feature list. Resolve the name against `/ai/features/feature-overview.md` and reuse it kebab-cased in the directory name and the file name so the two always line up with the overview. If the name I gave is ambiguous or absent from the overview, ask which feature I mean before creating anything.

### Versioning rules

* The version suffix is a **zero-padded 4-digit sequence**, starting at `0001`.
* **Never edit, rename, or delete an existing diagram file.** When a feature's flow changes, add the next number (`-0002.html`, `-0003.html`, …).
* The **highest-numbered file in a subdirectory is the current diagram**; lower numbers are history.
* Every diagram must state, near the top of the rendered page: the feature name, the version number, the date, and a one-line summary of what changed versus the previous version (`Initial version.` for `0001`).

### Diagram content

Each diagram must describe the feature's **actual** runtime flow as found in the code — not an idealized flow. Show:

* Start and end states.
* The actor or trigger (user action, cron, webhook, queue message, external callback).
* Ordered activities across every layer the flow touches (client → API → business logic → data store → external service), with the layer identified.
* Decision points and branches, including validation and authorization checks.
* Error, failure, and rollback paths.
* Asynchronous hand-offs (jobs, queues, events) marked as async rather than drawn as inline steps.
* The concrete code entry points involved, as `path/to/file.ext` references next to the relevant activities, so the diagram stays traceable to the source.

If a flow cannot be determined from the code, mark that segment `UNVERIFIED` in the diagram rather than guessing.

### File requirements

* **Self-contained and offline.** One HTML file per diagram, openable directly in a browser with no build step, no local server, and no network access. Inline all CSS and any JavaScript; do not link external stylesheets, scripts, fonts, or images.
* Render the diagram as **inline SVG**, or as plain HTML/CSS boxes and connectors. Do not depend on a CDN-hosted diagramming library.
* Include a `<title>` of the form `Activity Diagram — <Feature> — v0001`.
* Legible in both light and dark browser themes; wide diagrams scroll inside their own container rather than forcing the page to scroll sideways.
* Keep the markup readable and hand-editable — a human should be able to diff two versions.

### Index

After creating a diagram, add or update its row in an activity-diagram table in `/ai/features/feature-overview.md` — feature, current version, relative link. Only list features that actually have a diagram.

---

# On-Demand Task: Feature Test Cases

**Do not perform this task during initialization.** Do not create any test case document, and do not create the `/ai/test-cases/` directory, unless I explicitly ask for it.

This section defines *how* to build a test case document when — and only when — I request one. Requests look like:

* "Create test cases for `<feature>`."
* "Update the test cases for `<feature>`."
* "Create test cases for `<feature-a>`, `<feature-b>`."

Never generate test cases for features I did not name, and never generate them proactively as part of another task, a Tune step, a bug fix, or a documentation refresh. If you believe test cases are missing or out of date, **say so and stop** — let me decide.

Writing a test case document does **not** mean writing test code. Do not create, modify, or run test files, test suites, or any other source unless I ask separately.

## When I do ask

Create `/ai/test-cases/` if it does not exist yet, then create **one subdirectory per requested feature**, named after the feature in `kebab-case`, and inside it **one test case document as a single HTML file**:

```
/ai/test-cases/<feature>/test-cases-<feature>-0001.html
```

Example:

```
/ai/test-cases/
├── user-authentication/
│   └── test-cases-user-authentication-0001.html
├── checkout/
│   ├── test-cases-checkout-0001.html
│   └── test-cases-checkout-0002.html
└── invoice-generation/
    └── test-cases-invoice-generation-0001.html
```

### Scope

Write test cases for **only the features I named** — never the full feature list. Resolve the name against `/ai/features/feature-overview.md` and reuse it kebab-cased in the directory name and the file name so the two always line up with the overview. Use the **same feature slug** as that feature's activity diagram, if one exists, so `activity-diagrams/<feature>/` and `test-cases/<feature>/` always match. If the name I gave is ambiguous or absent from the overview, ask which feature I mean before creating anything.

### Versioning rules

* The version suffix is a **zero-padded 4-digit sequence**, starting at `0001`.
* **Never edit, rename, or delete an existing test case file.** When the feature's behaviour changes, add the next number (`-0002.html`, `-0003.html`, …).
* The **highest-numbered file in a subdirectory is the current document**; lower numbers are history.
* Every document must state, near the top of the rendered page: the feature name, the version number, the date, the total case count, and a one-line summary of what changed versus the previous version (`Initial version.` for `0001`).

### Test case content

Derive every case from the feature's **actual** implemented behaviour as found in the code — not from assumed or intended behaviour. Render the cases as a table, one row per case, with these columns:

* **ID** — `TC-<FEATURE>-001`, incrementing within the document (e.g. `TC-CHECKOUT-001`). IDs are stable: once assigned, an ID always refers to the same case across versions.
* **Title** — short description of what is being verified.
* **Type** — `Happy path`, `Validation`, `Authorization`, `Edge case`, `Negative`, `Error handling`, or `Regression`.
* **Priority** — `High`, `Medium`, `Low`.
* **Preconditions** — required state, seeded data, roles, feature flags, environment.
* **Steps** — numbered, concrete, and reproducible; include the actual inputs used.
* **Expected result** — the observable outcome, including status codes, error messages, persisted state changes, and side effects (emails, jobs queued, webhooks fired).
* **Code reference** — `path/to/file.ext` for the handler, validation rule, or business logic the case exercises.
* **Automated?** — `Yes — path/to/test.ext::test_name`, or `No`.

Cover, for each feature:

* The primary happy path, and each meaningful variant of it.
* Every validation rule and its failure message.
* Every authorization boundary — each role or permission that should be allowed and denied.
* Boundary values and edge cases (empty, minimum, maximum, over-limit, duplicate, concurrent).
* Error and failure handling, including external service failure and rollback behaviour.
* Asynchronous outcomes (queued jobs, events, webhooks) as their own cases.

Close the document with a **Coverage gaps** section: cases that exist here but have no automated test, behaviour that could not be determined from the code (mark `UNVERIFIED` rather than guessing), and anything untestable without additional fixtures or access.

### File requirements

* **Self-contained and offline.** One HTML file per document, openable directly in a browser with no build step, no local server, and no network access. Inline all CSS and any JavaScript; do not link external stylesheets, scripts, fonts, or images.
* Include a `<title>` of the form `Test Cases — <Feature> — v0001`.
* Legible in both light and dark browser themes; the case table scrolls inside its own container rather than forcing the page to scroll sideways.
* Keep the markup readable and hand-editable — a human should be able to diff two versions.

### Index

After creating a document, add or update its row in a test-case table in `/ai/features/feature-overview.md` — feature, current version, case count, relative link. Only list features that actually have test cases.

---

**Important:**

Do not modify application source code during this initialization process. The only files you create or edit are documentation (`/ai/**`, `AGENTS.md`, `README.md`) **and** the single CI workflow file `.github/workflows/sync-postman-collection.yml` defined in Phase 6.

Never write real secret values into any file; reference them only as `${{ secrets.* }}` and instruct a human to configure them in the repository's secret settings.

The objective is to create a reliable AI understanding of the project before development begins.