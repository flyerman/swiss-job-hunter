# Job boards & how to reach them

Single source of truth for which boards the plugin covers and **how** it is
allowed to reach them. `jobhunt:search`, `jobhunt:evaluate`, and `jobhunt:apply`
read this file instead of hardcoding per-board logic.

It captures each board's **coverage, language, access strategy, and compliance
posture**. It deliberately avoids exact URL query-string syntax (which changes
often) — derive current filter parameters in-browser at run time. Canonical entry
points are given as starting URLs.

> **Last verified:** 2026-06-06. Re-check URLs, filters, and each site's terms periodically.

## Access reality (read this first)

Most Swiss boards have **no usable public jobseeker API**. Searching therefore
happens in this order of preference:

1. **A first-party connector, if one is present in your Cowork.** If you have an
   official connector / MCP for a board (for example an Indeed connector), prefer
   it — an authorized API is the cleanest, most Terms-compliant path of all. Skills
   should check for a relevant connector before falling back.
2. **Web search + Claude for Chrome, in YOUR OWN authenticated session.** Where no
   connector exists, the plugin uses ordinary web search to discover postings and
   Claude for Chrome to open and read them **as you**, in your logged-in browser.
   This is browsing on your behalf — not scraping, not automated bulk extraction.

Rules that always apply:

- **Respect each site's Terms of Use and robots directives.** Stay within what a
  human browsing manually would reasonably do.
- **No bulk scraping, no credential sharing, no circumventing access controls**
  (including anti-bot / Cloudflare challenges).
- **Human-paced, human-authenticated.** The browser session is yours; Claude acts
  within it and you stay in control.
- **LinkedIn and Indeed are the strict ones** — read their per-board notes before
  doing anything there.

## Cross-cutting Swiss notes

These apply across all boards and feed `search` and `evaluate`:

- **Language follows region.** German-speaking cantons (Zürich, Bern, Basel, …) →
  ads mostly in German; Suisse romande (Geneva, Vaud, Neuchâtel, Fribourg, Valais,
  Jura) → French; Ticino → Italian. International and tech firms often post in
  English. So **run keyword variants in the region's language**
  (`Softwareentwickler` / `développeur logiciel` / `software engineer`), not only
  English — otherwise you miss most local ads. (The user's ranked languages in
  `preferences.md` still control how results are **prioritized and written back**;
  region language controls how you **query**.)
- **Employment percentage (Pensum).** A standard Swiss filter, e.g. 80–100%. Honor
  the user's acceptable Pensum range from `preferences.md`.
- **Salary.** In **CHF**, usually annual, often quoted with a **13th-month** salary.
  Many Swiss ads omit salary entirely — **a missing salary is not a red flag.**
- **Work permit.** Some ads state EU/EFTA vs. third-country requirements. Surface any
  explicit permit requirement in `evaluate`.
- **Deduplicate across boards.** The same role is frequently cross-posted on jobs.ch,
  jobup.ch, LinkedIn, Indeed, and the employer's own site. Dedupe by **employer +
  title + location** (not just URL/id) and prefer the source with the most **direct**
  application path (the employer's own site / ATS over an "easy-apply" funnel).

## Boards at a glance

| Board | Region / language | Primary access | Starting point |
|-------|-------------------|----------------|----------------|
| **jobs.ch** | National; mostly German | Connector if any, else web search + Chrome | `jobs.ch/en/vacancies/` |
| **jobup.ch** | Suisse romande; mostly French | Web search + Chrome | `jobup.ch/en/jobs/` |
| **work.swiss / job-room.ch** | National public service; DE/FR/IT | Web search + Chrome | `job-room.ch` |
| **hiring.cafe** | Global, filter to CH; ATS early-signal | Web search + Chrome (site UI) | `hiring.cafe` |
| **linkedin.com** | Global incl. CH; mixed | **Manual assist only** (see note) | `linkedin.com/jobs/` |
| **indeed.com** | CH + global; mixed | Connector if any, else manual assist | `ch.indeed.com/jobs` |

## Per-board notes

### jobs.ch — largest national board
The biggest Swiss board (JobCloud); all industries and levels. UI in DE/EN/FR,
listings mostly German. Has location / industry / Pensum filters, a salary
calculator, and company reviews. **No public jobseeker API** — browse in-session,
human-paced. Shares a backend with jobup.ch, so a role may appear on both — dedupe.

### jobup.ch — Suisse romande
JobCloud's sister site and the top board for French-speaking Switzerland (Geneva,
Vaud, Neuchâtel, Valais, Fribourg, Jura); same platform features as jobs.ch. Use it
for the Romandie; use jobs.ch for German-speaking regions. Same compliance posture.

### work.swiss / job-room.ch — official public employment service
The federal (SECO) employment platform: the portal is **work.swiss**, the job
platform is **job-room.ch** (DE/FR/IT). Strong for public-sector and
registration-required roles.

