# Search Queries for Job Scraper

<!-- Customized for Elizaveta Shper (ID/LXD + Product Manager bridge, remote/relocation, no fixed commute city) -->

## Installed portal CLIs (primary for `/scrape`)

`/scrape` discovers every portal skill under `.agents/skills/*/SKILL.md` and runs its CLI first: `linkedin-search` and `freehire-search` are installed. You do **not** need a matching `site:` line below for those CLIs to run — pass the query text below as their `-q`/`--query` value, with `--location "Remote"` (or a specific country when a query is scoped to one, e.g. "Cyprus", "United Arab Emirates").

The `site:` query templates in this file are the **WebSearch fallback** — for portals without a CLI (Wellfound, Indeed, Toptal, Deel, Hirify, simplify.jobs, remote.co, bayt.com, startup.jobs — none of these have an installed CLI), company career pages, or when a CLI call fails at runtime.

**Language scope — deliberately narrowed.** The candidate profile lists German (A2) and Spanish (A1) as working languages, but at those levels she could not actually perform a professional ID/LXD or PM role in either language. Searching `site:` queries translated into German/Spanish would surface roles that *require* a fluency she doesn't have, which the Language Gate would then just FLAG anyway. So: **run every category in English, plus Russian for Russia-based/CIS-remote-friendly postings** (her native language, and some remote employers hiring Russian speakers specifically exist). Skip German/Spanish query translation unless she asks to open that up.

## Search Sites

Primary (no CLI installed — WebSearch `site:` fallback):
- **wellfound.com** — startup jobs, mostly PM/design-tagged
- **indeed.com** — general job board
- **remoteok.com**, **remotive.com**, **arc.dev**, **jobgether.com**, **edtechjobs.io** — remote-first aggregators that actually carry ID/LXD and PM listings
- **toptal.com**, **deel.com** (Deel Talent Marketplace) — contractor/marketplace platforms
- **bayt.com** — UAE/Middle East, relevant given Dubai is a core relocation-priority location

Also covered by installed CLI (no `site:` line needed):
- **linkedin.com/jobs** — `linkedin-search` CLI, `--location "Remote"`
- tech-leaning roles (APM/PM track) — `freehire-search` CLI

Secondary (company career pages via Google):
- Direct searches for the contractor/relocation-friendly companies flagged in `CLAUDE.md`: Canva, GoStudent, LearnWorlds, Zapier, Toptal, Deel — and the fuller list in `documents/target_companies/relocation_companies.csv` when it's present.

## Query Categories

**Every query below omits a city** — she is targeting remote-first or relocation roles, not a commute radius. Pair `site:` queries with "remote" and/or a Core/High-priority country from `04-job-evaluation.md`'s country tiers (Cyprus, Dubai/UAE, Kazakhstan, Australia, Ireland, EU) rather than a city.

### Priority 1: Instructional Design / Learning Experience Design / L&D

Her strongest, most direct-experience role type.

```
site:wellfound.com "instructional designer" OR "learning experience designer" remote
site:indeed.com "instructional designer" remote
site:remotive.com OR site:remoteok.com "instructional design" OR "learning experience" OR "L&D"
site:edtechjobs.io "instructional designer" OR "learning experience designer"
site:linkedin.com/jobs "learning and development specialist" remote
"удалённо" "методист" OR "специалист по обучению" OR "instructional designer"
```

### Priority 2: Corporate/B2B Training & EdTech domain

Domain-expertise match from WANO (corporate/crisis-center training) and the EdTech sector generally.

```
site:wellfound.com OR site:indeed.com "curriculum designer" OR "learning designer" corporate training remote
site:edtechjobs.io "curriculum" OR "learning solutions" remote
site:bayt.com "training" OR "learning and development" OR "instructional design" remote
```

### Priority 3: Product Manager bridge tracks (not EdTech-exclusive)

Three parallel entry bridges per `CLAUDE.md`: L&D/learning-products inside a product company, marketing-to-product (SnapTap background), and a pure Junior/APM track in IT or fintech.

```
site:wellfound.com "associate product manager" OR "junior product manager" remote
site:linkedin.com/jobs "product manager" "learning" OR "L&D" OR "EdTech" remote
site:wellfound.com "product manager" marketing background remote -senior -principal -staff OR -head
site:arc.dev OR site:jobgether.com "associate product manager" OR "APM" remote fintech OR SaaS
"удалённо" "product manager" OR "менеджер продукта" junior OR associate
```

### Priority 4: Content / Marketing crossover (SnapTap background)

Leverages the 3-year B2B agency/marketing background as an alternate entry point — replaces the old "Broader Technical/Consulting" template category, which didn't match her profile at all.

```
site:wellfound.com OR site:indeed.com "content marketing manager" OR "content strategist" remote B2B
site:linkedin.com/jobs "marketing" "creator" OR "influencer" B2B remote
```

## Location Filter

Not a commute radius — see `04-job-evaluation.md`'s flexible country-tier logic (Core priority: Cyprus, Dubai/UAE, US with sponsorship, Kazakhstan; High: Australia, Ireland, EU; Conditional: Germany, Montenegro; lower-priority: South Africa, Turkey). A genuinely global-remote posting with no relocation involved always passes regardless of country. The Eligibility Gate (citizenship/PR/visa wording) is run by `/rank`'s full evaluation, not by `/scrape`'s quick fit pass — `/scrape` should surface a posting and flag the country, not silently drop it.

## Language Filter

Working languages: Russian (native), English (C1). German (A2) and Spanish (A1) are declared but not search-targeted (see Language scope note above) — a posting requiring either outright still runs through the Language Gate in `04-job-evaluation.md` if one slips through some other query. A posting requiring a language not declared at all is excluded; a posting requiring a higher level than declared is flagged, not excluded.

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown" — and treat a posting dated **more than ~60 days old with no deadline given** as likely stale; present it but flag it explicitly rather than listing it as a fresh match (this was a real failure mode: a 2024-dated Deel posting and a 2026-02 TaskUs posting both looked "new" in a plain scrape because they'd simply never been seen before).

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape product" -> Priority 3 queries + custom PM-bridge queries
- "/scrape content" -> Priority 4 queries + custom marketing-crossover queries
