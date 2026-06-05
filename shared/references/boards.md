# Job boards & how to reach them

Single source of truth for which boards the plugin covers and **how** it is
allowed to reach them. `jobhunt:search` and `jobhunt:apply` read this file
instead of hardcoding per-board logic.

## Access reality (read this first)

Most Swiss boards have **no usable public API**. Searching therefore happens
in this order of preference:

1. **A first-party connector, if one is present in your Cowork.** If you have an
   official connector / MCP for a board (for example an Indeed connector),
   prefer it — it is the cleanest, Terms-compliant path. Skills should check for
   a relevant connector before falling back.
2. **Web search + Claude for Chrome, in YOUR OWN authenticated session.** Where
   no connector exists, the plugin uses ordinary web search to discover
   postings and Claude for Chrome to open and read them **as you**, in your
   logged-in browser. This is browsing on your behalf — not scraping, not
   automated bulk extraction.

Rules that always apply:

- **Respect each site's Terms of Use and robots directives.** Stay within what a
  human browsing manually would reasonably do.
- **No bulk scraping, no credential sharing, no circumventing access controls.**
- **Human-paced, human-authenticated.** The browser session is yours; Claude
  acts within it and you stay in control.

## Boards covered

| Board | Region / language focus | Primary access | Notes |
|-------|-------------------------|----------------|-------|
| **jobs.ch** | All of Switzerland (DE/FR/EN) | Web search + Chrome | Largest general CH board. |
| **work.swiss** | Official public employment portal (DE/FR/IT/EN) | Web search + Chrome | Government RAV/ORP portal; authoritative public listings. |
| **jobup.ch** | Suisse romande (FR) | Web search + Chrome | Best coverage for French-speaking Switzerland. |
| **hiring.cafe** | Aggregator (EN-leaning) | Web search + Chrome | Aggregates many sources; good for discovery. |
| **linkedin.com** | Global incl. CH (EN/DE/FR) | Chrome only, conservative | See LinkedIn caution below. |
| **indeed.com** | CH + global (EN/DE/FR) | Connector if available, else web search + Chrome | Prefer an Indeed connector when present. |

## LinkedIn caution

LinkedIn is **hostile to automated access** and its Terms prohibit scraping and
most automation.

- Interact **only inside Claude for Chrome**, in your own logged-in session, at
  human pace.
- **Never** bulk-collect profiles or postings, and never use unofficial APIs or
  scrapers.
- Treat LinkedIn as "open the page, read what's on screen, help me decide" —
  nothing more aggressive than that.
- When in doubt, prefer the other boards and use LinkedIn manually.

## Language prioritization (from preferences)

Source and present results following the user's ranked languages in
`~/.swissjobs/preferences.md` (default **EN → FR → DE**):

- Prefer English-language postings first, then French, then German.
- `evaluate` still **understands** ads in DE / FR / IT / EN on input, even if a
  language isn't being actively targeted.

## How `search` should use this list

1. Load the ranked job types and languages from `~/.swissjobs/preferences.md`.
2. For each board above: prefer a connector if present, else run web searches
   built from the job-type titles/keywords, then open promising hits in Chrome
   to confirm details.
3. Skip anything already in `~/.swissjobs/seen-postings.jsonl`.
4. Group and rank new matches by job-type priority (1 first), then by score.
5. Append newly surfaced postings to the seen log.
