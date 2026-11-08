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
- QAS: assumption (every stage)

## Layout

All stages write under `docs/quality/`, unless the project already keeps requirements somewhere else. In that case, use that location and say so in the report.

```
docs/quality/
├── 1-quality-in-use.md            stage 1 report
├── 2-functional-requirements.md   stage 2 report
├── 3-product-quality.md           stage 3 report
├── 4-nfr-slo.md                   stage 4 report
├── context.yaml                   stage 1: system, shared facts, KGIs, outcomes, users, CUJs
├── qiu/QIU-01.yaml …              stage 1: quality-in-use requirements
├── kpi/KPI-01.yaml …              stage 1: means KPIs
├── fr/FR-01.yaml …                stage 2: functional requirements
├── frp/FRP-01.yaml …              stages 3–4 propose, stage 2 resolves
├── qr/QR-01.yaml …                stage 3: product and data quality requirements
├── nfr/NFR-01.yaml …              stage 4: non-functional requirements, SLOs included
├── sli/SLI-01.yaml …              stage 4: SLI definitions
├── ebp/EBP-01.yaml …              stage 4: error budget policies
└── qas/QAS-01.yaml …              every stage: assumptions made without asking
```

- One item per file. The file name is the ID: `qr/QR-07.yaml`.
- IDs are the prefix plus a zero-padded number (`QR-07`, then `QR-100` when needed). Never reuse or renumber an ID.
- The YAML items are the source of truth. The report summarizes and indexes them and never restates what an item says; it holds what only the report records (reasons for leaving something out, coverage checks, decisions, open gaps). The items are written while the stage drafts, and answers change them in place. If the report and an item disagree, ask which one is right; do not silently pick one.
- `context.yaml` holds the inputs that the other items refer to (system, shared facts, KGIs, outcomes, users, CUJs), and what stage 1 found for later stages (hints). It is the only file that holds more than one entry. Stage 1 writes it; later stages only add or correct shared facts (`conventions.md`, "Shared facts"). Entries are never deleted and their numbers never reused: an entry of any list that no longer holds gets `status: retired`; an entry without `status` is active.
- Items hold the current state only: no changelog, no version, no dates of changes. Git keeps the history (`conventions.md`, "Current state only").
- Keep the YAML valid. Quote a free-text value when it contains `: ` or ` #`, or starts with one of `[ { * & ! | > ' " % @` or a backtick. Cite decisions and questions without `#` ("decision 12", "stage 3, decision 4"): an unquoted ` #` starts a comment and silently cuts off the rest of the value. Inside `[ ]` or `{ }`, also quote a value that contains a comma or `: `, or the value is silently split.

## Common fields

Every item file except `context.yaml` and the assumption files (QAS, which have their own fields) has these fields, first:

```yaml
id: QR-07
title: Short name, in the working language
status: active             # active | retired   (FRP: proposed | accepted | rejected; QAS: open | confirmed | overturned | retired)
basis: inferred            # stated | inferred | assumed | confirmed: the weakest basis of the judgments in the item
basis_note: null           # which parts are stated, inferred, assumed or confirmed, when they differ, and what the design must confirm
premises: []               # the shared facts (CON, EXT, OPF) and assumptions (QAS) this item depends on
# ... the fields of the item type ...
```

An item whose `premises` include an open QAS has `basis: assumed`. `characteristic` fields (QIU, QR) take the English names in `glossary.md`, written as `characteristic > sub-characteristic` when the characteristic has sub-characteristics: `effectiveness`, `satisfaction > trust`, `performance efficiency > time behaviour`, `accuracy`. Write the working-language names in the reports only.

## context.yaml (stage 1)

```yaml
system:
  name: Online store
  boundary: Storefront where buyers browse and buy, cart, checkout, order history. Excludes the warehouse system.
  purpose: Sell products to consumers online
  basis: stated
constraints:                         # requirement-level constraints that items refer to by ID
  - id: CON-01
    statement: The store never handles card numbers itself; card payments are taken by the payment provider (EXT-01)
    source: PRD, section 2
    basis: stated
external_systems:                    # systems outside the boundary, named by their role
  - id: EXT-01
    role: Payment provider (takes card payments and refunds)
    creates_data: true               # true when it creates data the users rely on; people who create data go in users
    source: PRD, section 2
    basis: stated
  - id: EXT-02
    role: Warehouse system (stock counts)
    creates_data: true
    source: PRD, section 4
    basis: stated
design_inputs:                       # design decisions stated in the input, for the design chain; no item refers to them
  - id: DIN-01
    statement: The storefront stays a web app this year; no native app
    source: Kickoff notes
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
operational_facts:                   # for stage 4 and every item that relies on them: volumes, peaks, when the product is used, existing telemetry, baselines, existing SLOs or SLAs
  - id: OPF-01
    fact: About 300,000 checkouts a day
    source: PRD, section 1
    basis: stated
  - id: OPF-02
    fact: Load balancer access logs and application events already exist
    source: Architecture notes
    basis: stated
  - id: OPF-03
    fact: Sale peak of about 300 checkout requests per second
    source: PRD, section 1
    basis: stated
  - id: OPF-04
    fact: Load test at the sale peak - 99.4% of checkouts answered within 1.5 s
    source: Load test report
    basis: stated
```

