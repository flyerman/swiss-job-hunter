# Data model & where personal data lives

All of the user's personal data lives **outside the repo**, in a git-ignored
directory — default **`~/.swissjobs/`**. Nothing here is ever committed.
`jobhunt:setup` creates this directory and its files from the templates in
`shared/templates/`.

## Directory layout

```
~/.swissjobs/
├── preferences.md        # ranked job types, location, pensum, languages, salary, must-haves, dealbreakers
├── cv.md                 # the user's real master CV
├── cv-versions/          # tailored CVs per posting (jobhunt:tailor-cv)
│   └── <company>-<role>-<date>.md
├── seen-postings.jsonl   # dedup log for jobhunt:search (one JSON object per line)
├── applications/         # per-application records (jobhunt:apply, jobhunt:draft-email)
│   └── <company>-<role>-<date>.md
└── connectors.md         # which connectors are confirmed available (Gmail, Chrome, Indeed, …)
```

## `preferences.md`

Human-readable Markdown (see `shared/templates/preferences.sample.md`). Key
fields, consumed by `search` and `evaluate`:

- **Target job types (ranked 1–3)** — each with titles + keywords. The ranking
  drives grouping in `search` and the "which priority does this match" line in
  `evaluate`.
- **Location & work mode** — cantons/cities, acceptable commute, remote/hybrid/onsite.
- **Employment level (Pensum)** — acceptable percentage range (e.g. 80–100%), a
  standard Swiss filter used by `search`.
- **Languages (ranked)** — default EN → FR → DE; used to prioritize and write back
  results. (The *region's* language is used to **query** boards — see `boards.md`.)
- **Compensation** — target + minimum, in CHF.
- **Seniority**, **must-haves**, **dealbreakers**.
- **Optional** — target/avoid companies, work-permit constraints.

## `seen-postings.jsonl`

One JSON object per line (see `shared/templates/seen-postings.sample.jsonl`).
Suggested fields:

| field | meaning |
|-------|---------|
| `id` | stable per-board id (e.g. `jobsch-abc123`) — used for dedup |
| `board` | board name from `boards.md` |
| `url` | canonical posting URL |
| `title`, `company`, `location` | posting basics |
| `language` | `en` / `fr` / `de` / `it` |
| `first_seen` | ISO date first surfaced |
| `job_type_priority` | 1–3, which target type it matched |
| `score` | 0–100 fit score |
| `status` | `new` / `evaluated` / `applied` / `skipped` |

`search` reads this file to suppress already-seen postings and appends new ones.
Because the same role is often cross-posted across boards, `search` also dedupes by a
normalized **employer + title + location** key — not just `id` / `url`.

## `connectors.md`

A short note, written by `setup`, recording which connectors the user confirmed
available (e.g. Gmail for `draft-email`, Claude for Chrome for `apply` and board
browsing, any board connector). Skills check here — and re-verify at run time —
before relying on a connector.

## Privacy

Everything above is personal. It stays in `~/.swissjobs/` and is **never
committed** to this public repo. See `guardrails.md`.
