# Guardrails

Shared rules every skill must follow. `CLAUDE.md` and individual `SKILL.md`
files point here instead of restating them.

## 1. No fabrication (CV & emails)

Applies to `jobhunt:tailor-cv` and `jobhunt:draft-email`.

- **Only truthful reframing** of what is already in the user's real CV
  (`~/.swissjobs/cv.md`).
- **Never invent** experience, skills, employers, job titles, dates, education,
  certifications, or metrics.
- Re-ordering, re-emphasizing, and re-wording real facts to match a posting is
  fine. Inventing facts to match a posting is not.
- If a posting **requires a qualification the user genuinely lacks**, say so
  plainly — flag the gap rather than papering over it.

## 2. Human-in-the-loop applications

Applies to `jobhunt:apply`.

- Auto-apply is **opt-in and confirmed per application**.
- Always **fill the form, then show the completed application** to the user.
- **Submit only after explicit confirmation** ("yes, submit"). A vague or
  ambiguous reply is not consent.
- **Never batch-submit** silently across multiple postings.
- Respect each site's Terms of Use (see `boards.md`).

## 3. Email = draft, never send

Applies to `jobhunt:draft-email`.

- Always create a **Gmail draft**. **Never auto-send.**
- The user reviews and sends manually.

## 4. Privacy / public repo

- This repository is **public**. **Never commit** personal data or secrets:
  no real CV, no real preferences, no application history, no API keys or tokens.
- All personal data lives in the **git-ignored data dir** (default
  `~/.swissjobs/`), outside the repo. See `data-model.md`.
- Only **fake / sample** data ships in `shared/templates/`.

## 5. Automation conservatism

- Browse boards in the user's **own authenticated session** via Claude for
  Chrome, at human pace. No scraping, no bulk extraction, no circumventing
  access controls. LinkedIn especially: stay conservative and in-browser
  (see `boards.md`).
