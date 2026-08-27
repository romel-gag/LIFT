# LIFT

A stack-agnostic prompt framework for onboarding an AI coding assistant onto an **existing** software project.

This repo contains no application code. It holds the LIFT initialization prompts you paste into an AI assistant (Claude Code, Cursor, Copilot, etc.) at the start of work on an unfamiliar codebase. The assistant then analyzes the repo and produces a durable, AI-readable knowledge base *before* touching any source.

Based on Romel Tech, LIFT v1.0 (July 2026). Current prompt version: 2.1.

---

## The LIFT workflow

```
Learn  →  Intend  →  Forge  →  Tune
```

| Stage | Meaning |
| --- | --- |
| **Learn** | Understand the relevant part of the system and its existing conventions before writing anything. |
| **Intend** | State the plan and intended changes; confirm scope. |
| **Forge** | Implement, following the project's established patterns. |
| **Tune** | Test, refine, and update the `/ai` documentation to reflect the change. |

Governing principle throughout: **Detect, then map.** Assume no technology. Read manifests, lockfiles, config, and directory conventions to detect the real stack, map each generic concept to that stack's idiom, then document only what actually exists.

---

## Contents

| File | Use it when |
| --- | --- |
| [LIFT Workflow.md](LIFT%20Workflow.md) | Standard onboarding. 6 phases. Documentation-only — the assistant creates no functional files. |
| [LIFT Workflow - Postman.md](LIFT%20Workflow%20-%20Postman.md) | Same as above, plus one sanctioned functional deliverable: a GitHub Actions workflow that auto-syncs newly added API routes into a shared Postman collection. 7 phases. |

Pick one. They are alternatives, not a sequence.

---

## Phases

Both variants share Phases 1–5; the Postman variant adds Phase 6 and shifts the final report to Phase 7.

1. **Project Discovery (Learn)** — purpose, users, business workflows, status, detected stack, repo shape, integrations. Then per-layer analysis of backend, client (web/mobile), data stores, infrastructure, and existing problems (`TODO`/`FIXME`/`HACK`, deprecated deps, error-handling gaps, security-sensitive patterns).
2. **Create AI project memory** — populate the `/ai` directory (structure below).
3. **Create AI development rules** — write `AGENTS.md`: LIFT rules, coding standards *derived from the existing codebase*, AI behavior rules, doc-update rules, issue-tracking rules, ADR rules.
4. **Review existing documentation** — improve `README.md` if present (never strip project-specific instructions), create it if absent.
5. **Initial ADR** — `ai/decisions/ADR-001-project-initialization.md`.
6. *(Postman variant only)* **CI automation** — create `.github/workflows/sync-postman-collection.yml`.
7. **Final report** — project understanding, files created/updated, architecture findings, risks, recommendations.

---

## Output: the `/ai` knowledge base

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

Plus `AGENTS.md` at the repo root, and `README.md` created or improved.

---

## Guardrails

Non-negotiable constraints the prompts impose on the assistant:

- **No source changes during initialization.** Documentation only. The single exception is the Postman variant's one CI workflow file.
- **No feature work, no refactors, no logic edits.**
- **Never invent content.** If a category doesn't apply (no mobile app, no queues), say so briefly rather than filling the gap.
- **Database safety.** Never delete, drop, truncate, reset, wipe, or otherwise destroy a database or its data. Stop and ask for explicit confirmation before any operation that could irreversibly alter significant existing data — dev environments included. Prefer targeted, reversible migrations.
- **Never write real secret values** into any file. Reference them only as `${{ secrets.* }}` and have a human configure them.
- **Communication style.** Lead with the result; be extremely concise. Code comments only where they explain non-obvious intent, constraints, or edge cases.

---

## Usage

1. Open the target project in your AI coding assistant.
2. Paste the contents of the chosen prompt file as the first instruction.
3. Let the assistant run Phase 1 discovery and produce `/ai`, `AGENTS.md`, and `README.md`.
4. Review the Final Report, especially **Risks Found**.
5. Commit the generated docs. Keep them updated during every **Tune** stage thereafter.

---

## Postman sync: required CI secrets

Only relevant if you use the Postman variant. Configure these as GitHub repository (or environment) secrets — names only, never values in code:

| Secret | Purpose |
| --- | --- |
| `POSTMAN_API_KEY` | Postman API key |
| `POSTMAN_COLLECTION_ID` | Target collection ID |
| `PROD_API_BASE_URL` | Base URL used for the `main` branch |
| `STAGING_API_BASE_URL` | Base URL used for the `staging` branch |

The workflow triggers on pushes to `main` and `staging`, diffs the pushed commits for newly added route declarations, and POSTs each to the Postman API.

**Known limitation:** detection is diff/grep-based and tuned for Express/Fastify/Koa-style method calls (`app.get(...)`, `router.post(...)`). NestJS decorators (`@Get('/x')`) and Next.js file-based API routes need the glob and `grep -E` pattern adapted, or a documented manual fallback. Want separate collections per environment? Split into `PROD_POSTMAN_COLLECTION_ID` / `STAGING_POSTMAN_COLLECTION_ID` and set each in its branch block.

---

## Versioning

Prompts are versioned in their own header (`Version: 2.1`). When editing a prompt, bump the version and note what changed in the commit message.
