# Stage 4 report template and worked example

## Contents
- Report template (`docs/quality/4-nfr-slo.md`)
- Worked example: online store
- Example items

Write the report in the working language. Translate the headings. Keep the IDs as they are, and write the basis marks and statuses with the glossary's terms for the working language.

## Report template

```markdown
# Non-functional requirements, SLIs and SLOs: <system name>

Status: draft | answers applied · Updated: <date> · Stage 4 of 4

## Summary
<Two or three sentences: SLOs per CUJ, other NFRs by kind, provisional targets, what needs agreement, proposals raised.>

## Inputs
| Source | What it gave |
|---|---|
<Include the operational facts: telemetry, traffic, baselines, existing SLOs or SLAs.>

## Allocation
| QR | Handling | Supporting function | NFRs | Kind | Out of scope |
|---|---|---|---|---|---|

## SLOs
| NFR | CUJ | SLI | Target (status) | Window | Error budget | Budget volume | Policy |
|---|---|---|---|---|---|---|---|

## SLI definitions
| SLI | Good ÷ valid | Method | Measured at | Blind spots |
|---|---|---|---|---|

## Error budget policy
| Condition | Action |
|---|---|

## Burn-rate alerts
| SLO | Notify | Long window | Short window | Burn rate | Budget spent at alert | Exhausted in |
|---|---|---|---|---|---|---|

## Deadline SLOs
<Only for batch and deadline SLOs, instead of burn-rate alerts.>
| SLO | Progress alert before the deadline | Budget share of one bad run | After a miss |
|---|---|---|---|

## Other non-functional requirements
<Emphasized QRs first.>
| NFR | Kind | From (handling) | Requirement | Where it is checked | What the numbers are based on |
|---|---|---|---|---|---|

## Traceability
| User and need | QIU | FR | QR | NFR / SLI |
|---|---|---|---|---|

## Function proposals for stage 2
| FRP | For | Function needed |
|---|---|---|

## Needs agreement
<One row per group of approvers, naming the items by ID. The values and what they are based on are in the tables above; do not repeat them here.>
| Approver candidates | Items | What to agree |
|---|---|---|

## Questions and answers
| # | Question | Drafted answer | Answer | What changed |
|---|---|---|---|---|

## Open gaps
## Out of scope
## Next
<If FRPs were raised: re-run `quality-in-use-to-functional-req`, then stages 3 and 4 for the affected items.>
```

## Worked example: online store

Operational facts given: about 300,000 checkouts a day; peak of 300 requests/s during sales; load balancer access logs and app events exist; load tests showed 99.4% of checkouts within 1.5 s at 300 requests/s.

Allocation (excerpt):

| QR | Handling | Supporting function | NFRs | Kind | Out of scope |
|---|---|---|---|---|---|
| QR-01 Availability of checkout | emphasized | none | NFR-01 | slo | |
| QR-02 Checkout latency | emphasized | none | NFR-02 | slo | |
| QR-03 Stock currentness | emphasized | FR-05 already exists | NFR-03 | slo | Registering incoming stock promptly is a warehouse procedure |
| QR-04 Correct totals, no double charge | emphasized | FR-03, FR-07 exist | NFR-04, NFR-08 | slo (reconciliation), monitor | |
| QR-06 Card data confidentiality | emphasized (security risk analysis) | none | NFR-05 | audit | |
| QR-07 Trace for every payment request | minimum-line | FR-06 exists | NFR-06 | gate | |
| QR-12 Payment provider calls | minimum-line | none | NFR-07 | monitor | |

SLOs:

| NFR | CUJ | SLI | Target (status) | Window | Error budget | Budget volume | Policy |
|---|---|---|---|---|---|---|---|
| NFR-01 | CUJ-01 | SLI-01 Successful checkouts ÷ valid checkouts | 99.9% (provisional) | 28-day rolling | 0.1% | 8,400 failed checkouts; 40 min of full outage | EBP-01 |
| NFR-02 | CUJ-01 | SLI-02 Checkouts within 1.5 s ÷ valid checkouts | 99% (provisional, from the load test) | 28-day rolling | 1% | 84,000 slow checkouts | EBP-01 |
| NFR-03 | CUJ-01 | SLI-03 Stock changes shown within 5 min ÷ stock changes | 99% (provisional) | 28-day rolling | 1% | depends on change volume (asked) | EBP-01 |
| NFR-04 | CUJ-01 | SLI-04 Orders whose charged amount matches the order total ÷ orders reconciled | 99.99% (provisional) | 28-day rolling | 0.01% | about 840 mismatched orders | EBP-01 |

Other non-functional requirements:

