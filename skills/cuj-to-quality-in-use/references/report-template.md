# Stage 1 report template and worked example

## Contents
- Report template (`docs/quality/1-quality-in-use.md`)
- Worked example: online store
- Example items

Write the report in the working language. Translate the headings. Keep the IDs as they are, and write the basis marks, statuses and other enum values with the glossary's terms for the working language.

The report indexes the items and holds only what no item holds (`artifacts.md`, "Layout"). Never restate what an item or `context.yaml` says: no statements, goals, steps, needs, measures, formulas, failure scenarios, target values or exclusion reasons, and none of the text of constraints, external systems, operational facts, design inputs or hints. The tables show IDs, titles or names, enum values and references; where an entry has no title (an outcome), use a label of a few words.

## Report template

```markdown
# Quality in use and KPIs: <system name>

Status: draft | answers applied · Updated: <date> · Inputs as of: <commit, or "none"> · Stage 1 of 4

## Summary
<Two or three sentences: the system in one line of your own words, which CUJs are protected, how many QIUs and KPIs, how many questions were asked, how many assumptions are open.>

## Inputs
| Source | What it gave |
|---|---|
<The documents the user gave. The user's answers are under "Decisions".>

## Shared facts
<One line: the boundary and purpose are in `system` in context.yaml; then the number of shared facts of each kind (constraints, external systems and how many of them create data, operational facts, design inputs). The entries themselves are only in context.yaml, where later stages add their own.>

## Business requirements
| ID | KGI | Basis |
|---|---|---|

| ID | Intermediate outcome | Verb | KGIs | Basis |
|---|---|---|---|---|
<A label of a few words for each outcome.>

## Users
| ID | User | Category | Data role | Considered | Basis |
|---|---|---|---|---|---|

## CUJs
| ID | CUJ | Users | Outcomes | Basis |
|---|---|---|---|---|

## Quality-in-use requirements
| ID | Title | User | CUJ | Characteristic | KPI | Target | Basis |
|---|---|---|---|---|---|---|---|
<Target: the target status.>

### Not targeted
<One row per reason; a row can cover several CUJs and characteristics.>
| CUJs | Characteristics | Reason |
|---|---|---|

## One formula, two counts
| Outcome | QIU (narrowed) | KPI (overall) | Overlap |
|---|---|---|---|
<One row per formula, with the ID and title of the QIU and of the KPI, or "none". Overlap: yes; or no, followed by where the reason is (the KPI's overlap_note, a decision, a QAS) or, when no item holds it, the reason in a few words.>
<Below the table: the outcomes whose narrowed and overall counts are the same because the input names one context of use only; and, when a KPI has no overlap, that it can move against quality in use.>

## Hints for later stages
<How many hints context.yaml holds for stage 3, by kind. The hints themselves are only in context.yaml.>

## Decisions
| # | Question | Answer | Items |
|---|---|---|---|
<The questions asked and the answers in effect, numbered per stage and continued on a re-run. An answer that overturns an earlier one replaces its row.>

## Open assumptions
| QAS | Title | Kind | Breadth | Severity | Items that depend on it |
|---|---|---|---|---|---|
<The open assumptions this stage wrote, highest ratings first.>

## Placeholders
| Name | Stands for | Used in |
|---|---|---|

## Open gaps
<What is missing and where it matters: a KGI without a measure or a target, a CUJ without steps, an event the product cannot observe (observable: false).>

## Out of scope
<One line each: a need that only business procedures, operations or a system outside the boundary can meet, and why.>

## Next
Run `quality-in-use-to-functional-requirements`. <On a re-run: which later stages to re-run, and for which items.>
```

## Worked example: online store

Input given by the user:

- Users: buyers, warehouse staff, SREs, accounting.
- CUJs: purchase (cart → payment → order confirmation); SREs isolate a checkout failure (alert → dashboards → failing component); accounting closes the month with the monthly sales report it receives (receive the report → reconcile with payments → close).
- KGI: revenue, measured as monthly gross sales, +10% year over year.
- Outcomes: "more people finish buying", "fewer cancellations because something was out of stock".
- Requirements: "the phone checkout must be quick"; "we must never double-charge"; "SREs should isolate a checkout failure within 15 minutes of the alert"; "flag addresses that do not exist before the order is confirmed"; "stock shown on the product page must be up to date"; and a line of the PRD: "checkout should answer within 1.5 s at the sale peak of about 300 requests per second".
- Constraints and other systems: "the store never handles card numbers; the payment provider takes card payments and refunds"; "stock counts come from the warehouse system"; a security team memo: "card data must be handled under the card industry's security standard".
- Facts: about 300,000 checkouts a day; a sale peak of about 300 checkout requests per second; load balancer access logs and application events already exist; a load test report: "a load test at the sale peak answered 99.4% of checkouts within 1.5 s".
- Kickoff notes: "the storefront stays a web app this year; no native app".

