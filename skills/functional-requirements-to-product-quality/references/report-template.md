# Stage 3 report template and worked example

## Contents
- Report template (`docs/quality/3-product-quality.md`)
- Worked example: online store
- Example items

Write the report in the working language. Translate the headings. Keep the IDs as they are, and write the basis marks, statuses, handlings, characteristics and other enum values with the glossary's terms for the working language.

The report indexes the items and never restates what they say: no statements, failure scenarios, conflict notes or reasons copied from the YAML. Refer to items by ID and title. The report holds only what the items do not: the summary, how the hints were used, the coverage checklist, the QRs counted in each CUJ, how internal QRs support external ones, the QRs moved after the confusable-pairs check, the decisions, the open assumptions, the placeholders, the open gaps and what is out of scope.

## Report template

```markdown
# Product quality and data quality: <system name>

Status: draft | answers applied · Updated: <date> · Inputs as of: <commit, or "none"> · Stage 3 of 4

## Summary
<Two or three sentences: emphasized characteristics per CUJ, number of QRs per handling, proposals raised, how many questions were asked, how many assumptions are open.>

## Inputs
| Source | What it gave |
|---|---|
<Documents and upstream artifacts, with the shared facts this stage added from them, by ID. The user's answers are under "Decisions".>

## Hints and how they were used
| Hint | Source | Used by | Reason if set aside |
|---|---|---|---|
<Every hint in context.yaml and every quality hint in the FRs, once each, as a short label. Hints worded the same way may share a row.>

## Coverage checklist
<One line per group: its CUJ and its FRs. Then one table per model.>

### Product quality
| Characteristic > sub-characteristic | <one column per group, headed by its CUJ ID> | Reason not targeted |
|---|---|---|
<One row per sub-characteristic. A cell holds the candidate QR IDs for that group, or "—" when the sub-characteristic is not targeted there. Give the reason for each "—" once, in the last column, prefixed with the CUJ IDs when the groups have different reasons. Rows whose cells and reason are all the same may be merged. A group walked for several CUJs together is headed by their IDs (CUJ-11–13). A QR with an entry point appears in a cell only when it is that group's candidate.>

### Data quality
| Characteristic | <one column per group> | Reason not targeted |
|---|---|---|
<One row per data characteristic, filled in the same way.>

## Priority
| CUJ | Chosen to emphasize | Emphasized for other CUJs | Minimum line | Excluded | Decided by |
|---|---|---|---|---|---|
<One row per CUJ, over the external and data QRs that trace to its QIUs, each with its score in parentheses, highest first. Chosen to emphasize: the QRs this CUJ chose; only these count toward its limit. Emphasized for other CUJs: the QRs that only other CUJs chose. Decided by: the decision (#n) that confirmed the chosen set, and the decision or QAS behind each exclusion. Internal QRs and QRs with an entry point are not listed. Under the table, name any CUJ that chose fewer than two or more than four, and any QIU left with only minimum-line QRs.>

## Internal quality derived from external quality
| External QR | Internal QR | How it supports the external one |
|---|---|---|

## Quality requirements
| ID | Title | Model / layer | Characteristic | Applies to | Traces to or entry point | Handling | Basis |
|---|---|---|---|---|---|---|---|
<Under the table, list each QR moved, merged or split after checking telling-apart.md, one line each with the reason.>

## Requirements that do not come from users
| QR | Type | Source | Brought by |
|---|---|---|---|

## Conflicts
| QRs | How it is resolved |
|---|---|
<By design, within what the QRs require; by an FRP (its ID); by decision #n; or by a provisional decision (its QAS). The QRs' notes say how; do not restate them.>

## Function proposals for stage 2
| FRP | Title | For |
|---|---|---|
<For: the QRs in the proposal's `origin`.>

## Decisions
| # | Question | Answer | Items |
|---|---|---|---|
<The questions asked and the answers in effect, numbered per stage and continued on a re-run. An answer that overturns an earlier one replaces its row.>

## Open assumptions
| QAS | Title | Kind | Breadth | Severity | Items that depend on it |
|---|---|---|---|---|---|
<The open assumptions this stage wrote, and the open upstream ones its items depend on.>

## Placeholders
| Name | Stands for | Used in |
|---|---|---|
<The placeholders this stage's QRs use: those from earlier stages and those this stage named. Stage 4 proposes their values.>

## Open gaps
<Include the gaps in stage 1's part of context.yaml (a person who produces data, a user's data role), and the QRs left without a function because stage 2 rejected a proposal. The shared facts this stage added or corrected are in context.yaml, named under "Inputs" or "Decisions".>
## Out of scope
## Next
<If FRPs were raised: re-run `quality-in-use-to-functional-requirements`, then this stage for the QRs in their `origin`. If there are gaps for stage 1, re-run it first. Name every stage, earlier ones included, whose items cite a shared fact this run added or corrected. Then run `product-quality-to-slo`.>
```

