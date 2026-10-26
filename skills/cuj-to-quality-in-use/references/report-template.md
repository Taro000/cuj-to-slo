# Stage 1 report template and worked example

## Contents
- Report template (`docs/quality/1-quality-in-use.md`)
- Worked example: online store
- Example items

Write the report in the working language. Translate the headings. Keep the IDs as they are, and write the basis marks and statuses with the glossary's terms for the working language.

## Report template

```markdown
# Quality in use and KPIs: <system name>

Status: draft | answers applied · Updated: <date> · Stage 1 of 4

## Summary
<Two or three sentences: which CUJs are protected, how many QIUs and KPIs, what still needs agreement.>

## Inputs
| Source | What it gave |
|---|---|

## System boundary
- Boundary: …
- Purpose: …
- Constraints: …
- Systems that create data: …

## Business requirements
| ID | KGI | Measure | Target | Basis |
|---|---|---|---|---|

| ID | Intermediate outcome | Verb | KGI | Basis |
|---|---|---|---|---|

## Users
| ID | User | Category | Data role | Considered | Reason if excluded | Basis |
|---|---|---|---|---|---|---|

## CUJs
| ID | CUJ | Users | User's goal | Steps | Outcomes | Basis |
|---|---|---|---|---|---|---|

## Quality-in-use requirements
| ID | User | CUJ | Context of use | Characteristic | Requirement | Basis |
|---|---|---|---|---|---|---|

### Not targeted
<One row per reason; a row can cover several CUJs and characteristics.>
| CUJs | Characteristics | Reason |
|---|---|---|

## One formula, two counts
| Outcome | Formula (A ÷ B) | Narrowed: quality-in-use measure | Overall: means KPI | Overlap |
|---|---|---|---|---|

## Hints for later stages
<For stage 3: statements in the inputs whose subject is the product or the data, and requirements that do not come from users (laws, standards and guidelines, security risk analysis, development policies). For stage 4: operational facts (telemetry, volumes, peaks, existing SLOs or SLAs, baselines). The same lists are in context.yaml.>

## Needs agreement
| Item | What to agree | Approver candidates |
|---|---|---|

## Questions and answers
| # | Question | Drafted answer | Answer | What changed |
|---|---|---|---|---|

## Open gaps
## Out of scope
## Next
Run `quality-in-use-to-functional-requirements`.
```

## Worked example: online store

Input given by the user:

- Users: buyers, warehouse staff, SREs, accounting.
- CUJs: purchase (cart → payment → order confirmation); SREs isolate a checkout failure (alert → dashboards → failing component); accounting closes the month with the monthly sales report it receives (receive the report → reconcile with payments → close).
- KGI: revenue, +10% year over year.
- Outcomes: "more people finish buying", "fewer cancellations because something was out of stock".
- Requirements: "the phone checkout must be quick"; "we must never double-charge"; "SREs should isolate a checkout failure within 15 minutes of the alert".
- Facts: about 300,000 checkouts a day; load balancer access logs and app events exist.

Business requirements:

| ID | Intermediate outcome | Verb | KGI | Basis |
|---|---|---|---|---|
| OUT-01 | Increase the number of buyers who complete a purchase | increase | KGI-01 | stated |
| OUT-02 | Decrease order cancellations caused by stock-outs | decrease | KGI-01 | stated |
| OUT-03 | Decrease refund requests and billing inquiries from buyers | decrease | KGI-01 | confirmed (input said "we must never double-charge"; question 3) |

Users:

| ID | User | Category | Data role | Considered | Reason if excluded |
|---|---|---|---|---|---|
| USR-01 | Buyer | primary | consumer | yes | |
| USR-02 | Warehouse staff | secondary-operation | producer (stock counts) | no | They work only in the warehouse system, outside the boundary. Their stock counts reach buyers as data, so the warehouse system is recorded under "Systems that create data". |
| USR-03 | SREs | secondary-maintenance | none | yes | |
| USR-04 | Accounting | indirect | consumer (monthly sales report) | yes | |

Accounting is indirect here because it only receives the sales report. If it worked in the system itself, it would be a primary user.

Quality-in-use requirements:

| ID | User | CUJ | Context of use | Characteristic | Requirement | Basis |
|---|---|---|---|---|---|---|
| QIU-01 | Buyer | CUJ-01 Purchase | Smartphone, card payment | effectiveness | Buyers on smartphones complete card checkout with the right items, quantity and address. | inferred (context confirmed in question 1; the measure is inferred) |
| QIU-02 | Buyer | CUJ-01 | Repeat purchase on a smartphone | efficiency | Repeat buyers on smartphones finish a second purchase within T_repeat seconds, without re-entering their address or card. | inferred (T_repeat is not in the input and needs agreement) |
| QIU-03 | Buyer | CUJ-01 | All payment methods | freedom from risk > economic risk mitigation | Buyers are never charged twice or billed the wrong amount. | confirmed (question 3) |
| QIU-04 | Buyer | CUJ-01 | Product page and cart | effectiveness | Buyers who see "in stock" receive the item without a stock-out cancellation. | inferred (rewritten from OUT-02 with the buyer as the subject) |
| QIU-05 | SREs | CUJ-02 Isolate a checkout failure | On call, production | efficiency | SREs find the failing component of a checkout failure within 15 minutes of the alert. | inferred (statement and 15 minutes from the input; context inferred) |
| QIU-06 | Accounting | CUJ-03 Month-end close | Month-end | freedom from risk > economic risk mitigation | Accounting closes the month with sales figures that match the payments. | inferred |

