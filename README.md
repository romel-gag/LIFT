# LIFT

A stack-agnostic prompt framework for onboarding an AI coding assistant onto an **existing** software project.

This repo contains no application code. It holds a single initialization prompt — [LIFT Workflow - Postman.md](LIFT%20Workflow%20-%20Postman.md) — that you paste into an AI assistant (Claude Code, Cursor, Copilot, etc.) at the start of work on an unfamiliar codebase. The assistant analyzes the repo and produces a durable, AI-readable knowledge base *before* touching any source.

Based on Romel Tech, LIFT v1.0 (July 2026). Current prompt version: 2.1 (stack-agnostic + Postman CI sync).

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
| **Tune** | Test, refine, and update the `/ai` documentation. If a change makes an activity diagram or test case document stale, flag it and stop — don't regenerate unasked. |

---

## Two guiding principles

**Detect, then map.** Assume no technology. Read manifests, lockfiles, config, and directory conventions to detect the real stack, map each generic concept to that stack's idiom, then document only what actually exists. Skip cleanly what doesn't apply; note what's missing but expected.

**Generated, not re-derived.** Everything under `/ai` is either *generated* or *authored*, and the assistant must know which it's writing:

| | **Generated** — `ai/generated/**` | **Authored** — everything else |
| --- | --- | --- |
| Produced by | A command or tool | The assistant, from analysis |
| Hand-edited | Never | Yes, that's the point |
| On change | Re-run the command, overwrite | New version, or update in place |
| Contains | Facts the stack emits mechanically | Interpretation, intent, rationale |

Before describing anything a command can emit — route inventories, OpenAPI schemas, dependency trees, env keys — the assistant runs that command, commits the output under `ai/generated/`, records the exact command, and references the file rather than transcribing it. This is the rule that stops the assistant re-deriving, in prose, facts that `php artisan route:list --json` or `/openapi.json` already answers correctly and for free.

---

## Phases

1. **Project Discovery (Learn)** — purpose, users, business workflows, status, detected stack, repo shape, integrations. Then per-layer analysis of backend, client (web/mobile), data stores, infrastructure, and existing problems (`TODO`/`FIXME`/`HACK`, deprecated deps, error-handling gaps, security-sensitive patterns).
2. **Create AI project memory** — populate the `/ai` directory (tree below).
3. **Create AI development rules** — write `AGENTS.md`: LIFT rules, coding standards *derived from the existing codebase*, AI behavior rules, doc-update rules, issue tracking, ADR rules.
4. **Review existing documentation** — improve `README.md` if present (never strip project-specific instructions), create it if absent. Add the required-CI-secrets subsection.
5. **Initial ADR** — `ai/decisions/ADR-001-project-initialization.md`.
6. **CI automation** — create `.github/workflows/sync-postman-collection.yml`.
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
├── generated/                         # tool-produced, never hand-edited
│   ├── README.md
│   └── <e.g. endpoints.json, openapi.json>
│
├── activity-diagrams/                 # on request only
│   ├── viewer.html
│   └── <feature>/
│       └── activity-diagram-<feature>-0001.json
│
├── test-cases/                        # on request only
│   └── <feature>/
│       └── test-cases-<feature>-0001.md
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

Plus `AGENTS.md` at the repo root, `README.md` created or improved, and the CI workflow from Phase 6.

---

## On-demand tasks

Two artifacts are **never produced automatically** — not during initialization, not during a Tune step, not as a side effect of a bug fix. The assistant creates them only when you ask by name, for the features you name. If it thinks one is missing or stale, it must say so and stop.

### Activity diagrams — `ask: "Create an activity diagram for <feature>."`

Data, not markup. Each version is a JSON payload describing the flow; one committed `viewer.html`, written on the first request and reused unchanged, renders any of them.

```
ai/activity-diagrams/<feature>/activity-diagram-<feature>-0001.json
```

The payload carries `trigger`, `layers`, `nodes`, `edges`, and `gaps`. Node types are `start`, `end`, `activity`, `decision`, `async`, `external`, `error`; each node carries a `layer` and a `ref` (`path/to/file.ext:42`) so the diagram stays traceable to source. Behaviour that can't be confirmed from the code is marked `verified: false` and rendered `UNVERIFIED` — never guessed.

