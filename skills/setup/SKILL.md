---
name: setup
description: "Use this when the user is getting started with their Swiss job hunt or wants to (re)configure it — capture or update their CV, build their ranked job preferences, check which connectors (Gmail, Claude for Chrome, board connectors) are available, and optionally set up a daily morning search. Triggers on 'set up my job search', 'onboard me', 'let's get started', 'update my preferences', or 'configure the job hunter'. Re-runnable onboarding that writes everything to the local data directory (~/.swissjobs/)."
---

# Setup — onboard the Swiss job hunt

One-time (re-runnable) onboarding. Collects the user's CV, builds their ranked
preferences, confirms which connectors are available, and offers a morning
search schedule. Everything is written to the local, git-ignored data directory.

## Where data lives

All personal data goes in **`~/.swissjobs/`** — never into the plugin or repo.
See `${CLAUDE_PLUGIN_ROOT}/shared/references/data-model.md` and
`${CLAUDE_PLUGIN_ROOT}/shared/references/guardrails.md`. Create the directory if
it does not exist.

## Inputs (ask the user)

- Their CV (paste text, share a file, or give a path).
- Answers to the preference questions in step 3.

## Steps

1. **Data dir** — ensure `~/.swissjobs/` exists, plus `cv-versions/` and
   `applications/` subfolders.
2. **CV** — ask the user to paste their CV, share a file, or give a path. If it
   is a PDF/DOCX, extract the text. Save it as `~/.swissjobs/cv.md`. Use
   `${CLAUDE_PLUGIN_ROOT}/shared/templates/cv.sample.md` only as a shape guide —
   never copy sample content into real data.
3. **Preferences** — walk through the schema in
   `${CLAUDE_PLUGIN_ROOT}/shared/templates/preferences.sample.md` and capture:
   - **Three ranked job types** (priority 1–3), each with its own titles + keywords.
   - **Location & work mode**: canton(s)/cities, acceptable commute, remote/hybrid/onsite.
   - **Languages**, ranked (default **EN → FR → DE**) — used for sourcing and output.
   - **Salary**: target and minimum, in CHF.
   - **Seniority**, **must-haves**, **dealbreakers**.
   - **Optional**: target/avoid companies, work-permit constraints.
   Save as `~/.swissjobs/preferences.md`. Both `jobhunt:search` and
   `jobhunt:evaluate` read this ranking, so keep the headings intact.
4. **Connectors** — check availability and record it in `~/.swissjobs/connectors.md`:
   - **Gmail** → for `jobhunt:draft-email`.
   - **Claude for Chrome** → for `jobhunt:apply` and for browsing boards with no
     API. Must be installed with Chrome running.
   - **Any board connector** (e.g. an Indeed connector) → preferred over browsing
     where present.
   Note any missing connector as a prerequisite (point to the README).
5. **Morning schedule (offer)** — a skill cannot create a Cowork schedule by
   itself; this is a **Cowork scheduled-task action**. Offer to help and hand the
   user the exact step: create a scheduled task that runs `jobhunt:search` every
   weekday morning, and give them the prompt text to paste.
   *(Flagged: this is a Cowork-UI step, not auto-creatable from a skill.)*
6. **Confirm** — summarize what was written and suggest next steps (run
   `jobhunt:search`, or `jobhunt:evaluate` on an ad).

## Output

A short summary: files written under `~/.swissjobs/`, connector status, whether a
morning schedule was set up, and recommended next actions.
