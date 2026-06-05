# Swiss Job Hunter

A **Claude Cowork plugin** that runs your job search on the **Swiss market** —
entirely as model-invoked skills you trigger in plain English. No backend, no
server: just skills, shared references, and templates.

> **Namespace:** the plugin installs under the `jobhunt` namespace, so its skills
> are invoked as `jobhunt:setup`, `jobhunt:search`, etc. (The repository is named
> `swiss-job-hunter`; the shorter `jobhunt` is the skill namespace.)

## Skills

| Skill | What it does |
|-------|--------------|
| `jobhunt:setup` | One-time onboarding: capture your CV, build ranked preferences, check connectors, offer a morning search schedule. |
| `jobhunt:evaluate` | Score a single job ad (DE/FR/IT/EN) against your CV + preferences; gives gaps, positioning, and an apply/don't verdict. |
| `jobhunt:search` | Sweep all configured boards for new postings, grouped and ranked by your three priorities. Schedulable each morning. |
| `jobhunt:tailor-cv` | Tailor your CV to a posting — reorder, mirror ATS keywords, match the ad's language. Truthful reframing only. |
| `jobhunt:draft-email` | Draft an application/outreach email as a Gmail draft (never sends), in the ad's language. |
| `jobhunt:apply` | Fill a web application via Claude for Chrome and submit only after you explicitly confirm. |
| `jobhunt:interview-prep` | Generate likely interview questions grouped by theme, each with a STAR angle from your real experience. |

## Install

1. In Claude Cowork, go to **Plugins → Upload** (or add it via your marketplace)
   and select this plugin.
2. Run `/reload-plugins` if needed.
3. Start with **"set up my job search"** to trigger `jobhunt:setup`.

## Prerequisites

Per-user; **nothing about authentication lives in this repo.**

- **Gmail connector** — required by `jobhunt:draft-email` to create drafts.
- **Claude for Chrome** — required by `jobhunt:apply` and for browsing boards that
  have no API. **Must be installed with Chrome running.**
- **Optional board connectors** (e.g. an Indeed connector) — used in preference to
  browsing when available.

The plugin assumes no specific accounts. If a connector isn't available, the
relevant skill tells you and degrades gracefully.

## How it finds jobs

Most Swiss boards (jobs.ch, work.swiss, jobup.ch, hiring.cafe, LinkedIn, Indeed)
have **no usable public API**. Searching happens via web search and via Claude for
Chrome browsing **in your own authenticated session** — not scraping. LinkedIn is
kept conservative and within its terms. See
[`shared/references/boards.md`](shared/references/boards.md).

## Your data & privacy

This is a **public** repo. **No personal data is ever committed.** Your CV, real
preferences, application history, and the seen-postings log live only in a local,
git-ignored directory — default **`~/.swissjobs/`**. Only fake sample data ships
here, in [`shared/templates/`](shared/templates/). See
[`shared/references/data-model.md`](shared/references/data-model.md).

## Guardrails

- **No fabrication** in `tailor-cv` / `draft-email` — only truthful reframing of
  what's in your CV.
- **Human-in-the-loop** — `apply` always shows the filled form and submits only on
  your explicit yes; never batch-submits. `draft-email` only ever creates drafts.

See [`shared/references/guardrails.md`](shared/references/guardrails.md).

## Repository layout

```
.claude-plugin/plugin.json   — plugin manifest (name: jobhunt)
skills/<name>/SKILL.md        — one folder per skill
shared/references/            — boards, guardrails, data-model (skills point here)
shared/templates/             — fake sample CV / preferences / seen-log
CLAUDE.md                     — project instructions for Claude
```

## License

[MIT](LICENSE).
