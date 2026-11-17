# AI for borrowing, search and returns at Toronto Public Library

Research date: 17 November 2026. Compiled from web search results; the TPL and vendor
pages themselves could not be opened from the research environment, so claims below
rest on search summaries of those pages and should be re-checked against the originals
before anything is built on them. Confidence is marked per item.

## 1. Where TPL is today

| Fact | Confidence | Source |
|---|---|---|
| TPL launched a rebuilt website on 19 Jan 2026, followed by a new mobile app. | High | [City Librarian's Report, Feb 2026](https://tpl.ca/news/city-librarians-report-for-february-2026/), [new website page](https://tpl.ca/new-website/) |
| The website, catalogue and app now run on BiblioCommons (the "Digital Experience Platform", part of the 2025–2029 Digital Strategy). The catalogue lives at `tpl.bibliocommons.com`. | High | [Board update, Feb 2026](https://tpl.ca/wp-content/uploads/sites/164/2026/02/21-reimagining-tpl.ca-the-digital-experience-platform-update-combined.pdf) |
| TPL has a Board-approved AI policy (Jan 2025) governing staff use of AI tools. | High | [AI policy](https://tpl.ca/policiesandterms-of-use/artificial-intelligence-policy/) |
| TPL runs a public AI literacy programme (AI Upskilling Initiative, $2.8M from Google.org, through Dec 2026) and an annual AI Summit (next: 28 Nov 2026). | High | [TPL blog](https://blogs.tpl.ca/digital_design_studio/2025/07/tpl-ai-upskilling-initiative-success-outcomes-and-highlights/), [AI Summit](https://tpl.ca/programs-and-classes/featured/learnai/2025-ai-summit/) |
| Late fines were abolished for everyone on 1 June 2022. Adults are still charged $1 per hold not picked up. | High (2022); current terms unverified | [TPL news](https://tpl.ca/news/toronto-public-library-ends-late-fines-for-all/) |
| About 100 branches. | Medium (2022 figure) | same |
| No official public API for live catalogue availability was found. Open data covers branch locations and catalogue records (MARC, Z39.50), published via the City of Toronto Open Data Portal. | Medium | [TPL open data](https://tpl.ca/open-data), [Open Data Policy](https://tpl.ca/policies-and-terms-of-use/open-data/) |

Not found: any confirmation that TPL has switched on AI features in its catalogue;
current loan periods and auto-renewal rules; anything on AI in TPL's returns handling.

### What the vendor is doing

BiblioCommons announced (2 Apr 2026) that it is exploring AI across the patron journey,
after a proof of concept with four public libraries:

- **Ask AI** — AI inside catalogue search to interpret complex, exploratory queries.
- **AI Guides** — contextual prompts during discovery.
- **Ask the Library** — a conversational tool for common questions (a separate product).
- A May 2026 trade briefing reports AI-powered search and review summaries added to its
  discovery interface.

Whether these are generally available, or enabled at TPL, is unconfirmed.
Sources: [press release](https://www.librarytechnology.org/pr/32422),
[proof-of-concept report](https://resources.bibliocommons.com/report-bibliocommons-ai-initiative-proof-of-concept-phase-1-findings),
[American Libraries briefing](https://americanlibrariesmagazine.org/2026/05/05/2026-library-systems-briefing/).

Implication for this project: TPL's own platform may gain AI search. An independent app
should aim at what the platform does not do well — location-aware "where can I get this
today" — rather than duplicate catalogue search.

## 2. Opportunities

Effort: S = days, M = weeks, L = needs TPL's cooperation or data we do not have.

### Search

| Idea | What the patron gets | Effort | Evidence |
|---|---|---|---|
| Natural-language search | "A short, funny novel set in Toronto" becomes a structured catalogue query. | S–M | BiblioCommons and Axiell are both shipping this; Calgary's "Scout" chatbot has given recommendations since 2018. |
| Vague-memory lookup | "Blue cover, lighthouse, came out around 2019" resolved to candidate titles. | S | Standard LLM capability; results must be checked against the catalogue so no title is invented. |
| Typo, transliteration and multilingual matching | Finds the book despite misspelling, or when searched in another language. Relevant to Toronto's multilingual population. | S | Common in semantic search products. |
| "Available now near me" alternatives | If the title is out everywhere nearby, suggest similar titles that are on a shelf within walking distance. | M | Extends recommendation engines with the location data this app already has. |
| Cover or shelf photo search | Photograph a book in a shop; see whether TPL has it and where. | M | Vision models handle this; no library-specific evidence found. |

### Borrowing

| Idea | What the patron gets | Effort | Evidence |
|---|---|---|---|
| Wait-time estimate | "About 3 weeks" in place of "47 holds on 12 copies". | M | Simple arithmetic gives a first estimate; a learned model needs historical data. Singapore's NLB used demand forecasting to cut waits (2013). |
| Best-branch recommendation | Weighs distance, opening hours, travel time and copies on shelf to say which branch to go to. | S–M | Mostly rules and routing; an LLM adds only the plain-language explanation. |
| Format switching | Print copy has a long queue; the e-book or audiobook is free now. | S | Requires format data the catalogue already exposes. |
| Reading-list planner | Paste a syllabus or book-club list; get one trip covering the most titles. | M | No library evidence found; a routing problem with an LLM front end for parsing the list. |
| Conversational help | Answers "how do I get a card", "can I return this at another branch". | S | Calgary's Scout; BiblioCommons "Ask the Library". Must answer from TPL's published policies only. |

### Returns

Evidence here is thin. Searches found no documented case of AI driving returns in a
public library; what exists is conventional automation (self-service kiosks, sorting
machines) and vendor projections without sources.

| Idea | What the patron gets | Effort | Evidence |
|---|---|---|---|
| Smart due-date reminders | Reminder timed to when the patron is near a branch or has a free evening, with the nearest open drop-off. | S–M | Needs access to the patron's loans, which means TPL login — see constraints. |
| Return-trip bundling | "Two items due Friday; your hold is ready at the same branch." | M | Same dependency. |
| Renewal likelihood | Warns that an item has holds on it and cannot be renewed, so plan to finish or return it. | S | Rule-based once loan data is available. |
| Demand-aware redistribution | Returned copies routed to branches where demand is highest. | L | Library-side operation. Only consultancy descriptions found, no reported deployments. |
| Automated check-in and condition assessment | Vision models flag damaged returns. | L | Library-side; no evidence found. |

## 3. Constraints

1. **Data access.** There is no documented public availability API. Building on
   undocumented BiblioCommons endpoints or scraping carries breakage and terms-of-use
   risk. Asking TPL or BiblioCommons for sanctioned access is the durable route.
2. **Patron privacy.** Borrowing history is among the most sensitive data a library
   holds, and Ontario's MFIPPA applies to TPL. Anything that touches a patron's account
   (loans, holds, reminders) needs their library credentials; a third-party app
   collecting those is a serious trust and security burden. Search and location features
   need none.
3. **Location data.** Use the device's location in the browser and do not store it.
4. **Accuracy.** A model may invent titles or misstate availability. Every title and
   every availability claim shown to the user must come from the catalogue, with the
   model limited to interpreting the query and wording the answer.
5. **Alignment with TPL's AI policy.** The policy governs TPL staff, not outside
   developers, but following its spirit (transparency, privacy, human oversight) matters
   if the app is ever shown to TPL.
6. **Equity.** AI features should be optional; plain keyword search must keep working.

## 4. Recommendation

Three phases, ordered by value against risk:

1. **With v1 or shortly after — no account access needed.** Natural-language and
   vague-memory search; plain-language best-branch explanation; simple wait-time
   estimate; format switching.
2. **Next — still no account access.** "Available now near me" alternatives;
   reading-list trip planner; photo search.
3. **Only with sanctioned TPL access.** Anything involving a patron's loans and holds:
   reminders, return bundling, renewal warnings.

Library-side operations (redistribution, automated check-in) are outside what an
independent app can do and are noted for completeness.

## 5. Open questions

- Does TPL's BiblioCommons deployment expose per-branch availability in a form a
  third-party app may use? What do the terms of use say?
- Has TPL enabled, or does it plan to enable, BiblioCommons's AI search?
- Current loan periods, renewal limits and auto-renewal rules.
- Would TPL's open data team consider an availability feed?