## Worked example: online store

Coverage checklist (excerpt). Groups: CUJ-01 Purchase, FR-01 to FR-05, FR-08 and FR-10; CUJ-02 Isolate a checkout failure, FR-06.

| Characteristic > sub-characteristic | CUJ-01 | CUJ-02 | Reason not targeted |
|---|---|---|---|
| Reliability > fault tolerance | QR-08 | — | CUJ-02: no external system takes part in FR-06 |
| Performance efficiency > capacity | QR-09 | — | CUJ-02: a few SREs use FR-06 at a time, with no load to carry |
| Portability > adaptability, installability, replaceability | — | — | No change in the environments the store is used in, and no replacement, is planned this cycle |
| Usability > user interface aesthetics | — | — | No failure scenario that hurts a QIU of either CUJ |

| Characteristic | CUJ-01 | CUJ-02 | Reason not targeted |
|---|---|---|---|
| Accuracy | QR-05 (prices) | — | CUJ-02: FR-06 shows no values that people enter |
| Traceability | — | QR-07 (payments) | CUJ-01: buyers never follow a payment from order to charge themselves |

Priority (excerpt):

| CUJ | Chosen to emphasize | Emphasized for other CUJs | Minimum line | Excluded | Decided by |
|---|---|---|---|---|---|
| CUJ-01 Purchase | QR-01 (16), QR-04 (15), QR-02 (12), QR-03 (12) | | QR-05 (12), QR-08 (9), QR-09 (8) | | #1 |

Quality requirements (excerpt):

| ID | Title | Model / layer | Characteristic | Applies to | Traces to or entry point | Handling | Basis |
|---|---|---|---|---|---|---|---|
| QR-01 | Orders can be confirmed whenever the store is open | product / external | reliability > availability | FR-01 | QIU-01 | emphasized | inferred |
| QR-02 | Order confirmation keeps buyers from waiting up to the sale peak | product / external | performance efficiency > time behaviour | FR-01 | QIU-01, QIU-02 | emphasized | inferred |
| QR-03 | Stock shown follows the warehouse | data / data-inherent | currentness | FR-04, FR-05 | QIU-04 | emphasized | inferred |
| QR-04 | Order totals are right and charged once | product / external | functional suitability > functional correctness | FR-01, FR-03 | QIU-03 | emphasized | inferred |
| QR-05 | Registered prices are right | data / data-inherent | accuracy | FR-01 | QIU-03 | minimum-line | inferred |
| QR-08 | Checkout survives a payment provider failure | product / external | reliability > fault tolerance | FR-01 | QIU-01 | minimum-line | inferred |
| QR-11 | Whole purchase works with the keyboard alone | product / external | usability > accessibility | FR-01, FR-02, FR-08 | entry point: standard | minimum-line | assumed |

Requirements that do not come from users (excerpt):

| QR | Type | Source | Brought by |
|---|---|---|---|
| QR-11 | standard | Accessibility guideline | Product owner |

Conflicts (excerpt):

