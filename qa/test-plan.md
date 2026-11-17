# QA test plan — TPL Book Locator v1

Status: prepared before development. Test IDs map to the ten approved use cases (UC1–UC10)
plus the map-first design and the travel-time requirement.

## Scope

In scope: search, map results, availability, location, travel time, branch details,
directions, hand-off to TPL, responsive layout, accessibility, failure handling.

Out of scope for v1: library login, placing holds inside the app, loans and returns,
AI search.

## Test levels

| Level | Tool | What it covers | When it runs |
|---|---|---|---|
| Unit | Vitest | Distance maths, opening-hours parsing, "best branch" ranking, catalogue response parsing | Every commit |
| Integration | Vitest, recorded responses | Server routes for search, availability, branches, travel time, including upstream failures | Every commit |
| End to end | Playwright, mocked upstream | The user journeys below, on phone and desktop sizes | Every pull request |
| Live smoke | Playwright, real services | One search, one availability lookup, one travel-time request | Daily, and before release |
| Manual | Real phones | Location prompt, map gestures, outdoor readability | Before release |

Recorded responses keep the automated suite stable; the daily live smoke is what detects
a change in TPL's catalogue or the routing service.

## Test data

- Popular title with many copies and holds.
- Title held at exactly one branch.
- Title with no copies on any shelf (all on loan).
- E-book-only title.
- Query with no matches; query with a typo; query with accents and non-Latin script.
- An ISBN-10 and an ISBN-13.
- User positions: downtown (Union Station), Scarborough east edge, Etobicoke west edge,
  and a point outside Toronto.
- Clock set to: mid-afternoon weekday, 5 minutes before a branch closes, Sunday morning,
  a statutory holiday.

## Functional test cases

### Search (UC1)

| ID | Case | Expected |
|---|---|---|
| S-01 | Search by title | Matching titles listed with cover, author, format, year |
| S-02 | Search by author | Titles by that author returned |
| S-03 | Search by ISBN-10 and ISBN-13 | The exact edition is returned |
| S-04 | No matches | Clear empty message; map stays usable |
| S-05 | Empty or whitespace query | No request sent; prompt to type a title |
| S-06 | Accents, apostrophes, non-Latin script | Handled without error |
| S-07 | Very long query and markup characters | Input shown as plain text; no script runs |
| S-08 | New search while one is loading | Only the latest results are shown |
| S-09 | Several formats of one title | Formats are distinguishable and selectable |

### Map as the result (UC2, UC5)

| ID | Case | Expected |
|---|---|---|
| M-01 | App opens | Full-screen map of Toronto with all branches shown as neutral dots |
| M-02 | Title selected | Branches with a copy on the shelf are highlighted; others fade |
| M-03 | Pin shows copy count | Pin size or label matches the number of copies on shelf |
| M-04 | Best branch | Map moves to the best branch and a one-line answer names it, the travel time and closing time |
| M-05 | Tap a pin | Card opens with address, hours, copies, travel time, directions, TPL link |
| M-06 | Pan, zoom, pinch, rotate device | Pins stay correctly placed; no layout break |
| M-07 | Dense area (downtown) | Overlapping pins remain individually selectable |
| M-08 | Clear the search | Map returns to the neutral state |
| M-09 | List view toggle | Same branches, same order, same data as the pins |
| M-10 | Status is not colour-only | Available and unavailable differ by shape or label as well as colour |

### Availability (UC2, UC9)

| ID | Case | Expected |
|---|---|---|
| A-01 | Copies on shelf at several branches | Count per branch matches the TPL catalogue page |
| A-02 | All copies on loan | No branch highlighted as available; holds and copy totals shown |
| A-03 | Single-branch title | Exactly one branch highlighted |
| A-04 | E-book only | Stated plainly; no branch pins; link to TPL |
| A-05 | Reference-only or in-library-use copy | Not presented as borrowable |
| A-06 | Freshness | The time the availability was fetched is displayed |
| A-07 | Branch in the catalogue but missing from branch data | Handled without error and logged |

### Location (UC3)