## QIU: quality-in-use requirement (stage 1), `qiu/QIU-01.yaml`

```yaml
# common fields ...
user: USR-01
cuj: CUJ-01
outcomes: [OUT-01]
need: Finish checkout without being kept waiting, even at busy times
context_of_use:                       # what is held fixed when counting; kinds of device and situations, never platforms or versions
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
  observable: true                    # true when the events happen in the product, so that a function can count them (name the FR, or the open FRP, in basis_note); false when they happen outside it; say why in the report's open gaps
  compared_with: null                 # context coverage only: the QIU whose formula this one repeats in another context
  scale: ratio                        # nominal | ordinal | interval | ratio
kpi: KPI-01                           # the same formula counted overall; null when there is none
target: null                          # never derived from the KGI target
target_status: undecided              # undecided | provisional | confirmed
```

## KPI: means KPI (stage 1), `kpi/KPI-01.yaml`

```yaml
# common fields ...
outcome: OUT-01
numerator: Orders confirmed
denominator: Checkouts started
counted_over: All users and all contexts of use
observable: true                      # true when the events happen in the product; false when they happen outside it
overlaps_with: [QIU-01]               # [] when the formula has no quality-in-use counterpart
overlap_note: null                    # required when overlaps_with is empty: why there is no overlap
target: null
target_status: undecided              # undecided | provisional | confirmed
```

## FR: functional requirement (stage 2), `fr/FR-01.yaml`

```yaml
# common fields ...
statement: The system charges an order at most once, even when the payment request for it is sent again.
origin: quality-in-use                # cuj-step | quality-in-use | proposal
cuj: CUJ-01                           # the CUJ whose step the function acts in; traces_to may name QIUs of other CUJs; null when the function acts across CUJs
cuj_step: Payment                     # one of the CUJ's steps, written exactly as in context.yaml; null when the CUJ has no steps or the function acts on no single step
traces_to: [QIU-03]                   # QIU and/or KPI IDs
proposal: null                        # FRP ID when origin is proposal
existing: false                       # true when the inputs show the function already exists in the product
extends: null                         # FR ID of the function this FR adds to
interface: ["screen: buyer checkout", EXT-01]   # who or what the function exchanges with, by kind: screen or notification (for whom), file or report (for whom), an external system (EXT ID); never paths or endpoints, except those of an interface that already exists in a running product
acceptance_criteria:                  # behaviour observable from outside the system; no "how well", no mechanisms
  - Given an order that is already paid, when a payment request for the same order arrives again, then the system returns the first result and charges nothing.
quality_hints:                        # "how well" aspects left for stage 3: "<characteristic> (<QIU>) - <what>", with the characteristic named in the working language (glossary.md)
  - Economic risk mitigation (QIU-03) - no double charge
```

## FRP: function proposal (stages 3 and 4), `frp/FRP-01.yaml`

```yaml
id: FRP-01
title: Keep checkout working when the payment provider fails
status: proposed                      # proposed | accepted | rejected
basis: inferred
basis_note: null
premises: [EXT-01]
proposed_by: functional-requirements-to-product-quality   # or product-quality-to-slo
origin: [QR-08]                       # the quality requirements that need the function; for a change that an answer brought, the decision ("stage 4, decision 21")
changes: null                         # FR ID when the proposal changes an existing FR instead of adding one
reason: Checkout must continue when the payment provider fails; no FR covers that case.
suggested_statement: The system keeps confirming orders while the payment provider is failing.
affects: [NFR-01]                     # later-stage items that depend on the decision
placeholders: []                      # thresholds the function implies, named as in conventions.md
resolution: null                      # written by stage 2 in the working language: accepted as which FR, or why not; read status for the decision
```

## QR: product or data quality requirement (stage 3), `qr/QR-01.yaml`

```yaml
# common fields ...  (premises: [OPF-03] here, for the sale peak)
model: product                        # product | data
layer: external                       # external | internal | data-inherent | data-system-dependent
characteristic: performance efficiency > time behaviour
statement: The system responds to order confirmation fast enough that buyers are not kept waiting, up to sale-peak load.
applies_to: [FR-01]                   # the functions this quality is about
traces_to: [QIU-01, QIU-02]           # QIU IDs; empty only when entry_point is set. A QR from a law or standard that also protects a QIU has both
entry_point: null                     # {type: law | standard | security-risk | policy, source: "...", brought_by: "..."}
stakeholders: [USR-01]                # for data quality, include the data producers (users or EXT IDs)
failure_scenario: Slow payment makes buyers abandon checkout.
score: {impact: 4, likelihood: 3}     # 1–5 each; the score is impact × likelihood; null for internal QRs
handling: emphasized                  # emphasized | minimum-line | excluded (this cycle)
handling_reason: null                 # required for excluded, and for entry points (why they are kept)
implies_internal: []                  # internal QR IDs that support this external QR
conflicts:
  - {with: QR-08, note: "Riding out a failing payment provider adds waiting time; part of the response time is reserved for it, sized in stage 4"}
stated_numbers: ["1.5 s at the sale peak, OPF-03 (PRD)"]   # numbers given in the inputs, with their source, for stage 4; never invent; cite shared facts by ID
placeholders: []                      # named placeholders from earlier stages, e.g. [{name: T_repeat, meaning: seconds for a repeat purchase, from: QIU-02}]
```

