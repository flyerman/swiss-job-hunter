---
name: evaluate
description: "Use this when the user pastes or links a single job ad and wants to know whether it is worth applying to. Scores fit against their CV and ranked preferences, says which of their three job-type priorities it matches, lists requirement-by-requirement gaps, suggests how to position themselves, and gives a clear apply / do-not-apply verdict. Understands ads written in German, French, Italian, or English. Triggers on 'should I apply to this', 'evaluate this job', 'is this role a fit', or a pasted job posting."
---

# Evaluate — score one job ad

Scores a single job ad against the user's CV and ranked preferences and gives a
clear apply / don't verdict. For many ads at once, use `jobhunt:search` instead.

## Inputs

One job ad — pasted text, a URL, or a screenshot.

## Reads / writes

- **Reads:** `~/.swissjobs/cv.md`, `~/.swissjobs/preferences.md`. If either is
  missing, tell the user to run `jobhunt:setup` first.
- **Writes:** optionally append the posting to `~/.swissjobs/seen-postings.jsonl`.

## Steps

1. Load the CV and preferences (especially the three ranked job types).
2. Obtain the ad text. If given only a URL, read it via web fetch, or open it in
   Claude for Chrome (the user's own session) if it needs authentication. Respect
   site terms — see `${CLAUDE_PLUGIN_ROOT}/shared/references/boards.md`.
3. Detect the ad's language (DE/FR/IT/EN) and understand it natively. Write your
   output in the user's primary language (default EN) unless asked otherwise.
4. Map the ad to the best-matching ranked job type (1, 2, 3, or "none").
5. **Requirement-by-requirement**: list each key requirement and mark
   **Met / Partial / Gap**, citing concrete CV evidence. Judge only against what
   is truly in the CV — no fabrication
   (`${CLAUDE_PLUGIN_ROOT}/shared/references/guardrails.md`).
6. Score the fit **0–100** with brief reasoning.
   *(TODO(you): adjust the "worth applying" threshold to taste; default ~65+.)*
7. **Positioning**: 2–4 truthful angles for how the user should present
   themselves for this role.
8. **Verdict**: a clear "worth applying or not", in priority context
   (e.g. "Matches priority 2; strong fit — apply").
9. Offer next steps: `jobhunt:tailor-cv`, `jobhunt:draft-email`,
   `jobhunt:interview-prep`; offer to log the posting to the seen list.

## Swiss context

Account for Swiss norms when scoring and reporting (see
`${CLAUDE_PLUGIN_ROOT}/shared/references/boards.md`):

- **Salary:** many ads omit it — treat a missing salary as **neutral, not a gap**.
  Pay is CHF, usually annual, often with a 13th-month.
- **Work permit:** surface any explicit EU/EFTA vs. third-country requirement and
  whether it affects the user.
- **Pensum:** note the advertised employment percentage and whether it fits the
  user's preferred range.
- **Language:** the ad's language reflects the region — factor the required working
  language into fit.

## Output format

- **Header:** role · company · location · ad language · matched priority
- **Fit score** (0–100)
- **Requirements table:** requirement | Met / Partial / Gap | evidence
- **Positioning** bullets
- **Verdict** + recommended next step
