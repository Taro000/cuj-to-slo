# Designing SLIs

## Contents
- The shape of an SLI
- How a measure is built
- Six reshaping rules
- Vantages
- Patterns by system type
- The SLI definition
- The two-person test
- Example definitions

## The shape of an SLI

An SLI is the ratio of good events to valid events, counted over a window:

SLI = good events ÷ valid events

Define SLIs per CUJ: the availability of order confirmation, the latency of order confirmation, the freshness of the stock shown. An SLI measures how the running system behaves. It does not tell you whether users completed their CUJ; that is the quality-in-use measure from stage 1, which stays separate.

Write an SLI in the terms of the domain: which events count, under which conditions, observed from which vantage. It names no log, field, path, table or tool. The design later binds each SLI to the interface or event that produces its events, with the fields its counting rules need, and the method. Write the definition so precisely that the binding has only one reasonable reading.

## How a measure is built

A characteristic and a sub-characteristic are names of categories; they cannot be measured directly. A measure is computed by a function (A ÷ B) from elements that can be counted (fast checkouts, valid checkouts). Each element counts a unit event of the domain (one checkout submitted by a buyer, with the time the system took to answer it) of a target entity (the checkout as the system handles it). Decide them from the top of the table down; the value is built from the bottom up.

| Part | Checkout latency SLI |
|---|---|
| Measure | Share of checkouts answered within 1.5 s |
| Function | Fast checkouts ÷ valid checkouts |
| Elements | Fast checkouts, valid checkouts |
| Unit event | One checkout submitted by a buyer, and the time the system took to answer it, observed at the system boundary |
| Target entity | The checkout as the system handles it, from receiving the order to answering it |

The target entity can also be data (the stock shown to buyers) or people and their tasks; the shape stays the same. The real work is not picking a measure, but writing each element down so precisely that anyone who counts the same events gets the same value.

## Six reshaping rules

The obvious measure is rarely fit for production monitoring. Reshape it, and record why in the SLI's `reshaped` field.

1. Replace averages with proportions or percentiles. An average hides a few extremely slow checkouts. Use the share within a threshold (best for SLOs), or a percentile under a stated load.
2. Replace time-based ratios with event-based ratios. "Time in operation ÷ planned operating time" cannot express a failure that hits only some functions or some users. Count events (good ÷ valid), and decide whether planned maintenance counts against the budget; that is a business decision.
3. Move the vantage closer to the user. Count what users experience, at the system boundary or on the user's side, rather than what one part of the system sees inside it. Say which period, which users and which contexts of use the count covers.
4. Write the valid events and the conditions into the definition. The operating conditions of the requirement (workload, users, transaction types) become the SLI's conditions. Say exactly which interval is timed: "from the system receiving the checkout to answering it" (at the system boundary) is not "from the buyer submitting the order to seeing the result" (on the buyer's side). Leave it open, and the dashboard and the monthly report will disagree.
5. Write criteria as proportions, such as "90% of users finish within 10 minutes". This form is an SLO already.
6. Aggregate according to the scale. Ratings are ordinal: count the share at or above a level, never the average. Counts and durations are ratio scales: ratios and percentiles are fine.

## Vantages

The vantage is where the events are observed from. Choose it as close to the user as the requirement needs; the design then picks the method that realizes it.

