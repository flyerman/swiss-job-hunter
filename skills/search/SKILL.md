---
name: search
description: "Use this when the user wants to find new Swiss job postings matching their preferences, or to run their daily morning search. Sweeps all configured boards (jobs.ch, work.swiss, jobup.ch, hiring.cafe, LinkedIn, Indeed), returns only postings not seen before, grouped and ranked by their three job-type priorities (priority 1 first). Built to run as a scheduled Cowork task. Triggers on 'search for jobs', 'any new roles', 'run my morning search', or 'find me jobs'."
---

# Search — find new Swiss postings

Finds new postings matching the user's preferences across all configured boards
and returns them grouped and ranked by the three job-type priorities. Designed to
run as a **daily Cowork scheduled task**.

## Inputs

None required. Optional: restrict to certain boards, a single job-type priority,
or "today only".

## Reads / writes

- **Reads:** `~/.swissjobs/preferences.md`, `~/.swissjobs/seen-postings.jsonl`,
  and `${CLAUDE_PLUGIN_ROOT}/shared/references/boards.md`. If preferences are
  missing, run `jobhunt:setup` first.
- **Writes:** appends new postings to `~/.swissjobs/seen-postings.jsonl`.

## Steps

1. Load preferences: the three ranked job types (titles + keywords), location &
   mode, and ranked languages (default **EN → FR → DE**).
2. Load `boards.md` (board list + access rules) and `seen-postings.jsonl` (the
   dedup log).
3. For each board, follow the **access reality** in `boards.md`: prefer a
   first-party connector if one is present; otherwise use web search + Claude for
   Chrome in the user's authenticated session. Keep **LinkedIn** conservative and
   in-browser. Never scrape; respect each site's terms.
4. Build queries from each job type's titles/keywords, filtered by location and
   language priority.
5. **Dedup** every hit against `seen-postings.jsonl` (by `id`/`url`). Skip
   anything already seen so the same posting never resurfaces.
6. For each NEW posting, assign the best job-type priority and a rough fit score
   (0–100). *(TODO(you): tune scoring weights and recency to taste.)*
7. Group by priority (1 first); within each group, rank by score.
8. Append new postings to `seen-postings.jsonl` with status `new` (schema in
   `${CLAUDE_PLUGIN_ROOT}/shared/references/data-model.md`).
9. Present the digest.

## Output format

A grouped digest:

- `## Priority 1 — <type>` → list of `{ title · company · location · language · board · link · score · one-line why }`
- `## Priority 2 — <type>` → …
- `## Priority 3 — <type>` → …
- **Summary line:** N new across boards since last run; suggest running
  `jobhunt:evaluate` on the top hits.

## Scheduling

This is the skill to schedule each morning. `jobhunt:setup` can help you create
the Cowork scheduled task.
