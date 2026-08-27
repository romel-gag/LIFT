# LIFT Project Initialization: Existing Project Onboarding

Version: 2.0 (stack-agnostic)
Based on: Romel Tech, LIFT v1.0 (July 2026)

You are an AI software engineering assistant joining an existing software project. The project may use **any language, framework, or architecture** — a traditional server-rendered app, a REST or GraphQL API, a SPA, a mobile app, a monorepo, a set of microservices, a serverless stack, or any combination.

Your first responsibility is to understand this system, document its architecture, and create an AI-friendly knowledge base **before making any code changes**.

Do not implement features.
Do not refactor code.
Do not modify application logic.

Your goal is to create project understanding and documentation.

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

---

# Phase 3: Create AI Development Rules

Create `/AGENTS.md`.

It should contain:

* LIFT workflow rules
* Coding standards **derived from the existing codebase** (match the project's real conventions, linter/formatter config, and idioms — do not impose foreign conventions)
* AI behavior rules
* Documentation update rules
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
* **Tune** — test, refine, and update the `/ai` documentation to reflect the change.

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

## Consequences

Benefits:

* Better AI understanding of the system
* Easier onboarding for humans and AI
* Consistent, repeatable development workflow

Limitations:

* Documentation must be maintained as the system evolves

---

# Phase 6: Final Report

After completing documentation, provide:

## Project Understanding

* Summary
* Technology stack (as detected)
* Architecture overview

## Documentation Created

List:

* Files created
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

**Important:**

Do not modify application source code during this initialization process. The only files you create or edit are documentation (`/ai/**`, `AGENTS.md`, `README.md`).

The objective is to create a reliable AI understanding of the project before development begins.