Why JSON rather than hand-authored HTML: it's ~5–10× cheaper to write, far cheaper to read back (no SVG coordinates or CSS in context), genuinely diffable between versions, consistent because one renderer draws everything, and greppable — "which features touch payments?" becomes a search instead of opening twelve files.

The viewer must load payloads from `file://`, where a relative `fetch()` is CORS-blocked — so a file picker and drag-and-drop are the primary path, `?d=` only when the directory happens to be served over HTTP.

### Test cases — `ask: "Create test cases for <feature>."`

```
ai/test-cases/<feature>/test-cases-<feature>-0001.md
```

Markdown, because test cases are a table: renders on GitHub and in every editor, diffs cleanly, needs no renderer. Columns are ID, Title, Type, Priority, Preconditions, Steps, Expected result, Code reference, Automated?. Each case stays on **one line** so a changed line means exactly one changed case, and IDs are stable forever — retire a case in a `## Retired` table rather than renumbering. Every document closes with **Coverage gaps**: cases with no automated test, `UNVERIFIED` behaviour, anything untestable without more fixtures.

Where a feature has an activity diagram, its payload is the coverage checklist — every `decision` node yields a case per branch, every `error` node at least one case.

Writing test cases does **not** mean writing test code. No test files are created, modified, or run unless you ask separately.

### Shared rules

Both use the same append-only versioning: zero-padded 4-digit suffix from `0001`, never edit/rename/delete an existing version, highest number is current, lower numbers are history. Both key on the same kebab-case feature slug, resolved against `feature-overview.md`, so `activity-diagrams/checkout/` and `test-cases/checkout/` always line up. Both index themselves back into `feature-overview.md`.

---

## Guardrails

Non-negotiable constraints the prompt imposes on the assistant:

- **No source changes during initialization.** Documentation only. The single exception is the Phase 6 CI workflow file.
- **No feature work, no refactors, no logic edits.**
- **No invented content.** If a category doesn't apply (no mobile app, no queues), say so briefly rather than filling the gap. Untraceable behaviour is marked `UNVERIFIED`.
- **Database safety.** Never delete, drop, truncate, reset, wipe, or otherwise destroy a database or its data. Stop and ask for explicit confirmation before any operation that could irreversibly alter significant existing data — dev environments included. Prefer targeted, reversible migrations.
- **Never write real secret values** into any file. Reference them only as `${{ secrets.* }}` and have a human configure them.
- **Communication style.** Lead with the result; be extremely concise. Code comments only where they explain non-obvious intent, constraints, or edge cases.

---

## Usage

1. Open the target project in your AI coding assistant.
2. Paste the contents of [LIFT Workflow - Postman.md](LIFT%20Workflow%20-%20Postman.md) as the first instruction.
3. Let it run discovery and produce `/ai`, `AGENTS.md`, `README.md`, and the CI workflow.
4. Review the Final Report, especially **Risks Found**.
5. Configure the four GitHub secrets below, or the sync workflow will fail on its first run.
6. Commit the generated docs. Keep them updated during every **Tune** stage thereafter.
7. Ask for activity diagrams and test cases per feature, as you need them.

---

## Postman sync: required CI secrets

Configure as GitHub repository (or environment) secrets — names only, never values in code:

| Secret | Purpose |
| --- | --- |
| `POSTMAN_API_KEY` | Postman API key |
| `POSTMAN_COLLECTION_ID` | Target collection ID |
| `PROD_API_BASE_URL` | Base URL used for the `main` branch |
| `STAGING_API_BASE_URL` | Base URL used for the `staging` branch |

The workflow triggers on pushes to `main` and `staging`, diffs the pushed commits for newly added route declarations, and POSTs each to the Postman API.

**Known limitation:** detection is diff/grep-based and tuned for Express/Fastify/Koa-style method calls (`app.get(...)`, `router.post(...)`). NestJS decorators (`@Get('/x')`) and Next.js file-based API routes need the glob and `grep -E` pattern adapted, or a documented manual fallback. Want separate collections per environment? Split into `PROD_POSTMAN_COLLECTION_ID` / `STAGING_POSTMAN_COLLECTION_ID` and set each in its branch block.

Where the project has a generated route or OpenAPI inventory under `ai/generated/`, that's a more reliable sync source than grepping diffs — the prompt has the assistant record this in `ai/deployment.md` as a recommended migration, without changing the workflow unasked.

---

## Versioning

The prompt is versioned in its own header (`Version: 2.1`). When editing it, bump the version and note what changed in the commit message.