How the draft sorts the input:

- The kickoff note is a design decision. It becomes DIN-01 and goes into no item: QIU-01's context of use says "smartphone", the kind of device, and nothing about the platform. Nor is the platform drafted as an assumption.
- The card rule becomes CON-01, and the two systems outside the boundary become EXT-01 (payment provider) and EXT-02 (warehouse system), both with `creates_data: true`. The volumes, the logs and the load test are OPF-01 to OPF-04: the logs already exist, so they are a fact, not a design choice, and the load test is a baseline that stage 4 can start a target from.
- The address check and the PRD's 1.5 s line have the product as their subject, the up-to-date stock has the data, and the security standard does not come from users: all four are hints for stage 3. The 1.5 s hint keeps its source (PRD) and names the sale peak as OPF-03 instead of copying it. What the standard requires is asked in stage 3, not here.

Excerpts of the report follow. The notes between them explain the example and are not part of the report.

Header and summary:

> Status: answers applied · Updated: 2026-02-03 · Inputs as of: 4e1c7a2 · Stage 1 of 4
>
> An online store where buyers browse, buy and see their orders; the warehouse system is outside it. Buyers' purchases are protected, with failure isolation for SREs and the month-end close for accounting: six QIUs and three KPIs. Four questions were asked, and one assumption is open.

Shared facts: the boundary and purpose are in `system` in `context.yaml`, with one constraint, two external systems (both create data), four operational facts and one design input.

Business requirements:

| ID | KGI | Basis |
|---|---|---|
| KGI-01 | Revenue | stated |

| ID | Intermediate outcome | Verb | KGIs | Basis |
|---|---|---|---|---|
| OUT-01 | More completed purchases | increase | KGI-01 | stated |
| OUT-02 | Fewer stock-out cancellations | decrease | KGI-01 | stated |
| OUT-03 | Fewer refund requests and billing inquiries | decrease | KGI-01 | confirmed |

Users:

| ID | User | Category | Data role | Considered | Basis |
|---|---|---|---|---|---|
| USR-01 | Buyer | primary | consumer | yes | stated |
| USR-02 | Warehouse staff | secondary-operation | producer | no | inferred |
| USR-03 | SREs | secondary-maintenance | none | yes | stated |
| USR-04 | Accounting | indirect | consumer | yes | inferred |
| USR-05 | Marketing staff | secondary-operation | none | no | confirmed |

Warehouse staff work only in the warehouse system, outside the boundary, so they are excluded; their stock counts still reach buyers as data, which is why EXT-02 has `creates_data: true`. Accounting is indirect because it only receives the sales report; if it worked in the system itself, it would be a primary user. The reasons for the exclusions are in `exclusion_reason` in `context.yaml`, not in the report.

Quality-in-use requirements:

| ID | Title | User | CUJ | Characteristic | KPI | Target | Basis |
|---|---|---|---|---|---|---|---|
| QIU-01 | Buyers finish card checkout on smartphones | USR-01 | CUJ-01 | effectiveness | KPI-01 | undecided | inferred |
| QIU-02 | Repeat buyers finish a second purchase quickly | USR-01 | CUJ-01 | efficiency | none | undecided | inferred |
| QIU-03 | Buyers are never charged twice or billed wrongly | USR-01 | CUJ-01 | freedom from risk > economic risk mitigation | KPI-02 | undecided | inferred |
| QIU-04 | Buyers receive what was shown as in stock | USR-01 | CUJ-01 | effectiveness | KPI-03 | undecided | inferred |
| QIU-05 | SREs isolate a checkout failure in time | USR-03 | CUJ-02 | efficiency | none | undecided | assumed |
| QIU-06 | Accounting closes the month on figures that match payments | USR-04 | CUJ-03 | freedom from risk > economic risk mitigation | none | undecided | inferred |

QIU-01 and QIU-03 stay `inferred` although questions settled part of them (the context of one, the outcome of the other): their measures are still the draft's own judgment, as each `basis_note` says. QIU-05 is `assumed` because its context of use rests on QAS-01.

