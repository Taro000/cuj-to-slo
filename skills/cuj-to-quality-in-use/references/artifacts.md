# Artifacts

## Contents
- Layout
- Common fields
- context.yaml (stage 1)
- QIU: quality-in-use requirement (stage 1)
- KPI: means KPI (stage 1)
- FR: functional requirement (stage 2)
- FRP: function proposal (stages 3 and 4 propose, stage 2 resolves)
- QR: product or data quality requirement (stage 3)
- NFR: non-functional requirement (stage 4)
- SLI: SLI definition (stage 4)
- EBP: error budget policy (stage 4)

## Layout

All stages write under `docs/quality/`, unless the project already keeps requirements somewhere else. In that case, use that location and say so in the report.

```
docs/quality/
├── 1-quality-in-use.md            stage 1 report
├── 2-functional-requirements.md   stage 2 report
├── 3-product-quality.md           stage 3 report
├── 4-nfr-slo.md                   stage 4 report
├── context.yaml                   stage 1: system, KGIs, outcomes, users, CUJs
├── qiu/QIU-01.yaml …              stage 1: quality-in-use requirements
├── kpi/KPI-01.yaml …              stage 1: means KPIs
├── fr/FR-01.yaml …                stage 2: functional requirements
├── frp/FRP-01.yaml …              stages 3–4 propose, stage 2 resolves
├── qr/QR-01.yaml …                stage 3: product and data quality requirements
├── nfr/NFR-01.yaml …              stage 4: non-functional requirements, SLOs included
├── sli/SLI-01.yaml …              stage 4: SLI definitions
└── ebp/EBP-01.yaml …              stage 4: error budget policies
```

- One requirement per file. The file name is the ID: `qr/QR-07.yaml`.
- IDs are the prefix plus a zero-padded number (`QR-07`, then `QR-100` when needed). Never reuse or renumber an ID.
- The YAML items are the source of truth, and the report explains them. They are written once the answers are applied; until then the draft report is the working copy. If the two disagree, ask which one is right; do not silently pick one.
- `context.yaml` holds the inputs that the other items refer to (system, KGIs, outcomes, users, CUJs), and what stage 1 found for later stages (hints and operational facts). It is the only file that holds more than one entry.
- Keep the YAML valid. Quote a free-text value when it contains `: ` or ` #`, or starts with one of `[ { * & ! | > ' " % @` or a backtick.

## Common fields

Every item file except `context.yaml` has these fields. The common fields come first and `changelog` comes last:

```yaml
id: QR-07
title: Short name, in the working language
status: draft              # draft | agreed | retired   (FRP: proposed | accepted | rejected; a rejection for cost needs agreement)
version: 1
basis: inferred            # stated | inferred | confirmed: the weakest basis of the judgments in the item
basis_note: null           # which parts are stated, inferred or confirmed, when they differ
needs_agreement: false     # true for priorities, targets and thresholds, exclusions and policies
approver_candidates: []    # roles, e.g. [Product owner, SRE lead]
# ... the fields of the item type ...
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 3 draft}
```

`characteristic` fields (QIU, QR) take the English names in `glossary.md`, written as `characteristic > sub-characteristic` when the characteristic has sub-characteristics: `effectiveness`, `satisfaction > trust`, `performance efficiency > time behaviour`, `accuracy`. Write the working-language names in the reports only.

## context.yaml (stage 1)

```yaml
system:
  name: Online store
  boundary: Storefront (web and app), cart, checkout, order history. Excludes the warehouse system.
  purpose: Sell products to consumers online
  constraints: [Card payments go through an external payment provider]
  data_producers: [Warehouse system (stock counts)]   # systems that create data the users rely on; people go in users
  basis: stated
kgis:
  - id: KGI-01
    name: Revenue
    measure: Monthly gross sales
    target: +10% year over year      # set by the business; null when the input gives none
    basis: stated
outcomes:
  - id: OUT-01
    statement: Increase the number of buyers who complete a purchase
    verb: increase                   # maintain | eliminate | decrease | increase | create
    kgis: [KGI-01]
    basis: stated
users:
  - id: USR-01
    name: Buyer
    category: primary                # primary | secondary-operation | secondary-maintenance | indirect
    data_role: consumer              # producer | consumer | both | none
    considered: true
    exclusion_reason: null           # required when considered is false
    basis: stated
cujs:
  - id: CUJ-01
    name: Purchase
    users: [USR-01]
    user_goal: Buy the items they want, with the right quantity and delivery address
    steps: [Cart, Payment, Order confirmation]    # [] when the input gives no steps; never write your own
    outcomes: [OUT-01]
    basis: stated
    basis_note: null
hints:                               # for stage 3: statements whose subject is the product or the data, and requirements that do not come from users
  - statement: Flag addresses that do not exist before the order is confirmed
    kind: product                    # product | data | law | standard | security-risk | policy
    source: PRD, section 3
  - statement: Card data must be handled under the card industry's security standard
    kind: standard
    source: Security team memo
operational_facts:                   # for stage 4: telemetry, volumes, peaks, existing SLOs or SLAs, baselines
  - fact: About 300,000 checkouts a day
    source: PRD, section 1
  - fact: Load balancer access logs and app events exist
    source: Architecture notes
```

