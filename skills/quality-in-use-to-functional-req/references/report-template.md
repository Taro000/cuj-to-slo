# Stage 2 report template and worked example

## Contents
- Report template (`docs/quality/2-functional-requirements.md`)
- Worked example: online store
- Example items

Write the report in the working language. Translate the headings. Keep the IDs as they are, and write the basis marks and statuses with the glossary's terms for the working language.

Write each FR's statement in one place only, the "Functional requirements" table; everywhere else, refer to FRs by ID. Every copy of the same text is another place for the report and the items to drift apart.

## Report template

```markdown
# Functional requirements: <system name>

Status: draft | answers applied · Updated: <date> · Stage 2 of 4

## Summary
<Two or three sentences: how many FRs, how many new and how many existing, how many from proposals; what still needs agreement.>

## Inputs
| Source | What it gave |
|---|---|

## Functions by CUJ step
| CUJ | Step | FRs | Note |
|---|---|---|---|

## Quality-in-use coverage
| QIU | How it is covered | FRs | Quality hints for stage 3 |
|---|---|---|---|

## Functional requirements
| ID | Function | Acceptance criteria (summary) | Origin | Existing | Traces to | Basis |
|---|---|---|---|---|---|---|

## Functions considered and not added
| Candidate | For | Reason |
|---|---|---|

## Function proposals
| FRP | Proposed by | For | Decision | Result |
|---|---|---|---|---|

## Untraced functions
<Features in the inputs that trace to no CUJ step, QIU or KPI, and the question asked about each.>

## Needs agreement
| Item | What to agree | Approver candidates |
|---|---|---|

## Questions and answers
| # | Question | Drafted answer | Answer | What changed |
|---|---|---|---|---|

## Open gaps
<Include the functions you expect stage 3 to propose for laws, standards, risk analysis or policies.>
## Out of scope
## Next
Run `functional-req-to-product-quality`. After resolving proposals, name the later-stage items they affect and the stages to re-run for them.
```

## Worked example: online store

Inputs: the stage 1 artifacts of the worked example in `cuj-to-quality-in-use`, and a feature list of the live store: checkout with card payment (a declined card shows the reason and the buyer can pick another method), order history, product pages with stock status.

Functions by CUJ step (excerpt):

| CUJ | Step | FRs | Note |
|---|---|---|---|
| CUJ-01 Purchase | Cart | FR-04 | |
| CUJ-01 | Payment | FR-01, FR-02, FR-03, FR-08 | |
| CUJ-03 Month-end close | Reconcile with payments | FR-07 | Accounting reconciles by hand; the system supplies the mismatch list |

Quality-in-use coverage:

| QIU | How it is covered | FRs | Quality hints for stage 3 |
|---|---|---|---|
| QIU-01 Smartphone card checkout | Existing checkout, plus address checking | FR-01, FR-08 | Effectiveness (QIU-01) - checkout keeps working at busy times |
| QIU-02 Repeat purchase within T_repeat seconds | New function | FR-02 | Efficiency (QIU-02) - a repeat purchase finishes within T_repeat seconds |
| QIU-03 Never charged twice | New function | FR-03 | Economic risk mitigation (QIU-03) - no double charge, even when the provider times out |
| QIU-04 "In stock" means the item arrives | Existing stock display, plus stock changes from the warehouse | FR-04, FR-05 | Effectiveness (QIU-04) - the stock shown keeps up with the warehouse |
| QIU-05 Find the failing component within 15 minutes | New function | FR-06 | Efficiency (QIU-05) - every checkout request can be followed across services |
| QIU-06 Month-end figures match | New function | FR-07 | Economic risk mitigation (QIU-06) - the list is ready for the close |

Functional requirements (excerpt):