Not targeted (excerpt):

| CUJs | Characteristics | Reason |
|---|---|---|
| CUJ-01 | satisfaction > pleasure | Recommendations are not part of this CUJ. |
| CUJ-01 | context coverage > flexibility | The input names no buyers or uses beyond those it describes. |
| CUJ-01, CUJ-02, CUJ-03 | satisfaction > comfort; freedom from risk > health and safety risk mitigation; freedom from risk > environmental risk mitigation | No failure scenario in the input hurts an outcome. |

One formula, two counts (excerpt):

| Outcome | QIU (narrowed) | KPI (overall) | Overlap |
|---|---|---|---|
| OUT-01 | QIU-01 Buyers finish card checkout on smartphones | KPI-01 Purchase completion rate | yes |
| OUT-02 | QIU-04 Buyers receive what was shown as in stock | KPI-03 Stock-out cancellation rate | yes |
| OUT-03 | QIU-03 Buyers are never charged twice or billed wrongly | KPI-02 Billing error rate | yes |
| OUT-01 | QIU-02 Repeat buyers finish a second purchase quickly | none | no: not counted overall (question 4) |

Hints for later stages: `context.yaml` holds four hints for stage 3: two of kind product, one of kind data and one of kind standard.

Decisions:

| # | Question | Answer | Items |
|---|---|---|---|
| 1 | Is "the phone checkout must be quick" about card payment only, or every payment method? | Card payment only. | QIU-01 |
| 2 | No one who operates the store is considered. Should marketing staff, who run campaigns, be considered as secondary users? | No: they do not use the checkout. | USR-05 |
| 3 | "We must never double-charge" reads as a business requirement. Should it become the outcome "decrease refund requests and billing inquiries from buyers"? | Yes: add it as an intermediate outcome under KGI-01. | OUT-03, QIU-03, KPI-02 |
| 4 | The input names no KPIs. Which of these does the business count overall: purchase completion, billing errors, stock-out cancellations, repeat-purchase time? | Purchase completion, billing errors and stock-out cancellations. Repeat-purchase time is not counted overall. | KPI-01, KPI-02, KPI-03, QIU-02 |

Question 1 was asked because narrowing a stated requirement to card payment would take protection away from the other payment methods (severity high). Questions 2 to 4 change a user, an outcome, or five or more items (breadth high).

Open assumptions:

| QAS | Title | Kind | Breadth | Severity | Items that depend on it |
|---|---|---|---|---|---|
| QAS-01 | SREs answer checkout alerts at any hour | judgment | low | low | QIU-05 |

QAS-01 was not asked: only QIU-05 depends on it, and it is easy to correct before stage 2 writes functions for that QIU.

Placeholders:

| Name | Stands for | Used in |
|---|---|---|
| T_repeat | Seconds within which a repeat purchase should finish | QIU-02 |

Open gaps (excerpt):

- QIU-05 and QIU-06: the store cannot see when SREs find the failing component, or whether a month closes on figures that match the payments (`observable: false`). Stage 2 decides whether a function should produce the data.

## Example items

`docs/quality/qiu/QIU-01.yaml`:

```yaml
id: QIU-01
title: Buyers finish card checkout on smartphones
status: active
basis: inferred
basis_note: Context of use confirmed in question 1; the measure is inferred
premises: []
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
```

`docs/quality/kpi/KPI-01.yaml`:

```yaml
id: KPI-01
title: Purchase completion rate
status: active
basis: inferred
basis_note: Counted overall by the business, confirmed in question 4; the formula is inferred
premises: []
outcome: OUT-01
numerator: Orders confirmed
denominator: Checkouts started
counted_over: All users and all contexts of use
overlaps_with: [QIU-01]
overlap_note: null
target: null
target_status: undecided
```

`docs/quality/qas/QAS-01.yaml`:

```yaml
id: QAS-01
title: SREs answer checkout alerts at any hour
status: open
stage: 1
kind: judgment
statement: SREs handle checkout failure alerts while on call at any hour, nights and weekends included.
why_unsure: The input gives the 15 minutes but not when SREs are on call.
breadth: low
severity: low
rating_note: One item depends on it; easy to correct before stage 2 writes functions for it
answer: null
answered_in: null
```

QIU-05, which depends on it, has `premises: [QAS-01]` and `basis: assumed`.
