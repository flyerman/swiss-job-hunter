# Data model & where personal data lives

All of the user's personal data lives **outside the repo**, in a git-ignored
directory referred to throughout the plugin as **`<data-dir>`**. Nothing here is
ever committed. `jobhunt:setup` creates this directory and its files from the
templates in `shared/templates/`.

## Resolving `<data-dir>`

Every skill resolves the data directory the same way, in this order. **Never
hardcode a path** — always resolve.

1. **`JOBHUNT_DATA_DIR`**, if that environment variable is set and non-empty.
2. **A `.jobhunt-data-dir` pointer file** at the root of any folder connected to
   the session. It contains one line: the data directory path. A relative path
   resolves against the folder holding the pointer file, so
   `data` in `~/work/job-search/.jobhunt-data-dir` means
   `~/work/job-search/data`. If more than one connected folder has a pointer
   file, ask the user which to use rather than guessing.
3. **`~/.swissjobs/`** otherwise. This is the default and is what `setup`
   creates when the user has expressed no preference.

**Why this is not just `~/.swissjobs/`.** In Cowork, and especially in scheduled
tasks, the home directory is per-session and does not survive. Anything written
to `~` is gone by the next run, which silently resets the dedup log and loses
tailored CVs and application records. A user who wants their data to persist
points `<data-dir>` at a connected folder, which does. `setup` should offer
this rather than leaving the user to discover the loss.

Resolve once at the start of a skill, and say which path you resolved to if it
is not the default — the user should never be guessing where their CV went.

## Directory layout

```
<data-dir>/
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

Everything above is personal. It stays in `<data-dir>` and is **never
committed** to this public repo. The pointer file `.jobhunt-data-dir` may be
committed to the *user's own* private repo; it must never be committed here.
See `guardrails.md`.