Not targeted (excerpt):

| CUJs | Characteristics | Reason |
|---|---|---|
| CUJ-01 | satisfaction > pleasure | Recommendations are not part of this CUJ. |
| CUJ-01 | context coverage > flexibility | The input names no buyers or uses beyond those it describes. |
| CUJ-01, CUJ-02, CUJ-03 | satisfaction > comfort; freedom from risk > health and safety risk mitigation; freedom from risk > environmental risk mitigation | No failure scenario in the input hurts an outcome. |

One formula, two counts:

| Outcome | Formula (A ÷ B) | Narrowed: quality-in-use measure | Overall: means KPI | Overlap |
|---|---|---|---|---|
| OUT-01 | Orders confirmed ÷ checkouts started | QIU-01: smartphone, card payment, signed-in buyers | KPI-01 Purchase completion rate | yes |
| OUT-03 | Double charges and wrong bills ÷ payments | QIU-03: per payment method | KPI-02 Billing error rate | yes |
| OUT-02 | Cancellations for stock-outs ÷ orders | QIU-04: product page and cart, all buyers | KPI-03 Stock-out cancellation rate | yes |
| OUT-01 | Repeat purchases finished within T_repeat seconds ÷ repeat purchases started | QIU-02 | none | no: the business does not count purchase time overall (question 4) |

Hints for later stages:

- For stage 3: "Flag addresses that do not exist before confirmation" (subject: the product; user error protection). "Stock shown on the product page must be up to date" (subject: the data; currentness).
- For stage 4: about 300,000 checkouts a day; load balancer access logs and app events exist.

Needs agreement:

| Item | What to agree | Approver candidates |
|---|---|---|
| QIU-02 | T_repeat: the seconds within which a repeat purchase should finish | Product owner |

Questions asked after the draft (excerpt):

| # | Question | Drafted answer | Answer | What changed |
|---|---|---|---|---|
| 1 | Is "phone checkout must be quick" about card payment only, or every payment method? | Card payment only (most phone orders use cards) | Card payment only | QIU-01's context marked confirmed |
| 2 | Should marketing staff who run campaigns be considered as secondary users? | Excluded: they do not use the checkout | Excluded | Reason recorded |
| 3 | "We must never double-charge" reads as a business requirement. Should it become the outcome "decrease refund requests and billing inquiries from buyers"? | Yes, as OUT-03 | Yes | OUT-03 and QIU-03 marked confirmed |
| 4 | The input names no KPIs. Which of these does the business count overall: purchase completion, billing errors, stock-out cancellations, repeat-purchase time? | The first three | The first three | KPI-01 to KPI-03 marked confirmed; QIU-02 has no KPI |

## Example items

`docs/quality/qiu/QIU-01.yaml`:

```yaml
id: QIU-01
title: Buyers finish card checkout on smartphones
status: draft
version: 1
basis: inferred
basis_note: Context of use confirmed in question 1; the measure is inferred
needs_agreement: false
approver_candidates: []
user: USR-01
cuj: CUJ-01
outcomes: [OUT-01]
need: Finish checkout without being kept waiting, even at busy times
context_of_use:
  device: Smartphone
  network: Any
  user_type: Signed-in buyer
  task: Card payment
  environment: null
  other: null
characteristic: effectiveness
statement: Buyers on smartphones complete card checkout with the right items, quantity and address.
failure_scenario: Buyers give up during payment and no order is placed.
measure:
  numerator: Orders confirmed
  denominator: Checkouts started
  narrowed_to: Smartphone, card payment, signed-in buyers; test accounts excluded
  denominator_needed_only: Only buyers who started checkout are counted
  contribution: Event sits right after the checkout flow; A/B testing is possible
  observable: true
  compared_with: null
  scale: ratio
kpi: KPI-01
target: null
target_status: undecided
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 1 draft; question 1 answered}
```

`docs/quality/kpi/KPI-01.yaml`:

```yaml
id: KPI-01
title: Purchase completion rate
status: draft
version: 1
basis: confirmed
basis_note: null
needs_agreement: false
approver_candidates: []
outcome: OUT-01
numerator: Orders confirmed
denominator: Checkouts started
counted_over: All users and all contexts of use
overlaps_with: [QIU-01]
overlap_note: null
target: null
target_status: undecided
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 1 draft; question 4 answered}
```