## NFR: non-functional requirement (stage 4), `nfr/NFR-02.yaml`

```yaml
# common fields ...  (premises: [OPF-01, OPF-03, OPF-04, QAS-08] here: the daily volume behind the budget, the peak, the load-test baseline, and the provisional target adopted without asking; so basis: assumed)
from: [QR-02]
kind: slo                             # slo | gate | audit | monitor
cuj: CUJ-01                           # null when the NFR applies to every CUJ (a law, a policy)
statement: Over a rolling 28 days, 99% of valid checkouts are answered within 1.5 s.
conditions:                           # the operating conditions the requirement holds under; refer to shared facts by ID
  workload: Up to the sale peak (OPF-03)
  users: Members; bots and load tests excluded
  transactions: All payment methods
slo:                                  # kind: slo
  sli: SLI-02
  target: 99%
  target_status: provisional          # provisional | confirmed
  target_rationale: Set slightly below the load test (OPF-04) and adopted provisionally (QAS-08); not derived from KPIs   # also what the SLI's thresholds are based on
  window: 28-day rolling
  error_budget: Up to 1% of valid checkouts may take longer than 1.5 s
  error_budget_volume: About 84,000 slow checkouts per window at the volume in OPF-01
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

The SLI is defined here in the terms of the domain: which events count, under which conditions, observed from where. The design decides where the events come from, which fields carry them and how they are collected. Fields marked `# required` must be filled before the SLI is used.

```yaml
id: SLI-02
title: Checkout latency
status: active
basis: inferred
basis_note: null
premises: [OPF-02]
function: GOOD ÷ VALID                # required
refs: [QR-02, NFR-02]                 # required: the requirements this SLI serves
reshaped: Average response time replaced with "share of checkouts answered within 1.5 s"
target_entity: The checkout as the system handles it, from receiving the order to answering it   # required
purpose: How often buyers wait at checkout   # required
unit_event: One checkout submitted by a buyer    # required: an event of the domain, not a log line
vantage: system-boundary              # required: user-side | system-boundary | synthetic | data
vantage_reason: The time the system takes is what the team can change; the time on the buyer's device is a separate SLI   # required
existing_source: OPF-02               # only when the input names telemetry that already exists; otherwise null, and the design provides the source
elements:
  VALID:
    definition: Checkouts submitted by signed-in buyers that the system did not fail (failures belong to the availability SLI)   # required
    exclusions: Health checks, synthetic monitoring, load tests
    unit: checkouts                   # required
    counting_rule: A checkout the buyer submits again counts once   # required
  GOOD:
    definition: VALID checkouts answered within 1.5 s of the system receiving them   # required
    unit: checkouts                   # required
    counting_rule: Same as VALID      # required
scale: ratio                          # required
applicability: Production, normal traffic. Synthetic checks are a separate SLI   # required
lifecycle: Measured in production. Load-test values are estimates and stay separate   # required
blind_spots: Checkouts that never reach the system and the time spent on the buyer's device are invisible from the system boundary   # required
```

## EBP: error budget policy (stage 4), `ebp/EBP-01.yaml`

```yaml
# common fields ...  (a business decision: rated like any other point, asked when either rating is high)
applies_to: [NFR-01, NFR-02]
rules:
  - {when: Remaining budget falls below 50%, action: The next sprint puts reliability work first}
  - {when: One incident consumes 20% or more of the budget, action: A postmortem is required and at least one root-cause fix goes first}
  - {when: The budget is exhausted, action: "For the rest of the window, release only fixes and security patches"}
  - {when: 50% or more remains at the end of every window, action: "Tighten the SLO, or spend the surplus on release speed and experiments"}
  - {when: A data SLO's budget is exhausted, action: Ticket both the fix of the cause and the correction of bad data already stored}
```

## QAS: assumption (every stage), `qas/QAS-01.yaml`

One judgment made without asking, because neither of its ratings was high (`conventions.md`, "Draft first, ask afterwards"). The items that depend on it list it in `premises`.

```yaml
id: QAS-04
title: Saved cards stay available on every device the buyer signs in on
status: open                          # open | confirmed | overturned | retired
stage: 2                              # the stage that wrote it
kind: judgment                        # judgment | decision (a business decision adopted provisionally)
statement: Buyers expect a card saved on one device to be offered on every device they sign in on.
why_unsure: The PRD names saved cards but not whether they follow the buyer across devices.
breadth: low                          # high | medium | low
severity: low                         # high | medium | low
rating_note: Only FR-02 depends on it; easy to change before the design starts
answer: null                          # the user's answer, when someone asked about it later
answered_in: null                     # where, e.g. "stage 3, question 4"
```