| NFR | Kind | From (handling) | Requirement | Where it is checked | What the numbers are based on |
|---|---|---|---|---|---|
| NFR-05 | audit | QR-06 (emphasized) | Critical vulnerabilities in the payment path are fixed within 7 days; card-data handling is audited every year | Vulnerability management; annual audit | Proposed; not in the inputs |
| NFR-08 | monitor | QR-04 (emphasized) | Any double charge found by the daily reconciliation notifies on-call and accounting the same morning. Target: zero. A zero target has no error budget, so it is a monitor, not an SLO | Daily reconciliation (FR-07) | Zero follows from "we must never double-charge" |
| NFR-06 | gate | QR-07 (minimum-line) | Every service on the checkout path logs the trace ID; 99% of payment requests have a complete trace | Definition of Done; trace coverage report | 99% proposed |
| NFR-07 | monitor | QR-12 (minimum-line) | The payment provider's success rate is watched; on-call is notified when it stays below 99% for an hour | Monitoring | Proposed; to be reset from a month of data |

Traceability (excerpt):

| User and need | QIU | FR | QR | NFR / SLI |
|---|---|---|---|---|
| Buyer: finish checkout on a phone without waiting | QIU-01 | FR-01 | QR-01, QR-02 | NFR-01 / SLI-01, NFR-02 / SLI-02 |
| Buyer: "in stock" means it arrives | QIU-04 | FR-04, FR-05 | QR-03 | NFR-03 / SLI-03 |
| Buyer: never charged twice | QIU-03 | FR-03, FR-07 | QR-04 | NFR-04 / SLI-04, NFR-08 |

Questions asked after the draft (excerpt):

| # | Question | Drafted answer | Answer | What changed |
|---|---|---|---|---|
| 1 | How many stock changes happen per day? It sets NFR-03's budget volume. | Unknown; left as a formula | About 50,000 | Volume: 14,000 stale changes per window |
| 2 | Should planned maintenance count against the availability budget? | No: excluded from valid events | Yes, it counts | SLI-01 exclusions changed; NFR-01 rationale updated |

## Example items

`docs/quality/nfr/NFR-02.yaml`:

```yaml
id: NFR-02
title: Checkout latency SLO
status: draft
version: 1
basis: inferred
basis_note: The 1.5 s threshold and the 300 requests/s peak are stated in QR-02; the target is proposed
needs_agreement: true
approver_candidates: [Product owner, SRE lead]
from: [QR-02]
kind: slo
cuj: CUJ-01
statement: Over a rolling 28 days, 99% of valid checkout requests respond within 1.5 s.
conditions:
  workload: Up to 300 requests/s (sale peak)
  users: Members; bots and load tests excluded
  transactions: All payment methods
slo:
  sli: SLI-02
  target: 99%
  target_status: provisional
  target_rationale: Load test showed 99.4% within 1.5 s at 300 requests/s; set slightly below it. The 1.5 s threshold comes from QR-02 (PRD). Not derived from KPIs
  window: 28-day rolling
  error_budget: Up to 1% of valid checkouts may take longer than 1.5 s
  error_budget_volume: About 84,000 slow checkouts per window at 300,000 checkouts a day
  policy: EBP-01
  alerts:
    - {notify: page, long_window: 1h, short_window: 5m, burn_rate: 14.4, budget_spent_at_alert: 2.1%, condition: null}
    - {notify: page, long_window: 6h, short_window: 30m, burn_rate: 6, budget_spent_at_alert: 5.4%, condition: null}
    - {notify: ticket, long_window: 3d, short_window: 6h, burn_rate: 1, budget_spent_at_alert: 10.7%, condition: null}
gate: null
audit: null
monitor: null
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 4 draft}
```

`docs/quality/nfr/NFR-05.yaml`:

```yaml
id: NFR-05
title: Card data protection through audits and fix deadlines
status: draft
version: 1
basis: inferred
basis_note: The 7-day fix deadline and the yearly audit are proposed values, not from the inputs
needs_agreement: true
approver_candidates: [Security lead, Product owner]
from: [QR-06]
kind: audit
cuj: CUJ-01
statement: Critical vulnerabilities in the payment path are fixed within 7 days, and card-data handling is audited every year.
conditions:
  workload: null
  users: null
  transactions: Card payments
slo: null
gate: null
audit:
  mechanism: Vulnerability management and an annual audit of card-data handling
  criterion: No critical vulnerability open for more than 7 days; no audit finding left without an owner
  cadence_or_deadline: Fix within 7 days; audit every year
monitor: null
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 4 draft}
```

`docs/quality/ebp/EBP-01.yaml`:

```yaml
id: EBP-01
title: Checkout error budget policy
status: draft
version: 1
basis: inferred
basis_note: null
needs_agreement: true
approver_candidates: [Product owner, Engineering lead, SRE lead]
applies_to: [NFR-01, NFR-02, NFR-03, NFR-04]
rules:
  - {when: Remaining budget falls below 50%, action: The next sprint puts reliability work first}
  - {when: One incident consumes 20% or more of the budget, action: A postmortem is required and at least one root-cause fix goes first}
  - {when: The budget is exhausted, action: For the rest of the window release only fixes and security patches}
  - {when: 50% or more remains at the end of every window, action: Tighten the SLO or spend the surplus on release speed and experiments}
  - {when: The stock freshness budget is exhausted, action: Ticket both the fix of the cause and the correction of stock data already shown wrongly}
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 4 draft}
```
