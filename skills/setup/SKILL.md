---
name: setup
description: "Use this when the user is getting started with their Swiss job hunt or wants to (re)configure it — capture or update their CV, build their ranked job preferences, check which connectors (Gmail, Claude for Chrome, board connectors) are available, and optionally set up a daily morning search. Triggers on 'set up my job search', 'onboard me', 'let's get started', 'update my preferences', or 'configure the job hunter'. Re-runnable onboarding that writes everything to the local data directory."
---

# Setup — onboard the Swiss job hunt

One-time (re-runnable) onboarding. Collects the user's CV, builds their ranked
preferences, confirms which connectors are available, and offers a morning
search schedule. Everything is written to the local, git-ignored data directory.

## Where data lives

All personal data goes in **`<data-dir>`** — never into the plugin or repo.
`<data-dir>` is resolved, not assumed: see the resolution order in
`${CLAUDE_PLUGIN_ROOT}/shared/references/data-model.md`, and
`${CLAUDE_PLUGIN_ROOT}/shared/references/guardrails.md`. Create the directory if
it does not exist.

## Inputs (ask the user)

- Their CV (paste text, share a file, or give a path).
- Answers to the preference questions in step 3.

## Steps

1. **Data dir** — resolve `<data-dir>` using the order in `data-model.md`, then
   ensure it exists along with its `cv-versions/` and `applications/` subfolders.

   If it resolved to the `~/.swissjobs/` default, **say so and offer the
   alternative before writing anything**: in Cowork the home directory is
   per-session, so the dedup log, tailored CVs and application records are lost
   between sessions and between scheduled runs. Offer to point `<data-dir>` at a
   connected folder instead, by writing a `.jobhunt-data-dir` pointer file at
   that folder's root. Take the default only if the user declines or no folder
   is connected. Tell the user the path you settled on.
2. **CV** — ask the user to paste their CV, share a file, or give a path. If it
   is a PDF/DOCX, extract the text. Save it as `<data-dir>/cv.md`. Use
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
   Save as `<data-dir>/preferences.md`. Both `jobhunt:search` and
   `jobhunt:evaluate` read this ranking, so keep the headings intact.
4. **Connectors** — check availability and record it in `<data-dir>/connectors.md`:
   - **Gmail** → for `jobhunt:draft-email`.
   - **Claude for Chrome** → for `jobhunt:apply` and for browsing boards with no
     API. Must be installed with Chrome running.
   - **Any board connector** (e.g. an Indeed connector) → preferred over browsing
     where present.
   Note any missing connector as a prerequisite (point to the README).
5. **Morning schedule (offer)** — offer to create a scheduled task that runs
   `jobhunt:search` on a cadence the user picks. In Cowork this **can** be
   created directly with the scheduled-task tools (`create_trigger`, managed
   afterwards with `list_triggers` / `update_trigger`); it is not a manual UI
   step. Write the task's prompt so it stands alone — each firing is a fresh
   session with no memory of this one — and state the resolved `<data-dir>` in
   it explicitly. If the data dir lives in a connected folder, the task must be
   bound to that computer with the folder attached, or its runs cannot reach it.
   If the scheduled-task tools are unavailable, say so and hand the user the
   prompt text to create it themselves.
6. **Confirm** — summarize what was written and suggest next steps (run
   `jobhunt:search`, or `jobhunt:evaluate` on an ad).

## Output

A short summary: the resolved `<data-dir>` and the files written under it,
connector status, whether a morning schedule was set up, and recommended next
actions.