| ID | Case | Expected |
|---|---|---|
| L-01 | Permission granted | User marker shown; travel times calculated |
| L-02 | Permission denied | Address or postal code entry offered; app remains usable |
| L-03 | Valid postal code or address | Marker placed correctly |
| L-04 | Unrecognised address | Clear message; previous position kept |
| L-05 | Position outside Toronto | Works; nearest branches still ranked |
| L-06 | No location at all | Availability still shown; travel times omitted with an explanation |
| L-07 | Privacy | Position is not written to storage, logs or analytics |

### Travel time (UC4, UC6 — required in v1)

| ID | Case | Expected |
|---|---|---|
| T-01 | Transit time shown per available branch | Minutes shown; within a few minutes of a reference trip planner for the same trip and time |
| T-02 | Sort order | Branches ordered by travel time, not straight-line distance |
| T-03 | Mode switch (transit, walk) | Times and order update |
| T-04 | Time rings or filter (10, 20, 30 min) | Only branches within the limit are highlighted |
| T-05 | Branch closes before arrival | Flagged; not chosen as best branch |
| T-06 | Routing service slow | Pins appear first; times fill in afterwards |
| T-07 | Routing service fails | Falls back to an estimate from distance, labelled as an estimate |
| T-08 | Late night, no service | Walking time or "no transit now" shown, not an error |
| T-09 | Many branches available | Number of routing requests is capped; nearest candidates timed first |

### Branch details and directions (UC7, UC8)

| ID | Case | Expected |
|---|---|---|
| B-01 | Open now | Shows open and today's closing time |
| B-02 | Closed now | Shows closed and next opening time |
| B-03 | Closing within 30 minutes | Warning shown |
| B-04 | Sunday closures and holidays | Reflected, or the limitation is stated |
| B-05 | Directions button | Opens the device's maps app with the branch as destination and the selected mode |
| B-06 | Phone number | Tappable on a phone |

### Hand-off to TPL (UC10)

| ID | Case | Expected |
|---|---|---|
| H-01 | "Place hold on TPL" | Opens the correct title page on TPL in a new tab |
| H-02 | No credentials | The app never asks for a library card number or PIN |

## Non-functional

| ID | Area | Target |
|---|---|---|
| N-01 | Speed | Map usable within 3 s on a mid-range phone on 4G; pins within 3 s of choosing a title |
| N-02 | Phone layout | 360 px wide and up; no horizontal scroll; controls reachable by thumb |
| N-03 | Browsers | Current Safari (iOS), Chrome (Android), Chrome, Firefox, Edge |
| N-04 | Accessibility | WCAG 2.1 AA: keyboard reaches search, pins and cards; screen reader announces results; contrast 4.5:1; list view as map alternative |
| N-05 | Resilience | Catalogue down, routing down, tiles down, offline: each shows a specific message and a retry |
| N-06 | Security | No secrets in client code; inputs escaped; server routes rate-limited and restricted to the intended upstream hosts |
| N-07 | Privacy | No tracking of searches tied to location; no location stored |
| N-08 | Upstream courtesy | Responses cached; request rate to TPL and to the routing service capped |

## Release gate

- All unit, integration and end-to-end tests pass.
- Live smoke passes on the day of release.
- No open defect of severity 1 or 2 (below).
- Manual pass on one iPhone and one Android phone.
- Availability on five spot-checked titles matches the TPL catalogue.

## Defect severity

| Level | Meaning | Example |
|---|---|---|
| 1 | Wrong information that sends someone on a wasted trip, or the app is unusable | Branch shown as available when it is not |
| 2 | A core use case fails with no workaround | Travel times never load |
| 3 | A use case is impaired but has a workaround | List order differs from pin order |
| 4 | Cosmetic | Misaligned card on one screen size |

## Known risks to watch

- TPL's catalogue has no documented public interface; a change on their side can break
  search or availability without notice. The daily live smoke exists for this.
- The branch dataset on Toronto's open data portal is refreshed annually, so opening
  hours can drift from reality.
- The transit routing service is community-run with no service guarantee.
