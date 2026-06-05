---
name: interview-prep
description: "Use this when the user has an interview coming up and wants to practice. Generates likely interview questions from the job ad and their CV, grouped by theme, each with a suggested angle or STAR talking point drawn from their real experience. Triggers on 'help me prep for this interview', 'what might they ask', or 'interview questions for this role'."
---

# Interview prep — practice questions with STAR angles

Generates likely interview questions from the job ad and the user's CV, grouped by
theme, each with a STAR angle drawn from the user's **real** experience.

## Inputs

- The job ad (text or URL) + the user's CV.
- Optional: interview stage (recruiter screen / technical / hiring manager /
  final) and interviewer info.

## Reads / writes

- **Reads:** `~/.swissjobs/cv.md` (required — else `jobhunt:setup`), and the ad.
- **Writes:** optionally
  `~/.swissjobs/applications/<company>-<role>-interview-prep.md`.

## Steps

1. Load the CV and ad. Detect the ad's language; default output to the likely
   interview language (the ad's language or the user's primary).
2. Identify the role focus, key requirements, and the competencies likely to be
   tested.
3. Generate likely questions **grouped by theme**, e.g.:
   - Role-specific / technical
   - Behavioral / competency
   - Motivation & fit (why this company / role)
   - Swiss-context / logistics (languages, work permit, notice period, location)
   - Questions for the candidate to ask the interviewer
4. For each question, give a suggested angle or a **STAR** talking point
   (Situation–Task–Action–Result) drawn from the user's **real** CV experience.
   Truthful only — see
   `${CLAUDE_PLUGIN_ROOT}/shared/references/guardrails.md`. Where the user lacks a
   strong real example, **flag it** so they can prepare.
5. Optionally save the prep sheet.

## Output

A themed question list, each with an angle/STAR note, plus a few strong questions
for the candidate to ask. Optionally saved to `applications/`.