| ID | Function | Acceptance criteria (summary) | Origin | Existing | Traces to | Basis |
|---|---|---|---|---|---|---|
| FR-01 | The system confirms the order and takes payment (POST /checkout) | Confirmed orders appear in history; a declined card shows the reason and lets the buyer choose another method | cuj-step | yes | QIU-01, KPI-01 | stated (feature list) |
| FR-02 | The system lets signed-in buyers choose a saved address and card | Saved entries are offered; a new entry can be saved; an expired card is not offered | quality-in-use | no | QIU-02 | inferred |
| FR-03 | The system attaches a unique key to every payment request and refuses a second payment for the same order | See the Given/When/Then example in deriving-functions.md | quality-in-use | no | QIU-03, KPI-02 | inferred |
| FR-04 | The system shows the stock status on product pages and in the cart | "Out of stock" items cannot be added to the cart | cuj-step | yes | QIU-04 | stated (feature list) |
| FR-05 | The system receives stock-change events from the warehouse system and updates the stock shown (extends FR-04) | Each change updates the product page and the cart; a malformed event is rejected and logged | quality-in-use | no | QIU-04, KPI-03 | inferred |
| FR-06 | The system attaches a trace ID to every request and carries it across services | The ID appears in the logs of every service a checkout touches | quality-in-use | no | QIU-05 | inferred |
| FR-07 | The system matches orders against payments every day and lists the mismatches | Matched pairs are marked; mismatches are listed with the order and payment IDs | quality-in-use | no | QIU-06 | inferred |
| FR-08 | The system checks the delivery address against postal data and flags an address that does not exist before the order is confirmed | A non-existent address is flagged with a suggestion; the buyer can still correct it and continue | quality-in-use | no | QIU-01 | inferred (from a stage 1 hint) |

Functions considered and not added (excerpt):

| Candidate | For | Reason |
|---|---|---|
| Reorder a past order in one tap | QIU-02 | Saved address and card (FR-02) already remove the re-entry that QIU-02 names. |
| Notify the buyer when an item in the cart sells out | QIU-04 | The cart already flags the item before payment (FR-04). |

Function proposals, on a re-run after stage 3:

| FRP | Proposed by | For | Decision | Result |
|---|---|---|---|---|
| FRP-01 Switch to a secondary payment provider | functional-req-to-product-quality | QR-08 fault tolerance | Accepted | FR-09 |
| FRP-02 Send stock changes from the warehouse as events | functional-req-to-product-quality | QR-03 currentness | Rejected: FR-05 already receives every stock change as an event | FR-05 noted in the resolution |

## Example items

`docs/quality/fr/FR-03.yaml`:

```yaml
id: FR-03
title: Refuse a second payment for the same order
status: draft
version: 1
basis: inferred
basis_note: null
needs_agreement: false
approver_candidates: []
statement: The system attaches a unique key to every payment request and refuses a second payment for the same order.
origin: quality-in-use
cuj: CUJ-01
cuj_step: Payment
traces_to: [QIU-03, KPI-02]
proposal: null
existing: false
extends: null
interface: [POST /payments]
acceptance_criteria:
  - Given an order that is already paid, when a payment request with the same key arrives, then the system returns the first result and charges nothing.
  - Given a payment request whose first attempt timed out, when the buyer retries, then the system reuses the same key.
quality_hints:
  - Economic risk mitigation (QIU-03) - no double charge, even when the payment provider times out
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 2 draft}
```

`docs/quality/frp/FRP-01.yaml` after the re-run:

```yaml
id: FRP-01
title: Switch to a secondary payment provider
status: accepted
version: 2
basis: inferred
basis_note: null
needs_agreement: false
approver_candidates: []
proposed_by: functional-req-to-product-quality
origin: [QR-08]
changes: null
reason: Checkout must continue when the payment provider fails; no FR switches providers.
suggested_statement: The system routes payments to a secondary provider while the primary provider is failing.
affects: [NFR-01]
placeholders: []
resolution: Accepted as FR-09
changelog:
  - {version: 1, date: 2026-01-31, change: Proposed, reason: Stage 3 fault tolerance analysis}
  - {version: 2, date: 2026-02-03, change: Accepted as FR-09, reason: Stage 2 re-run}
```
