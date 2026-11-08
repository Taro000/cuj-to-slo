# Stage 4 report template and worked example

## Contents
- Report template (`docs/quality/4-nfr-slo.md`)
- Worked example: online store
- Example items

Write the report in the working language. Translate the headings. Keep the IDs as they are, and write the basis marks, statuses and ratings with the glossary's terms for the working language.

The report indexes the items and never restates them: no statements, SLI definitions, targets, windows, budgets, alert thresholds, rationales or other values copied from the YAML. It holds the summary, what only the report records (allocation notes, open gaps, out-of-scope lines), the decisions, the open assumptions, the placeholders with the values proposed for them, and index tables of IDs, titles, enum values and references. Compute the budgets, volumes and alert thresholds with a script, and write them in the items.

## Report template

```markdown
# Non-functional requirements, SLIs and SLOs: <system name>

Status: draft | answers applied · Updated: <date> · Inputs as of: <commit, or "none"> · Stage 4 of 4

## Summary
<Two or three sentences: SLOs per CUJ, other NFRs by kind, how many targets are confirmed and how many provisional, the proposals raised, how many questions were asked, how many assumptions are open.>

## Inputs
| Source | What it gave |
|---|---|
<Documents and upstream artifacts, with the operational facts used and the shared facts this stage added, by ID (OPF-01 …). The user's answers are under "Decisions".>

## Allocation
| QR | Handling | Supporting function | NFRs | Kind | Out of scope |
|---|---|---|---|---|---|
<One row per emphasized and minimum-line QR: its ID and title, its handling, the FR or FRP IDs that support it, its NFR IDs and their kinds, and a few words when part of it is out of scope (the line itself is under "Out of scope").>

## SLOs
| NFR | CUJ | SLI | Target status | Policy |
|---|---|---|---|---|
<The targets, windows and budgets are in the NFRs.>

## SLI definitions
| SLI | Vantage | Existing source |
|---|---|---|
<Existing source: the OPF ID, or "none".>

## Error budget policies
| EBP | Applies to |
|---|---|

## Alerts
| NFR | Alerting | Rules |
|---|---|---|
<Alerting: burn-rate, or deadline progress for batch and deadline SLOs. Rules: how many. The windows and thresholds are in the NFR.>

## Other non-functional requirements
<Emphasized QRs first.>
| NFR | Kind | From (handling) |
|---|---|---|

## Traceability
| User | QIU | FR | QR | NFR / SLI |
|---|---|---|---|---|

## Function proposals for stage 2
| FRP | For |
|---|---|
<The proposals this stage raised. For: the QRs in the proposal's `origin`.>

## Decisions
| # | Question | Answer | Items |
|---|---|---|---|
<The questions asked and the answers in effect, numbered per stage and continued on a re-run. An answer that overturns an earlier one replaces its row.>

## Open assumptions
| QAS | Title | Kind | Breadth | Severity | Items that depend on it |
|---|---|---|---|---|---|
<The open assumptions this stage wrote, and the open upstream ones its items depend on.>

## Placeholders
| Name | Stands for | Used in | Proposed value and basis | Written in |
|---|---|---|---|---|
<Every placeholder still open anywhere in the chain: in QIU measures, FR criteria, FRPs, QRs and this stage's items, the thresholds this stage named included. Proposed value and basis: the value, what it is based on, and the decision (#n) or QAS that adopts it; for a value that a law, a contract or someone else sets, no value, and who sets it. Written in: this stage's items that hold the value, and the earlier stages that write it into their items on their next run.>

## Open gaps
## Out of scope
## Next
<If FRPs were raised: re-run `quality-in-use-to-functional-requirements`, then stages 3 and 4 for the affected items. Name the earlier stages to re-run so that they write the placeholder values proposed here, and every stage whose items cite a shared fact this run added or corrected.>
```

## Worked example: online store