| QRs | How it is resolved |
|---|---|
| QR-02, QR-08 | By design |
| QR-03, QR-10 | By design |

Function proposals for stage 2 (excerpt):

| FRP | Title | For |
|---|---|---|
| FRP-01 | Keep checkout working when the payment provider fails | QR-08 |

Decisions (excerpt):

| # | Question | Answer | Items |
|---|---|---|---|
| 1 | Five QRs look indispensable for CUJ-01 Purchase. Which four are emphasized? | QR-01, QR-02, QR-03, QR-04; QR-05 is a minimum line | QR-01, QR-02, QR-03, QR-04, QR-05 |
| 2 | What does the accessibility guideline require of the purchase? | Every purchase step can be done with the keyboard alone | QR-11 |

Open assumptions (excerpt):

| QAS | Title | Kind | Breadth | Severity | Items that depend on it |
|---|---|---|---|---|---|
| QAS-07 | Keyboard-only purchase kept as a minimum line | decision | low | low | QR-11 |

Placeholders:

| Name | Stands for | Used in |
|---|---|---|
| T_stock_lag | Longest time from a stock change reported by the warehouse system (EXT-02) to the stock shown to buyers reflecting it | QR-03 |

`T_repeat` is not listed: it is the buyer's time for a repeat purchase (QIU-02), and no QR states that quantity. QR-02 traces to QIU-02 but states the system's response time.

Out of scope (excerpt): "Warehouse staff register incoming stock before a sale starts" would also help QR-03, but only a business procedure can do it.

## Example items

`docs/quality/qr/QR-03.yaml`:

```yaml
id: QR-03
title: Stock shown follows the warehouse
status: active
basis: inferred
basis_note: Handling confirmed in question 1. Likelihood judged from how often stock changes during a sale; the design must confirm how quickly changes from EXT-02 can reach the store.
premises: [EXT-02]
model: data
layer: data-inherent
characteristic: currentness
statement: The stock shown on product pages and in the cart never lags the actual stock in the warehouse system (EXT-02) by more than T_stock_lag, so that sold-out items are not ordered.
applies_to: [FR-04, FR-05]
traces_to: [QIU-04]
entry_point: null
stakeholders: [USR-01, EXT-02]
failure_scenario: Stock changes in the warehouse system (EXT-02) reach the store late; sold-out items are ordered and then cancelled.
score: {impact: 4, likelihood: 3}
handling: emphasized
handling_reason: null
implies_internal: []
conflicts:
  - {with: QR-10, note: "Carrying the sale-peak load of stock lookups (QR-10) pulls toward showing older stock; whatever the design does, the stock shown never lags the warehouse by more than T_stock_lag"}
stated_numbers: []
placeholders:
  - {name: T_stock_lag, meaning: "Longest time from a stock change reported by EXT-02 to the stock shown reflecting it", from: QR-03}
```

`docs/quality/frp/FRP-01.yaml`:

```yaml
id: FRP-01
title: Keep checkout working when the payment provider fails
status: proposed
basis: inferred
basis_note: null
premises: [EXT-01]
proposed_by: functional-requirements-to-product-quality
origin: [QR-08]
changes: null
reason: Checkout must continue when the payment provider fails; no FR covers that case.
suggested_statement: The system keeps confirming orders while the payment provider is failing.
affects: []
placeholders: []
resolution: null
```

`docs/quality/qas/QAS-07.yaml`, the provisional decision that QR-11 lists in its `premises` (QR-11 is `assumed` until it is settled):

```yaml
id: QAS-07
title: Keyboard-only purchase kept as a minimum line
status: open
stage: 3
kind: decision
statement: Keyboard-only operation of the purchase, which the accessibility guideline requires, is kept as a minimum line rather than emphasized.
why_unsure: The guideline's content was confirmed, but nothing in the inputs says how much weight the business gives it this cycle.
breadth: low
severity: low
rating_note: One QR depends on it, and a minimum line is easy to raise later
answer: null
answered_in: null
```
