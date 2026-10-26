# Stage 3 report template and worked example

## Contents
- Report template (`docs/quality/3-product-quality.md`)
- Worked example: online store
- Example items

Write the report in the working language. Translate the headings. Keep the IDs as they are, and write the basis marks and statuses with the glossary's terms for the working language. Write each QR's statement in the "Quality requirements" table only; elsewhere, refer to QRs by ID.

## Report template

```markdown
# Product quality and data quality: <system name>

Status: draft | answers applied · Updated: <date> · Stage 3 of 4

## Summary
<Two or three sentences: emphasized characteristics per CUJ, number of QRs per handling, proposals raised, what needs agreement.>

## Inputs
| Source | What it gave |
|---|---|

## Hints and how they were used
| Hint (source) | Used by | Reason if set aside |
|---|---|---|
<Every hint in context.yaml and every quality hint in the FRs, once each. Hints worded the same way may share a row.>

## Coverage checklist
### <CUJ-xx name>
| Model | Characteristic > sub-characteristic | Candidate (QR) or reason not targeted |
|---|---|---|
<Every product sub-characteristic and every data characteristic appears once per group. Lines that share a reason may be merged.>

## Priority table
| CUJ | QR | Characteristic | Failure scenario | Impact | Likelihood | Score | Handling | Basis |
|---|---|---|---|---|---|---|---|---|
<A QR that traces to QIUs of two CUJs has a row in each. Under the table, give the number of emphasized QRs per CUJ, and name any QIU left with only minimum-line QRs.>

## Internal quality derived from external quality
| External QR | Internal QR | How it supports the external one |
|---|---|---|

## Quality requirements
| ID | Model / layer | Characteristic | Requirement | Applies to | Traces to | Basis |
|---|---|---|---|---|---|---|

## Requirements that do not come from users
| QR | Type | Source | Brought by | Requirement |
|---|---|---|---|---|

## Conflicts
| QRs | Conflict | Resolution |
|---|---|---|

## Function proposals for stage 2
| FRP | For | Function needed | Why no FR covers it |
|---|---|---|---|

## Needs agreement
| Item | What to agree | Approver candidates |
|---|---|---|

## Questions and answers
| # | Question | Drafted answer | Answer | What changed |
|---|---|---|---|---|

## Open gaps
<Include changes stage 1 needs (a data producer to add, a data role to correct), and the QRs moved, merged or split after checking telling-apart.md.>
## Out of scope
## Next
<If FRPs were raised: re-run `quality-in-use-to-functional-req`, then this stage for the new FRs. If stage 1 needs changes, re-run it first. Then run `product-quality-to-slo`.>
```

## Worked example: online store

Coverage checklist for CUJ-01 Purchase (excerpt):

| Model | Characteristic > sub-characteristic | Candidate (QR) or reason not targeted |
|---|---|---|
| Product | Reliability > availability | QR-01 |
| Product | Reliability > fault tolerance | QR-08 |
| Product | Performance efficiency > time behaviour | QR-02 |
| Product | Performance efficiency > capacity | QR-09 (minimum line: sale peak of 300 requests/s stated) |
| Product | Functional suitability > functional correctness | QR-04 |
| Product | Portability > adaptability, installability, replaceability | Not targeted: one runtime, no move or replacement planned this cycle |
| Product | Usability > user interface aesthetics | Not targeted: no failure scenario that hurts a QIU of this CUJ |
| Data | Currentness (stock) | QR-03 |
| Data | Accuracy (price master) | QR-05 |
| Data | Precision | Not targeted: prices are whole currency units; weights are not used at checkout |

Quality requirements (excerpt):

| ID | Model / layer | Characteristic | Requirement | Applies to | Traces to |
|---|---|---|---|---|---|
| QR-01 | product / external | reliability > availability | Buyers can confirm orders whenever the store is open; failed confirmations are rare. | FR-01 | QIU-01 |
| QR-02 | product / external | performance efficiency > time behaviour | Order confirmation responds fast enough that buyers are not kept waiting, up to sale-peak load. | FR-01 | QIU-01, QIU-02 |
| QR-03 | data / data-inherent | currentness | The stock shown on product pages and in the cart follows the warehouse's actual stock closely enough that sold-out items are not ordered. | FR-04, FR-05 | QIU-04 |
| QR-04 | product / external | functional suitability > functional correctness | Order totals are correct with shipping, coupons and points combined, and no order is charged twice. | FR-01, FR-03 | QIU-03 |
| QR-08 | product / external | reliability > fault tolerance | Checkout continues when the payment provider fails. | FR-01 | QIU-01 |
| QR-11 | product / external | usability > accessibility | The purchase can be completed with the keyboard alone. | FR-01, FR-02, FR-08 | entry point: accessibility guideline (brought by the product owner) |

Function proposals for stage 2:

| FRP | For | Function needed | Why no FR covers it |
|---|---|---|---|
| FRP-01 | QR-08 | Route payments to a secondary provider while the primary is failing | FR-01 calls a single provider |

Out of scope (excerpt): "Warehouse staff register incoming stock before a sale starts" would also help QR-03, but only a business procedure can do it.

## Example items

`docs/quality/qr/QR-03.yaml`:

```yaml
id: QR-03
title: Stock shown follows the warehouse
status: draft
version: 1
basis: inferred
basis_note: Handling needs agreement; the failure scenario follows QIU-04
needs_agreement: true
approver_candidates: [Product owner, Logistics systems lead]
model: data
layer: data-inherent
characteristic: currentness
statement: The stock shown on product pages and in the cart follows the warehouse's actual stock closely enough that sold-out items are not ordered.
applies_to: [FR-04, FR-05]
traces_to: [QIU-04]
entry_point: null
stakeholders: [USR-01, USR-02]
failure_scenario: Stock updates lag; sold-out items are ordered and then cancelled.
score: {impact: 4, likelihood: 3}
handling: emphasized
handling_reason: null
implies_internal: []
conflicts:
  - {with: QR-10, note: Caching the stock API lowers its load but ages the data; cache entries must not outlive the freshness threshold that stage 4 sets}
stated_numbers: []
placeholders: []
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 3 draft}
```

`docs/quality/frp/FRP-01.yaml`:

```yaml
id: FRP-01
title: Switch to a secondary payment provider
status: proposed
version: 1
basis: inferred
basis_note: null
needs_agreement: false
approver_candidates: []
proposed_by: functional-req-to-product-quality
origin: [QR-08]
changes: null
reason: Checkout must continue when the payment provider fails; no FR switches providers.
suggested_statement: The system routes payments to a secondary provider while the primary provider is failing.
affects: []
placeholders: []
resolution: null
changelog:
  - {version: 1, date: 2026-01-31, change: Proposed, reason: Stage 3 fault tolerance analysis}
```