**Key quirk — Stellenmeldepflicht (job-registration requirement / publication ban).**
Vacancies in occupations whose national unemployment rate is at or above the legal
threshold (≈5%) must first be registered with the regional employment centre (RAV)
and shown on Job-Room **visible only to RAV-registered jobseekers for the first
5 working days**, before they may be advertised anywhere else. The ban can't be
shortened or circumvented. The list of affected occupations is updated periodically
(and has broadened over time).
- **Implication:** for listed occupations, Job-Room can carry roles up to a week
  before jobs.ch / LinkedIn — **but that early window is only accessible if the user
  is registered with an RAV** (i.e. registered as a jobseeker). If they are, it's a
  genuine early-access edge; if not, treat Job-Room as a normal public board.

Compliance: public-sector site; browse in-session, human-paced.

### hiring.cafe — ATS aggregator (early signal)
Aggregates listings **directly from company career pages and many ATS platforms**
(Greenhouse, Lever, Workable, Workday, BambooHR, …); UI in English, listings in the
company's own language. Filters for salary, language, location, seniority, schedule,
and workplace type, and **applications route to the employer's own site / ATS** rather
than an "easy-apply" funnel.
- **Why it matters:** because it reads ATS directly, it often surfaces roles
  **before** they reach mainstream boards — good for applying early. It pairs
  naturally with `jobhunt:apply`, whose ATS targets (Greenhouse / Lever / Workday)
  are exactly what hiring.cafe links out to.
- **Compliance:** Cloudflare-protected with an internal search API. Use the site's
  own search UI in-browser; **don't bypass Cloudflare or hit the internal API directly.**

### linkedin.com — network + jobs (most restricted)
Large jobs inventory plus networking value; mixed languages, English common for
international roles. **Strictest posture — read before doing anything.** LinkedIn's
User Agreement and "Prohibited software" policy explicitly forbid crawlers, bots,
scripts, **and browser extensions** that scrape or automate activity, and forbid
bypassing rate / access limits. **Claude for Chrome is a browser extension**, so:
- **Do not automate LinkedIn** via Claude for Chrome — no scroll-to-scrape, no
  automated connection requests or messages, no automated Easy-Apply at scale.
- Use it strictly as **"assist me while I browse"**: the user navigates to and views
  a posting; the skill helps analyze, tailor, or draft. Keep it manual, human-paced.
- For automated discovery, **prefer web search + hiring.cafe over LinkedIn.**
- "Easy Apply" yields high-volume, low-signal applications — prefer the employer's
  own site when offered.

### indeed.com / ch.indeed.com — global aggregator
Aggregates from company sites, boards, and agencies; high volume but more duplicates
and lower signal. **Prefer an official Indeed connector if one is present** (that's
authorized API access). Otherwise: Indeed's ToS prohibits robots / spiders / scrapers
and automated access without written permission, and the site is Cloudflare-protected
with "verify you are human" challenges — so **browse in-session at human pace, with no
automated extraction and no challenge-bypassing.** Heavy duplication with jobs.ch and
company sites, plus sponsored-listing noise — dedupe and down-rank.

## Language prioritization vs. query language

Two different things — don't conflate them:

- **Prioritize & write back** in the user's ranked languages from
  `~/.swissjobs/preferences.md` (default **EN → FR → DE**): English-language postings
  first, then French, then German.
- **Query** in the **region's** language to actually surface ads (German for Zürich,
  French for Geneva, …), per the cross-cutting note above.
- `evaluate` still **understands** ads in DE / FR / IT / EN on input, regardless of
  what is being targeted.

## How `search` should use this list

1. Load the ranked job types, location, **Pensum**, and ranked languages from
   `~/.swissjobs/preferences.md`.
2. **Pick boards by region / language:** jobs.ch (German-speaking regions), jobup.ch
   (Romandie), job-room.ch (always — especially registration-list / public-sector
   roles), hiring.cafe (always — early ATS signal); LinkedIn and Indeed as
   **manual-assist** sources (or via an official connector).
3. For each board, follow the **access reality**: connector first, else web search +
   Chrome in the user's session. Respect each site's terms; never scrape.
4. Build queries from each job type's titles/keywords **in the region's language**,
   filtered by location and Pensum.
5. **Dedupe** new hits against `~/.swissjobs/seen-postings.jsonl` (by `id`/`url`)
   **and** across boards (by employer + title + location); keep the most direct apply
   path.
6. For each NEW posting, assign the best job-type priority and a rough fit score
   (0–100). *(TODO(you): tune scoring weights and recency to taste.)*
7. Group by priority (1 first); within each group, rank by score.
8. Append new postings to `seen-postings.jsonl` with status `new` (schema in
   `${CLAUDE_PLUGIN_ROOT}/shared/references/data-model.md`).
9. Hand promising postings to `jobhunt:evaluate`. Never auto-apply here — that's
   `jobhunt:apply`, only with explicit per-application confirmation.