| Vantage | Sees | Misses |
|---|---|---|
| `user-side` | What the user actually waited for and saw, end to end, third-party screens included (a card issuer's authentication screen) | Attempts whose events never leave the user's side (the product stopped working, no network), unless the design keeps them |
| `system-boundary` | Every request that reached the system: whether the system failed it, and how long the system took | Attempts that never reached the system; time spent on the user's side and on third-party screens |
| `synthetic` | Whether a scripted user can complete the step from outside, even when no real user is active | Real users' paths, data and situations |
| `data` | The data compared with its source or with what it should be: how old it is when shown, what is missing, what does not reconcile | Errors made before the data reached its source; how the data was presented to users |

- Never mix two vantages in one SLI. A second vantage makes a second SLI.
- Choose the vantage from the requirement, not from the telemetry that happens to exist. Record it in `vantage`, and why in `vantage_reason`.
- What one part of the system sees inside it (the time one component takes) is a diagnostic for the design, not a vantage for an SLO: it misses everything in front of that part and around it.
- When the input names telemetry that already exists (an OPF) and it sees from the chosen vantage, put the OPF ID in `existing_source` and in `premises`. Otherwise leave `existing_source` null; the design provides the source.
- When the vantage needs the system to record something it does not record yet (that a step was reached, when a result was shown to the user), write an FRP for that behaviour, and use an SLI from a vantage that can be counted now until the function exists.

## Patterns by system type

### Request-based services

A request is one thing a user asks the system to do: a checkout, a search, a booking.

- Availability: valid requests that the system answered without failing ÷ valid requests. Say what a failure is: the system not answering, or answering with an error of its own, is one; a declined card or a rejected input is not.
- Latency: valid requests answered within T ÷ valid requests. Leave failed requests out of the latency SLI when an availability SLO on the same CUJ counts them. When there is none (availability is only a monitor), count failed requests as not good here, so that an outage still spends a budget, and say so in `reshaped`.
- Correctness: results verified as correct (by reconciliation or synthetic checks) ÷ results checked.

### User-side experience

- End-to-end task time: CUJ attempts where the user saw the result within T ÷ attempts. For example, "from starting payment to the order confirmation being shown, within 15 s". This catches waits the system boundary never sees, such as a card issuer's authentication screen.
- Crash-free sessions: sessions in which the product did not stop working on the user's side ÷ sessions.
- Screen experience: screens whose main content appeared within T ÷ screens shown.
- Say which users, sessions and contexts of use count (test accounts excluded), and write in `blind_spots` that attempts whose events never leave the user's side may be missed. How the events are collected, sampled and kept while offline is the design's to decide.
- The system often does not record yet what the user saw. Then write an FRP for recording it, and use a system-boundary SLI until it exists.

### Data and batch

- Freshness: changes that reached what users see within T of their source reporting them ÷ changes (event-based); or checks at which the data was younger than T ÷ checks.
- Completeness: entities present with every attribute the users need (an order with its delivery address) ÷ entities expected.
- Correctness: entities that pass validation or reconciliation ÷ entities checked.
- Run success: scheduled runs finished successfully by their deadline ÷ scheduled runs.
- Be precise about where the clock starts. "From the registration in the system" misses a delay that happens before the registration (a sold-out dish that is registered late); if the requirement is about what users see, start the clock at the real-world event when it can be observed.

### Multi-tenant services (B2B SaaS)

- Compute the same SLIs per tenant as well as overall. A global ratio hides an outage that hits one tenant.
- The SLI must be splittable by tenant: say so in the counting rule ("counted per tenant; test tenants excluded"). The design makes sure the tenant of every event is known. An SLI that cannot be split by tenant cannot serve a per-tenant SLO.
- Consider an SLO on the share of tenants that meet the target, so that the worst-served tenants count.
- Asynchronous jobs: jobs completed within T ÷ jobs submitted.

## The SLI definition

Write one `sli/SLI-xx.yaml` per SLI, with the fields in `artifacts.md`.

| Field | What to write | Required |
|---|---|---|
| `function` | GOOD ÷ VALID | yes |
| `refs` | The QR and NFR it serves | yes |
| `reshaped` | How and why it differs from the obvious measure | when reshaped |
| `target_entity` | What is measured, in the terms of the domain: the CUJ step and the FR, the purpose of the screen, the output of the job, or the data entity and its attributes | yes |
| `purpose` | What you want to know | yes |
| `unit_event` | The one event of the domain that is counted, not a log line | yes |
| `vantage` | `user-side`, `system-boundary`, `synthetic` or `data` | yes |
| `vantage_reason` | Why this vantage, and what another vantage would show instead | yes |
| `existing_source` | The OPF ID of telemetry that already exists and sees from this vantage; null otherwise, and the design provides the source | when such telemetry exists |
| `elements.*.definition` | What makes an event valid or good, thresholds included | yes |
| `elements.*.exclusions` | Health checks, synthetic traffic, load tests, bots, maintenance windows | when any apply |
| `elements.*.unit` | checkouts, stock changes, runs | yes |
| `elements.*.counting_rule` | How repeated submissions and duplicates count, which day an event belongs to, how the count splits (per tenant) | yes |
| `scale` | ratio for counts and durations; ordinal for ratings | yes |
| `applicability` | The environment and traffic in which the value holds | yes |
| `lifecycle` | Where it is measured. Load-test values are estimates and stay separate | yes |
| `blind_spots` | What this vantage cannot see | yes |

Targets, windows and tolerances do not go in the SLI definition; they belong to the NFR. Sources, fields and the method do not go in it either; the design binds them.

## The two-person test

Read the definition as if two people had to count the same events independently from it. Would they get the same number? Typical reasons they would not:

- The timed interval is not stated.
- Repeated submissions are not counted once, or what makes two submissions the same (the same order) is not stated.
- Bots, load tests, synthetic checks or maintenance windows are not excluded explicitly.
- "Success" is not defined (is a rejected input a success? a declined card?).
- Two vantages are mixed.

## Example definitions

Checkout latency (request-based, system boundary):

```yaml
id: SLI-02
title: Checkout latency
status: active
basis: inferred
basis_note: null
premises: [OPF-02]
function: GOOD ÷ VALID
refs: [QR-02, NFR-02]
reshaped: Average response time replaced with "share of checkouts answered within 1.5 s"
target_entity: The checkout as the system handles it, from receiving the order to answering it
purpose: How often buyers wait at checkout
unit_event: One checkout submitted by a buyer
vantage: system-boundary
vantage_reason: The time the system takes is what the team can change; the time on the buyer's device is a separate SLI
existing_source: OPF-02
elements:
  VALID:
    definition: Checkouts submitted by signed-in buyers that the system did not fail (failures belong to the availability SLI)
    exclusions: Health checks, synthetic monitoring, load tests
    unit: checkouts
    counting_rule: A checkout the buyer submits again counts once
  GOOD:
    definition: VALID checkouts answered within 1.5 s of the system receiving them
    unit: checkouts
    counting_rule: Same as VALID
scale: ratio
applicability: Production, normal traffic. Synthetic checks are a separate SLI
lifecycle: Measured in production. Load-test values are estimates and stay separate
blind_spots: Checkouts that never reach the system and the time spent on the buyer's device are invisible from the system boundary
```

Stock freshness (data):

```yaml
id: SLI-03
title: Stock freshness
status: active
basis: assumed
basis_note: The 5-minute threshold (T_stock_lag) is a provisional decision (QAS-09). The design binds the two events, the warehouse system reporting a change and the stock shown to buyers reflecting it
premises: [EXT-02, QAS-09]
function: GOOD ÷ VALID
refs: [QR-03, NFR-03]
reshaped: Rule 2 (event-based instead of "share of time the stock shown was fresh")
target_entity: The stock shown to buyers on product pages and in the cart
purpose: How often buyers see a stock count that lags the warehouse
unit_event: One stock change reported by the warehouse system (EXT-02)
vantage: data
vantage_reason: Buyers rely on the stock they are shown; comparing it with the warehouse's changes finds lags that no buyer request reveals
existing_source: null
elements:
  VALID:
    definition: Stock changes reported by the warehouse system for items on sale
    exclusions: Changes to items not on sale; bulk corrections during stock-taking
    unit: stock changes
    counting_rule: A change reported twice counts once; each change counts on the day the warehouse system reported it
  GOOD:
    definition: VALID changes that the stock shown to buyers reflects within 5 minutes (T_stock_lag) of the warehouse system reporting them
    unit: stock changes
    counting_rule: Same as VALID; a change that the stock shown never reflects counts as not good
scale: ratio
applicability: Production, every item on sale
lifecycle: Measured in production
blind_spots: Delays before the warehouse system reports a change (stock received but not yet registered) are invisible
```
