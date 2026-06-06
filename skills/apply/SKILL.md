---
name: apply
description: "Use this when the user wants help submitting an application through a job site's web form using the Claude for Chrome extension. Fills the form from their CV and preferences, shows the completed application, and submits only after the user explicitly confirms — never auto-submits and never batch-submits. Triggers on 'apply to this', 'fill out this application', or 'submit my application'."
---

# Apply — fill & submit a web application (human-in-the-loop)

Helps submit an application through a site's web form using **Claude for Chrome**.
Fills the form, shows it, and **submits only after the user's explicit
confirmation**. Human-in-the-loop is mandatory.

## Inputs

- The application URL / page.
- Which CV to use (a tailored version from `cv-versions/` or the master CV).
- Answers to any extra questions the form asks.

## Reads / writes

- **Reads:** `~/.swissjobs/cv.md` or a `~/.swissjobs/cv-versions/*` file,
  `~/.swissjobs/preferences.md`, `~/.swissjobs/connectors.md`. Uses **Claude for
  Chrome**.
- **Writes:** a record under `~/.swissjobs/applications/`.

## Steps

1. Confirm **Claude for Chrome** is available and Chrome is running (prerequisite
   — README). If not, explain and stop.
2. Open the application page in the user's own authenticated browser session.
   Respect the site's Terms of Use
   (`${CLAUDE_PLUGIN_ROOT}/shared/references/boards.md`).
3. Fill the form from the CV and preferences. Ask the user for anything the form
   needs that is not on file (cover letter, salary expectation, availability /
   notice, work permit, screening answers).
4. **SHOW the completed application** back to the user — every field and any
   attached documents.
5. **WAIT for explicit confirmation** ("yes, submit"). An ambiguous reply is NOT
   consent. **Never batch-submit** across multiple postings — see
   `${CLAUDE_PLUGIN_ROOT}/shared/references/guardrails.md`.
6. On explicit confirmation, submit and capture any confirmation / reference
   number. If the user declines, leave it unsubmitted.
7. Log to `applications/` with status (`applied` / `draft` / `skipped`), date,
   and link.

## Output

- **Before submit:** a full review of the filled application.
- **After confirmed submit:** a confirmation summary (with reference if any).
- **If declined:** noted as draft/skipped.

> This skill never submits without the user's explicit, per-application "yes".