## QIU: quality-in-use requirement (stage 1), `qiu/QIU-01.yaml`

```yaml
# common fields ...
user: USR-01
cuj: CUJ-01
outcomes: [OUT-01]
need: Finish checkout without being kept waiting, even at busy times
context_of_use:                       # what is held fixed when counting
  device: Smartphone
  network: Any
  user_type: Signed-in buyer
  task: Card payment
  environment: null
  other: null                         # any other condition held fixed, such as tenant size or data volume
characteristic: effectiveness         # an English name from glossary.md, e.g. "satisfaction > trust"
statement: Buyers on smartphones complete card checkout with the right items, quantity and address.
failure_scenario: Buyers give up during payment and no order is placed.
measure:
  numerator: Orders confirmed
  denominator: Checkouts started
  narrowed_to: Smartphone, card payment, signed-in buyers; test accounts excluded
  denominator_needed_only: Only buyers who started checkout are counted
  contribution: Event sits right after the checkout flow; A/B testing is possible
  observable: true                    # false when the product cannot see the event yet; say why in the report's open gaps
  compared_with: null                 # context coverage only: the QIU whose formula this one repeats in another context
  scale: ratio                        # nominal | ordinal | interval | ratio
kpi: KPI-01                           # the same formula counted overall; null when there is none
target: null                          # never derived from the KGI target
target_status: undecided              # undecided | provisional | agreed
```

## KPI: means KPI (stage 1), `kpi/KPI-01.yaml`

```yaml
# common fields ...
outcome: OUT-01
numerator: Orders confirmed
denominator: Checkouts started
counted_over: All users and all contexts of use
overlaps_with: [QIU-01]               # [] when the formula has no quality-in-use counterpart
overlap_note: null                    # required when overlaps_with is empty: why there is no overlap
target: null
target_status: undecided
```

## FR: functional requirement (stage 2), `fr/FR-01.yaml`

```yaml
# common fields ...
statement: The system attaches a unique key to every payment request and does not accept a second payment for the same order.
origin: quality-in-use                # cuj-step | quality-in-use | proposal
cuj: CUJ-01                           # the CUJ whose step the function acts in; traces_to may name QIUs of other CUJs
cuj_step: Payment                     # one of the CUJ's steps, written exactly as in context.yaml
traces_to: [QIU-03]                   # QIU and/or KPI IDs
proposal: null                        # FRP ID when origin is proposal
existing: false                       # true when the inputs show the function already exists in the product
extends: null                         # FR ID of the function this FR adds to
interface: [POST /payments]           # names from the inputs, or only the kind ("screen: approver web")
acceptance_criteria:                  # observable behaviour only; no "how well"
  - Given an order that is already paid, when a payment request with the same key arrives, then the system returns the first result and charges nothing.
quality_hints:                        # "how well" aspects left for stage 3: "<characteristic> (<QIU>) - <what>"
  - Economic risk mitigation (QIU-03) - no double charge
```

## FRP: function proposal (stages 3 and 4), `frp/FRP-01.yaml`

```yaml
id: FRP-01
title: Switch to a secondary payment provider
status: proposed                      # proposed | accepted | rejected
version: 1
basis: inferred
basis_note: null
needs_agreement: false
approver_candidates: []
proposed_by: functional-requirements-to-product-quality   # or product-quality-to-slo
origin: [QR-08]                       # the quality requirements that need the function
changes: null                         # FR ID when the proposal changes an existing FR instead of adding one
reason: Checkout must continue when the payment provider fails; no FR switches providers.
suggested_statement: The system routes payments to a secondary provider while the primary provider is failing.
affects: [NFR-01]                     # later-stage items that depend on the decision
placeholders: []                      # thresholds the function implies, named as in conventions.md
resolution: null                      # written by stage 2 in the working language: accepted as which FR, or why not; read status for the decision
changelog:
  - {version: 1, date: 2026-01-31, change: Proposed, reason: Stage 3 fault tolerance analysis}
```

## QR: product or data quality requirement (stage 3), `qr/QR-01.yaml`

```yaml
# common fields ...  (needs_agreement: true, because the handling is a priority decision)
model: product                        # product | data
layer: external                       # external | internal | data-inherent | data-system-dependent
characteristic: performance efficiency > time behaviour
statement: The system responds to order confirmation fast enough that buyers are not kept waiting, up to sale-peak load.
applies_to: [FR-01]                   # the functions this quality is about
traces_to: [QIU-01, QIU-02]           # QIU IDs; empty only when entry_point is set
entry_point: null                     # {type: law | standard | security-risk | policy, source: "...", brought_by: "..."}
stakeholders: [USR-01]                # for data quality, include the data producers
failure_scenario: Slow payment makes buyers abandon checkout.
score: {impact: 4, likelihood: 3}     # 1–5 each; the score is impact × likelihood; null for internal QRs
handling: emphasized                  # emphasized | minimum-line | excluded (this cycle)
handling_reason: null                 # required for excluded, and for entry points (why they are kept)
implies_internal: []                  # internal QR IDs that support this external QR
conflicts:
  - {with: QR-13, note: Fraud checks add latency; part of the response time is reserved for them, sized in stage 4}
stated_numbers: ["1.5 s at 300 requests/s (PRD)"]   # numbers given in the inputs, with their source, for stage 4; never invent
placeholders: []                      # named placeholders from earlier stages, e.g. [{name: T_repeat, meaning: seconds for a repeat purchase, from: QIU-02}]
```

