# Designing SLIs

## Contents
- The shape of an SLI
- How a measure is built
- Six reshaping rules
- Measurement points and methods
- Patterns by system type
- The SLI definition
- The two-person test
- Example definitions

## The shape of an SLI

An SLI is the ratio of good events to valid events, counted over a window:

SLI = good events ÷ valid events

Define SLIs per CUJ: the availability of order confirmation, the latency of order confirmation, the freshness of the stock shown. An SLI measures how the running system behaves. It does not tell you whether users completed their CUJ; that is the quality-in-use measure from stage 1, which stays separate.

## How a measure is built

A characteristic and a sub-characteristic are names of categories; they cannot be measured directly. A measure is computed by a function (A ÷ B) from elements that can be counted (fast requests, valid requests). Each element counts a defined feature (one request accepted by the load balancer, with its response time) of a target entity (the production system's behaviour when it handles order confirmation). Decide them from left to right; the value is built from right to left.

| Part | Checkout latency SLI |
|---|---|
| Measure | Share of checkouts that responded within 1.5 s |
| Function | Fast requests ÷ valid requests |
| Elements | Fast requests, valid requests |
| Feature counted | One request accepted by the load balancer, and its response time |
| Target entity | The production system's behaviour when it handles order confirmation |

The target entity can also be data (a table, a field) or people and their tasks; the shape stays the same. The real work is not picking a measure, but writing each element down so precisely that anyone who counts it gets the same value.

## Six reshaping rules

The obvious measure is rarely fit for production monitoring. Reshape it, and record why in the SLI's `reshaped` field.

1. Replace averages with proportions or percentiles. An average hides a few extremely slow requests. Use the share within a threshold (best for SLOs), or a percentile under a stated load.
2. Replace time-based ratios with event-based ratios. "Time in operation ÷ planned operating time" cannot express a failure that hits only some functions or some users. Count requests (good ÷ valid), and decide whether planned downtime counts against the budget.
3. Move the measurement point closer to the user. Measure at the load balancer or in the client rather than inside the application. Record the measurement period, the sites and the population measured.
4. Write the valid events and the measurement conditions into the definition. The operating conditions of the requirement (workload, users, transaction types) become the SLI's measurement conditions. Say exactly which interval is timed: "from the request being received to the first byte of the response" (inside the system) is not "from the request being sent to the response being received" (at the requester). Leave it open, and the dashboard and the monthly report will disagree.
5. Write criteria as proportions, such as "90% of users finish within 10 minutes". This form is an SLO already.
6. Aggregate according to the scale. Ratings are ordinal: count the share at or above a level, never the average. Counts and durations are ratio scales: ratios and percentiles are fine.

## Measurement points and methods

| Point | Sees | Misses |
|---|---|---|
| Inside the application | Timing of the business logic | Queueing in front of the application, load balancer errors, the network, the client |
| Load balancer logs | Every request that reached the service: status and server-side time | Requests that never arrived; client rendering; third-party screens |
| Synthetic probes | Availability from outside, even with no traffic | Real users' paths and devices |
| Client telemetry (app events, real-user monitoring) | What the user actually waited for, end to end, third-party screens included | Sessions that never send their events (a crash before sending, offline) unless buffered; sampling |
| Data pipeline metadata | Event time against the time the data became available; row counts; validation results | Errors made before the data reached the source |

Name each method ("load balancer log method", "app event method"), and never mix methods in one SLI. A second method makes a second SLI.

## Patterns by system type

### Request-based services (web, API)

- Availability: valid requests answered without a server error ÷ valid requests.
- Latency: valid requests answered within T ÷ valid requests. Leave failed requests out of the latency SLI when an availability SLO on the same CUJ counts them. When there is none (availability is only a monitor), count failed requests as not good here, so that an outage still spends a budget, and say so in `reshaped`.
- Correctness: responses verified as correct (by reconciliation or probes) ÷ responses checked.

### Client-side (mobile, frontend)

- End-to-end task time: CUJ attempts where the user saw the result within T ÷ attempts. For example, "from starting payment to the order confirmation being displayed, within 15 s". This catches waits the server never sees, such as a card issuer's authentication screen.
- Crash-free sessions: sessions without a crash ÷ sessions.
- Page experience: page views whose main content appeared within T ÷ page views.
- Write down the sampling rate, how events are buffered when offline, and which app versions count.

### Data platforms and batch

- Freshness: updates reflected downstream within T ÷ updates (event-based); or checks at which the data's age was below T ÷ checks.
- Completeness: records or partitions present, with their required fields ÷ records or partitions expected.
- Correctness: records passing validation or reconciliation ÷ records checked.
- Run success: scheduled runs finished successfully by their deadline ÷ scheduled runs.
- Be precise about where the clock starts. "From the registration to the app" misses a delay that happens before the registration (a sold-out dish that is registered late); if the requirement is about what users see, start the clock at the real-world event when you can observe it.

### Multi-tenant services (B2B SaaS)

- Compute the same SLIs per tenant as well as overall. A global ratio hides an outage that hits one tenant.
- Load balancer logs rarely carry the tenant. Per-tenant SLIs, and excluding test tenants, then need the tenant from the path or a header, or a join with application logs by request or trace ID. Write the join into the counting rule; an SLI that cannot be split by tenant cannot serve a per-tenant SLO.
- Consider an SLO on the share of tenants that meet the target, so that the worst-served tenants count.
- Asynchronous jobs: jobs completed within T ÷ jobs submitted.

## The SLI definition

Write one `sli/SLI-xx.yaml` per SLI, with the fields in `artifacts.md`.

| Field | What to write | Required |
|---|---|---|
| `function` | GOOD ÷ VALID | yes |
| `refs` | The QR and NFR it serves | yes |
| `reshaped` | How and why it differs from the obvious measure | when reshaped |
| `target_entity` | Which service's, which operation's behaviour (or which data) is measured | yes |
| `purpose` | What you want to know | yes |
| `unit_event` | The one event that is counted | yes |
| `method` | One named method | yes |
| `elements.*.definition` | What makes an event valid or good, thresholds included | yes |
| `elements.*.exclusions` | Health checks, synthetic traffic, load tests, bots, maintenance windows | when any apply |
| `elements.*.inputs` | Which log or source, which fields; for data SLIs, which tables and columns | yes |
| `elements.*.unit` | requests, events, records, runs | yes |
| `elements.*.counting_rule` | Aggregation interval, exclusions, how retries and duplicates count | yes |
| `scale` | ratio for counts and durations; ordinal for ratings | yes |
| `applicability` | The environment and traffic in which the value holds | yes |
| `lifecycle` | Where it is measured. Load-test values are estimates and stay separate | yes |
| `blind_spots` | What the method cannot see, and assumptions such as log loss | yes |

Targets, windows and tolerances do not go in the SLI definition. They belong to the NFR.

## The two-person test

Read the definition as if two people had to compute the SLI independently from it. Would they get the same number? Typical reasons they would not:

- The timed interval is not stated.
- Retries are not de-duplicated, or the key used to de-duplicate is not named.
- Bots, load tests, synthetic checks or maintenance windows are not excluded explicitly.
- "Success" is not defined (is a 4xx a success? a declined card?).
- Two measurement methods are mixed.

## Example definitions

Checkout latency (request-based):

```yaml
id: SLI-02
title: Checkout latency
status: draft
version: 1
basis: inferred
basis_note: The 1.5 s threshold is stated in QR-02; the exclusions are proposed
needs_agreement: true
approver_candidates: [SRE lead, Product owner]
function: FAST ÷ VALID
refs: [QR-02, NFR-02]
reshaped: Rule 1 (average response time replaced with the share within 1.5 s); rule 4 (timed interval stated)
target_entity: Production behaviour of the system that handles POST /checkout
purpose: How often buyers wait at checkout
unit_event: One request accepted by the load balancer
method: Load balancer log method (access logs aggregated every minute)
elements:
  VALID:
    definition: POST /checkout requests from authenticated buyers that did not return 5xx (failures belong to the availability SLI)
    exclusions: Health checks, synthetic monitoring, load tests, planned maintenance windows
    inputs: Load balancer access log - request_id, status, elapsed, user_agent
    unit: requests
    counting_rule: Summed per minute; retries count once per request_id
  FAST:
    definition: VALID requests with elapsed at most 1.5 s, from the load balancer receiving the request to starting the response
    inputs: Same as VALID
    unit: requests
    counting_rule: Same as VALID
scale: ratio
applicability: Production, Tokyo region, normal traffic. Synthetic checks are a separate SLI
lifecycle: Measured in production. Load-test values are estimates and stay separate
blind_spots: Client-side network drops are invisible. Waits on the card issuer's authentication screen are invisible. Assumes log loss below 0.1%
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 4 draft}
```

Stock freshness (data):

```yaml
id: SLI-03
title: Stock freshness
status: draft
version: 1
basis: inferred
basis_note: The 5-minute threshold is a proposed value, not from the inputs
needs_agreement: true
approver_candidates: [Product owner, SRE lead]
function: FRESH ÷ CHANGES
refs: [QR-03, NFR-03]
reshaped: Rule 2 (event-based instead of "share of time the data was fresh")
target_entity: Stock counts shown on product pages and in the cart
purpose: How often buyers see a stock count that lags the warehouse
unit_event: One stock change recorded by the warehouse system
method: Event timestamp method (warehouse change time against the time the storefront cache was updated)
elements:
  CHANGES:
    definition: Stock changes recorded by the warehouse system for items on sale
    exclusions: Changes to items not on sale; bulk corrections during stock-taking
    inputs: Warehouse change events - item_id, change_id, changed_at; storefront cache log - item_id, change_id, applied_at
    unit: events
    counting_rule: Each change_id counts once; counted on the day of changed_at
  FRESH:
    definition: CHANGES whose applied_at is at most 5 minutes after changed_at
    inputs: Same as CHANGES
    unit: events
    counting_rule: Same as CHANGES; a change never applied counts as not fresh
scale: ratio
applicability: Production storefront, all regions
lifecycle: Measured in production
blind_spots: Delays before the warehouse records a change (stock received but not yet registered) are invisible
changelog:
  - {version: 1, date: 2026-01-31, change: Created, reason: Stage 4 draft}
```
