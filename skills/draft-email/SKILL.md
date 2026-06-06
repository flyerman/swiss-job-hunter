---
name: draft-email
description: "Use this when the user wants to write an application or outreach email for a role. Creates it as a Gmail draft (never sends), tailored to the role and, if known, the contact, in the ad's language, using only truthful highlights from their CV. Triggers on 'draft an email for this job', 'write a cover email', 'reach out to this recruiter', or 'email this hiring manager'."
---

# Draft email — application or outreach (Gmail draft)

Drafts an application or outreach email **as a Gmail draft — never sends it** —
tailored to the role and contact, in the ad's language.

## Inputs

- The role (posting text or URL).
- Optional contact (name and/or email).
- Type: application vs networking/outreach.

## Reads / writes

- **Reads:** `~/.swissjobs/cv.md`, `~/.swissjobs/preferences.md`,
  `~/.swissjobs/connectors.md`. Uses the **Gmail** connector.
- **Writes:** a Gmail draft; optionally a record under `~/.swissjobs/applications/`.

## Steps

1. Confirm the **Gmail connector** is available (check `connectors.md` and verify
   live). If not, explain the prerequisite (README) and offer to output the email
   text for the user to copy instead.
2. Load the CV and role details. Detect the ad's language and write the email in
   it (default the user's primary language, EN).
3. Draft a concise, tailored email:
   - subject line;
   - greeting (use the contact's name if known);
   - why this role / company;
   - 2–3 **truthful** highlights from the CV mapped to the ad's needs;
   - a clear call to action; professional sign-off.
4. **NO FABRICATION** — every claim must be true to the CV
   (`${CLAUDE_PLUGIN_ROOT}/shared/references/guardrails.md`).
5. Create it as a **Gmail DRAFT — never auto-send** (guardrails.md). Show the full
   draft (recipient if known, subject, body).
6. Optionally log to `applications/`.

## Output

The drafted email, plus confirmation it is saved as a Gmail draft and a reminder
that sending stays manual.

> If several Gmail actions are available, always choose **create draft** — never
> a send action.