## NFR: non-functional requirement (stage 4), `nfr/NFR-01.yaml`

```yaml
# common fields ...  (targets and policies need agreement)
from: [QR-02]
kind: slo                             # slo | gate | audit | monitor
cuj: CUJ-01                           # null when the NFR applies to every CUJ (a law, a policy)
statement: Over a rolling 28 days, 99% of valid checkout requests respond within 1.5 s.
conditions:                           # the operating conditions the requirement holds under
  workload: Up to 300 requests/s (sale peak)
  users: Members; bots and load tests excluded
  transactions: All payment methods
slo:                                  # kind: slo
  sli: SLI-02
  target: 99%
  target_status: provisional          # provisional | agreed
  target_rationale: First value set conservatively from load-test results; not derived from KPIs   # also what the SLI's thresholds are based on
  window: 28-day rolling
  error_budget: Up to 1% of valid checkouts may take longer than 1.5 s
  error_budget_volume: About 84,000 slow checkouts per window at 300,000 checkouts a day
  policy: EBP-01
  alerts:
    - {notify: page, long_window: 1h, short_window: 5m, burn_rate: 14.4, budget_spent_at_alert: 2.1%, condition: null}
    - {notify: page, long_window: 6h, short_window: 30m, burn_rate: 6, budget_spent_at_alert: 5.4%, condition: null}
    - {notify: ticket, long_window: 3d, short_window: 6h, burn_rate: 1, budget_spent_at_alert: 10.7%, condition: null}
  # batch or deadline SLO: instead of alerts, {bad_run_budget_share: "30%", progress_alert: "monitor NFR-xx at 5:30", after_miss: ticket}
gate: null                            # kind: gate  → {where: CI | release-check | definition-of-done, check: "...", threshold: "..."}
audit: null                           # kind: audit → {mechanism: "...", criterion: "...", cadence_or_deadline: "..."}
monitor: null                         # kind: monitor → {signal: "...", threshold: "...", notifies: "..."}
```

## SLI: SLI definition (stage 4), `sli/SLI-01.yaml`

Fields marked `# required` must be filled before the SLI is used.

```yaml
id: SLI-02
title: Checkout latency
status: draft
version: 1
basis: inferred
basis_note: null
needs_agreement: true                 # the exclusions and thresholds need agreement
approver_candidates: [SRE lead, Product owner]
function: GOOD ÷ VALID                # required
refs: [QR-02, NFR-02]                 # required: the requirements this SLI serves
reshaped: Average response time replaced with "share of requests within 1.5 s"
target_entity: Production behaviour of the system that handles POST /checkout   # required
purpose: How often buyers wait at checkout
unit_event: One request accepted by the load balancer
method: LB log method (load balancer access logs, aggregated every minute)        # name it; never mix methods
elements:
  VALID:
    definition: Checkout requests from authenticated users that did not return 5xx (failures belong to the availability SLI)   # required
    exclusions: Health checks, synthetic monitoring, load tests, planned maintenance windows
    inputs: Load balancer access log fields request_id, status, elapsed, user_agent   # required
    unit: requests
    counting_rule: Summed per minute; retries count once per request_id               # required
  GOOD:
    definition: VALID requests whose elapsed time is at most 1.5 s, measured from the load balancer receiving the request to starting the response   # required
    inputs: Same as VALID
    unit: requests
    counting_rule: Same as VALID
scale: ratio                          # required
applicability: Production, Tokyo region, normal traffic. Synthetic checks are a separate SLI
lifecycle: Measured in production. Load-test values are estimates and stay separate
blind_spots: Client-side network drops are invisible. Assumes log loss below 0.1%
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 4 draft}
```

## EBP: error budget policy (stage 4), `ebp/EBP-01.yaml`

```yaml
# common fields ...  (needs_agreement: true; approvers from both business and development)
applies_to: [NFR-01, NFR-02]
rules:
  - {when: Remaining budget falls below 50%, action: The next sprint puts reliability work first}
  - {when: One incident consumes 20% or more of the budget, action: A postmortem is required and at least one root-cause fix goes first}
  - {when: The budget is exhausted, action: For the rest of the window, release only fixes and security patches}
  - {when: 50% or more remains at the end of every window, action: Tighten the SLO, or spend the surplus on release speed and experiments}
  - {when: A data SLO's budget is exhausted, action: Ticket both the fix of the cause and the correction of bad data already stored}
```
