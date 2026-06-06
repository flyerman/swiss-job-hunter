# CLAUDE.md

Project instructions for Claude when working in this repository.

## What this repo is

`swiss-job-hunter` is a **Claude Cowork plugin** (skill namespace **`jobhunt`**)
that runs a job search on the **Swiss market**. The plugin is the only artifact:
everything here is **skills, shared references, templates, and docs**. There is no
backend and no server.

## The overriding rule: it must work in Cowork

**Every capability is a model-invoked skill**, triggered by the user describing
what they want in plain English. A skill's `description` is what makes Cowork
auto-trigger it, so descriptions are written as "when to use this," from the
user's point of view. If something can only work from a terminal, **flag it**
instead of relying on it (e.g. creating the morning schedule is a Cowork
scheduled-task action, not something a skill creates itself).

## Skills (`skills/<name>/SKILL.md`)

- `setup` — one-time onboarding: CV + ranked preferences + connector check + offer a morning schedule.
- `evaluate` — score one ad (DE/FR/IT/EN) vs CV + preferences; gaps, positioning, apply/don't verdict.
- `search` — sweep configured boards for new postings, grouped/ranked by the three priorities; schedulable.
- `tailor-cv` — tailor the CV to a posting; truthful reframing only.
- `draft-email` — Gmail draft (never send), in the ad's language.
- `apply` — fill + submit a web application via Claude for Chrome, human-confirmed per application.
- `interview-prep` — likely questions grouped by theme with STAR angles from real experience.

Keep each skill **single-purpose**. Skills point to `shared/references/` instead
of duplicating rules (DRY).

## Preferences & data model

- Personal data lives in a **local, git-ignored** directory — default
  **`~/.swissjobs/`** — never in the repo. See `shared/references/data-model.md`.
- `preferences.md` captures the **three ranked job types** (each with
  titles/keywords), location & work mode, ranked languages (default
  **EN → FR → DE**), salary in CHF, seniority, must-haves, dealbreakers, and
  optional companies / permit. Both `search` and `evaluate` consume the ranking.
- Sample (fake) data lives in `shared/templates/`.

## Guardrails (also in `shared/references/guardrails.md`)

- **No fabrication** in `tailor-cv` / `draft-email`: only truthful reframing of
  what's in the CV; never invent experience, skills, employers, dates, or
  credentials; flag genuinely missing qualifications.
- **Human-in-the-loop**: `apply` always shows the filled form and submits only on
  the user's explicit, per-application yes — never batch-submits. `draft-email`
  only ever creates drafts, never sends.
- **Privacy / public repo**: never commit personal data or secrets (no CV, real
  preferences, application history, API keys/tokens). Only fake samples ship.
- **Automation conservatism**: browse boards in the user's own authenticated
  session; no scraping; LinkedIn stays conservative and within its terms.

## Connectors

**Gmail** for `draft-email`; **Claude for Chrome** for `apply` and board browsing;
optional **board connectors** (e.g. Indeed) preferred over browsing where present.
Per-user auth — nothing about auth lives in this repo. Documented as prerequisites
in the README.

## Git conventions

- **Committed:** plugin manifest, skills, shared references/templates (fake data
  only), docs.
- **Never committed:** anything under `~/.swissjobs/`, `.env`, secrets/keys,
  `.claude/settings.local.json` (see `.gitignore`).
- **Commit messages:** short, imperative ("Add evaluate skill", "Tighten apply
  guardrails").
- **Versioning:** bump `version` in `.claude-plugin/plugin.json` whenever skills
  change.
- The plugin `name` is the skill namespace (`jobhunt`); the GitHub repo is
  `swiss-job-hunter`.