The operational facts in `context.yaml` are OPF-01 (about 300,000 checkouts a day), OPF-02 (load balancer access logs and application events already exist), OPF-03 (a sale peak of about 300 checkout requests per second), OPF-04 (a load test answered 99.4% of checkouts within 1.5 s at that peak) and OPF-05 (the SREs' incident records, which stage 2 added). The run added OPF-06 (about 50,000 stock changes a day) from the answer to question 1. The excerpts below are from the report; the values are in the items that follow.

Allocation (excerpt):

| QR | Handling | Supporting function | NFRs | Kind | Out of scope |
|---|---|---|---|---|---|
| QR-01 Orders can be confirmed whenever the store is open | emphasized | — | NFR-01 | slo | |
| QR-02 Order confirmation keeps buyers from waiting up to the sale peak | emphasized | FRP-03 | NFR-02 | slo | |
| QR-03 Stock shown follows the warehouse | emphasized | FR-05 | NFR-03 | slo | Prompt registration of incoming stock |
| QR-04 Order totals are right and charged once | emphasized | FR-03 | NFR-04, NFR-08 | slo, monitor | |
| QR-06 Card data confidentiality | emphasized | — | NFR-05 | audit | |
| QR-07 Payments can be followed from order to charge | minimum-line | FR-06 | NFR-06 | gate | |
| QR-12 Exchanges with the payment provider | minimum-line | — | NFR-07 | monitor | |

SLOs:

| NFR | CUJ | SLI | Target status | Policy |
|---|---|---|---|---|
| NFR-01 Checkout availability SLO | CUJ-01 | SLI-01 | provisional | EBP-01 |
| NFR-02 Checkout latency SLO | CUJ-01 | SLI-02 | provisional | EBP-01 |
| NFR-03 Stock freshness SLO | CUJ-01 | SLI-03 | provisional | EBP-01 |
| NFR-04 Order total reconciliation SLO | CUJ-01 | SLI-04 | provisional | EBP-01 |

SLI definitions:

| SLI | Vantage | Existing source |
|---|---|---|
| SLI-01 Checkout availability | system-boundary | OPF-02 |
| SLI-02 Checkout latency | system-boundary | OPF-02 |
| SLI-03 Stock freshness | data | none |
| SLI-04 Order total reconciliation | data | none |

Error budget policies:

| EBP | Applies to |
|---|---|
| EBP-01 Checkout error budget policy | NFR-01, NFR-02, NFR-03, NFR-04 |

Alerts (excerpt):

| NFR | Alerting | Rules |
|---|---|---|
| NFR-01 | burn-rate | 3 |
| NFR-02 | burn-rate | 3 |
| NFR-03 | burn-rate | 3 |

Other non-functional requirements (excerpt):

| NFR | Kind | From (handling) |
|---|---|---|
| NFR-05 Card data protection through audits and fix deadlines | audit | QR-06 (emphasized) |
| NFR-08 Double charges found by reconciliation | monitor | QR-04 (emphasized) |
| NFR-06 Payments followed from order to charge | gate | QR-07 (minimum-line) |
| NFR-07 Payment provider success rate | monitor | QR-12 (minimum-line) |

Traceability (excerpt):

| User | QIU | FR | QR | NFR / SLI |
|---|---|---|---|---|
| USR-01 | QIU-01 | FR-01 | QR-01, QR-02 | NFR-01 / SLI-01, NFR-02 / SLI-02 |
| USR-01 | QIU-04 | FR-04, FR-05 | QR-03 | NFR-03 / SLI-03 |
| USR-01 | QIU-03 | FR-01, FR-03 | QR-04 | NFR-04 / SLI-04, NFR-08 |

Function proposals for stage 2:

| FRP | For |
|---|---|
| FRP-03 Record when the order result is shown to the buyer | QR-02 |

Decisions (excerpt):

| # | Question | Answer | Items |
|---|---|---|---|
| 1 | How many stock changes happen per day? NFR-03's budget volume depends on it. | About 50,000 a day. | OPF-06, NFR-03 |
| 2 | Should planned maintenance count against the checkout availability budget? | Yes, it counts: buyers cannot check out during maintenance either. | SLI-01, NFR-01 |
| 3 | Do you adopt the drafted error budget policy EBP-01 for the four checkout SLOs? | Yes, as drafted. | EBP-01 |
| 4 | QR-06 comes from the card industry's security standard. Do the standard or your contracts set a deadline for fixing vulnerabilities, or a cadence for audits? If not, NFR-05 proposes 7 days for critical vulnerabilities and a yearly audit. | None that applies to us; use 7 days and a yearly audit. | NFR-05 |

Open assumptions (excerpt):

| QAS | Title | Kind | Breadth | Severity | Items that depend on it |
|---|---|---|---|---|---|
| QAS-09 | First threshold and target of stock freshness | decision | medium | medium | SLI-03, NFR-03 |
| QAS-12 | First value of T_repeat | decision | medium | low | QIU-02, FR-02, once stages 1 and 2 write the value |
| QAS-05 | The payment provider reports a result for every payment it receives (stage 3) | judgment | low | medium | QR-12, and NFR-07 through it |
| QAS-11 | First target of checkout availability | decision | low | medium | NFR-01 |
| QAS-08 | First target of checkout latency | decision | low | low | NFR-02 |
| QAS-10 | Alert threshold of the payment provider monitor | decision | low | low | NFR-07 |

Placeholders:

| Name | Stands for | Used in | Proposed value and basis | Written in |
|---|---|---|---|---|
| T_stock_lag | Longest time from a stock change reported by the warehouse system (EXT-02) to the stock shown to buyers reflecting it | QR-03, SLI-03, NFR-03 | 5 minutes: a first value, to be reset once a few windows of data exist (QAS-09) | SLI-03 and NFR-03; QR-03 by stage 3 on its next run |
| T_repeat | Seconds within which a repeat purchase should finish | QIU-02, FR-02 | 60 s: a first value for a purchase that needs no typing with the saved address and card (FR-02), to be reset from the first month of counts (QAS-12) | QIU-02 by stage 1 and FR-02 by stage 2, on their next runs |

Open gaps (excerpt):

- Waits on the buyer's side, such as a card issuer's authentication screen, are invisible to SLI-02. A user-side SLI for checkout waits on FRP-03.
- FRP-01 from stage 3 is still proposed; NFR-01 waits on it and is listed in its `affects`.

Out of scope (excerpt):

- Prompt registration of incoming stock (QR-03): a warehouse procedure, outside the software.

Next (excerpt):

- Re-run `quality-in-use-to-functional-requirements` to resolve FRP-01 and FRP-03, then stages 3 and 4 for the affected items.
- Re-run `cuj-to-quality-in-use` for QIU-02 and `quality-in-use-to-functional-requirements` for FR-02, so that they write the value proposed for T_repeat, and `functional-requirements-to-product-quality` for QR-03, so that it writes the value proposed for T_stock_lag.

## Example items

`docs/quality/nfr/NFR-02.yaml`:

```yaml
id: NFR-02
title: Checkout latency SLO
status: active
basis: assumed
basis_note: The 1.5 s threshold is stated in QR-02 and the sale peak in OPF-03; the 99% target is a provisional decision (QAS-08)
premises: [OPF-01, OPF-03, OPF-04, QAS-08]
from: [QR-02]
kind: slo
cuj: CUJ-01
statement: Over a rolling 28 days, 99% of valid checkouts are answered within 1.5 s.
conditions:
  workload: Up to the sale peak (OPF-03)
  users: Members; bots and load tests excluded
  transactions: All payment methods
slo:
  sli: SLI-02
  target: 99%
  target_status: provisional
  target_rationale: Set slightly below the load-test result (OPF-04); the 1.5 s threshold comes from QR-02. Not derived from KPIs
  window: 28-day rolling
  error_budget: Up to 1% of valid checkouts may take longer than 1.5 s
  error_budget_volume: About 84,000 slow checkouts per window at the volume in OPF-01
  policy: EBP-01
  alerts:
    - {notify: page, long_window: 1h, short_window: 5m, burn_rate: 14.4, budget_spent_at_alert: 2.1%, condition: null}
    - {notify: page, long_window: 6h, short_window: 30m, burn_rate: 6, budget_spent_at_alert: 5.4%, condition: null}
    - {notify: ticket, long_window: 3d, short_window: 6h, burn_rate: 1, budget_spent_at_alert: 10.7%, condition: null}
gate: null
audit: null
monitor: null
```

`docs/quality/nfr/NFR-05.yaml`:

```yaml
id: NFR-05
title: Card data protection through audits and fix deadlines
status: active
basis: inferred
basis_note: The 7-day fix deadline and the yearly audit were confirmed in question 4; making this an audit rather than an SLO is inferred
premises: [CON-01]
from: [QR-06]
kind: audit
cuj: CUJ-01
statement: Critical vulnerabilities that affect payment are fixed within 7 days, and the handling of card payments is audited every year.
conditions:
  workload: null
  users: null
  transactions: Card payments
slo: null
gate: null
audit:
  mechanism: Vulnerability management and a yearly audit of how card payments are handled
  criterion: No critical vulnerability that affects payment stays open for more than 7 days; no audit finding is left without an owner
  cadence_or_deadline: Fix within 7 days; audit every year
monitor: null
```

`docs/quality/ebp/EBP-01.yaml`:

```yaml
id: EBP-01
title: Checkout error budget policy
status: active
basis: confirmed
basis_note: Adopted as drafted in question 3
premises: []
applies_to: [NFR-01, NFR-02, NFR-03, NFR-04]
rules:
  - {when: Remaining budget falls below 50%, action: The next sprint puts reliability work first}
  - {when: One incident consumes 20% or more of the budget, action: A postmortem is required and at least one root-cause fix goes first}
  - {when: The budget is exhausted, action: "For the rest of the window, release only fixes and security patches"}
  - {when: 50% or more remains at the end of every window, action: "Tighten the SLO, or spend the surplus on release speed and experiments"}
  - {when: The stock freshness budget is exhausted, action: Ticket both the fix of the cause and the correction of stock data already shown wrongly}
```

`docs/quality/qas/QAS-10.yaml`:

```yaml
id: QAS-10
title: Alert threshold of the payment provider monitor
status: open
stage: 4
kind: decision
statement: Notify on-call when the payment provider's success rate stays below 99% for an hour.
why_unsure: The inputs give no success rate for the payment provider; 99% for an hour is a first value, to be reset from a month of data.
breadth: low
severity: low
rating_note: One item depends on it, a monitor for a minimum line; a wrong value is easy to change
answer: null
answered_in: null
```
