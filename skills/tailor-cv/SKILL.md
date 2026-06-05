---
name: tailor-cv
description: "Use this when the user wants their CV tailored to a specific posting — reorder and emphasize the most relevant experience, mirror the ad's keywords for ATS, and match the ad's language. Truthful reframing only: never invents experience, skills, employers, dates, or credentials, and flags any genuinely missing required qualification. Triggers on 'tailor my CV', 'customize my resume for this', or 'adapt my CV to this job'."
---

# Tailor CV — adapt the CV to one posting

Produces a version of the user's CV tailored to a specific posting: reordered and
re-emphasized, mirroring the ad's ATS keywords, in the ad's language. **Truthful
reframing only.**

## Inputs

The target posting (text or URL).

## Reads / writes

- **Reads:** `~/.swissjobs/cv.md` (the master CV — required; else run
  `jobhunt:setup`), optionally `~/.swissjobs/preferences.md`.
- **Writes:** `~/.swissjobs/cv-versions/<company>-<role>-<YYYY-MM-DD>.md`.

## Steps

1. Load the real master CV. If missing, run `jobhunt:setup`.
2. Read the posting; detect its language; extract the key requirements and the
   ATS keywords/phrases it uses.
3. Produce a tailored CV that:
   - **reorders** sections/bullets so the most relevant real experience comes first;
   - **re-emphasizes and re-words** real bullets to mirror the ad's keywords;
   - is written in the **ad's language** (faithfully translate the user's real
     content — do not invent).
4. **NO FABRICATION** — see
   `${CLAUDE_PLUGIN_ROOT}/shared/references/guardrails.md`. Only truthful
   reframing of what is already in the CV. Never invent experience, skills,
   employers, titles, dates, education, or credentials. If the ad requires a
   qualification the user **genuinely lacks, flag it explicitly** rather than
   papering over it.
5. Save to `cv-versions/` and present the result.

## Output

- The **tailored CV** (in the ad's language).
- A short **change-log**: what was reordered/emphasized and which keywords were mirrored.
- **Gap flags**: any required qualifications the user is genuinely missing.
