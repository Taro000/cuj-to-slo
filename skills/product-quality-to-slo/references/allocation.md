# Allocation: where each quality requirement lives

## Contents
- Three questions
- Four kinds of non-functional requirement
- Typical allocation by characteristic
- One line from need to requirement
- SLI or quality-in-use measure
- What is not a non-functional requirement

## Three questions

Ask these for every `emphasized` and `minimum-line` quality requirement. Each answer has its own destination.

1. Does it need a supporting function? If no FR provides that function, write an FRP for stage 2.
2. How well must it be met? Keep it as a measure with a target, as a non-functional requirement. External quality observed in production becomes an SLO. Internal quality goes into CI gates and the Definition of Done. Qualities that are not sensibly measured as a ratio of production events go to audits, deadlines and evaluations.
3. Can only something outside the software meet it? Write one line under "Out of scope". Business procedures, operations, facilities and training are not covered by these skills.

A single quality requirement often splits into a supporting function and a target. Non-functional requirements are not a rewording of the quality requirements: the functions that come out of quality requirements are just as important, and they disappear from estimates if only the non-functional column is read.

## Four kinds of non-functional requirement

| Kind | Use it for | Where it lives | Example |
|---|---|---|---|
| `slo` | External behaviour on a CUJ that can be observed continuously in production: availability, latency, correctness checked by reconciliation, data freshness and completeness, job completion | An SLO with an SLI definition, a window, an error budget and a policy | Over a rolling 28 days, 99% of valid checkouts respond within 1.5 s. |
| `gate` | Internal quality, and product properties that can be verified before release | A CI gate or an item in the Definition of Done | The number of database queries per order confirmation stays under the cap, checked in CI. Every new endpoint logs the trace ID (Definition of Done). |
| `audit` | Qualities whose failures are rare and serious, or that cannot be expressed as a ratio of production events: most of security, usability evaluated with people, accessibility, recovery from disasters | Audits, vulnerability-fix deadlines, penetration tests, usability evaluations, accessibility checks, recovery drills | Critical vulnerabilities are fixed within 7 days. Keyboard-only purchase passes the accessibility check at every release. A restore drill each quarter loses no confirmed order. |
| `monitor` | Mostly `minimum-line` quality requirements whose drop shows in production events; also progress checks before a deadline | A light monitor with a threshold and a recipient, just enough that a drop gets noticed | The payment provider's success rate is watched; the on-call engineer is notified when it stays below 99% for an hour. |

How many NFRs a QR gets: an emphasized QR gets what it needs, often an SLO plus a gate. A minimum-line QR gets one light NFR: a monitor when a drop shows in production events, otherwise the lightest gate or audit that would notice it. The table below says which kind fits each characteristic.

## Typical allocation by characteristic

These are starting points, not rules. When the system makes another kind more sensible, use it and say why.

| Characteristic | Usual kind | Notes |
|---|---|---|
| Reliability > availability | `slo` | Success rate of valid requests or CUJ attempts. |
| Reliability > maturity | `monitor`, or `slo` on mobile | Failure counts; crash-free sessions for apps. |
| Reliability > fault tolerance | `gate` and `monitor` | Failover tests before release; dependency health in production. The availability SLO shows the end effect. |
| Reliability > recoverability | `audit` | Recovery time and data loss measured in drills. |
| Performance efficiency > time behaviour | `slo` | Share of valid requests within a threshold. |
| Performance efficiency > resource utilization, capacity | `gate` and `monitor` | Load tests at the stated peak before release; resource monitors in production. |
| Functional suitability > functional correctness | `slo` or `gate` | Reconciliation in production (share of orders whose totals reconcile), tests for the rules. |
| Functional suitability > completeness, appropriateness | `gate` | Acceptance tests per CUJ; mostly covered by the FR acceptance criteria. |
| Compatibility > interoperability | `monitor`, or `slo` when the CUJ depends on it | Success rate of calls to the other system. |
| Compatibility > co-existence | `gate` | Performance tests with the co-located workloads. |
| Usability > user error protection | `gate` or `monitor` | Validation tests; input error rate. |
| Usability > accessibility | `gate` and `audit` | Automated checks in CI plus a manual review per release. |
| Usability > learnability, operability, aesthetics, recognizability | `audit` | Evaluations with people. When measured with people, it is quality in use: keep the criterion in proportion form ("90% of new staff register a product within 30 minutes"). |
| Security (all) | `audit` and `gate` | Vulnerability-fix deadlines, penetration tests, audits; access-control and tamper tests in CI. Not SLOs. |
| Maintainability > analysability | `gate` | The trace ID in every log line (Definition of Done); share of requests with a complete trace. |
| Maintainability > modularity, reusability, modifiability, testability | `gate` and `monitor` | Static analysis, dependency rules, tests for critical rules; delivery measures such as change lead time and change failure rate. |
| Portability | `gate` | Automated installation and deployment; a test matrix of supported environments. |
| Data > currentness, completeness, accuracy, consistency | `slo` when observable continuously, otherwise `gate` | Data SLIs in production; validation at intake as a gate. |
| Data > traceability, compliance, understandability | `gate` and `audit` | History kept and checked; validation against code lists; data dictionary reviewed. |
| Data > availability, recoverability, portability | `audit` and `monitor` | Retrieval and restore drills; export tests. |
| Data > confidentiality | `audit` and `gate` | Encryption and masking checks. |

## One line from need to requirement

Walk each user's need down to its requirements in one row:

| User and need | Quality-in-use requirement | Product quality requirement | Functional requirement | Non-functional requirement |
|---|---|---|---|---|
| Buyer (primary): finish shopping without waiting, even at busy times | Efficiency: time to complete a purchase. Effectiveness: purchase completion | Performance efficiency > time behaviour | None added: the requirement attaches to the existing checkout | Over a rolling 28 days, 99% of checkouts respond within 1.5 s (SLO) |
| Buyer (primary): never charged twice | Freedom from risk: loss from wrong bills | Functional correctness; reliability > fault tolerance | A unique key per payment request; a second payment for the same order is refused | Zero double charges found by the daily reconciliation |
| SREs (secondary): find where a failure is, fast | Efficiency of maintenance work: time to isolate | Maintainability > analysability | A trace ID on every request, carried across services | 99% of payment requests leave a complete trace (also in the Definition of Done) |
| Accounting (indirect): monthly sales figures are right | Freedom from risk: misstated sales | Functional correctness; data accuracy and consistency | Orders and payments are matched every day, with a list of mismatches | The previous day's matching result arrives by 9:00 every morning. Resolving mismatches is accounting's own procedure: out of scope |

## SLI or quality-in-use measure

An SLI counts how the system behaves, on events the team can improve by changing the system or how it is run: requests, runs, records. A quality-in-use measure counts whether people reached their goal: tasks finished, claims approved, months closed. When the two seem to coincide, keep them apart:

- The natural SLI counts the same events as a QIU measure. "Failed nightly runs re-run by 7:00 ÷ failed runs" is the SRE's task (quality in use). Narrow the SLI to the system's part, such as "re-runs that finish within M minutes of being started", or use a gate or a drill instead.
- The only production signal is what users do. When applicants' corrections are the only sign of misread receipts, the correction rate is the QIU measure. Check the system with a labelled test set (a gate) and a periodic sample audit instead.
- The bad events come from data that customers enter, such as approval routes that tenant admins set up. No error budget action reduces them. Watch them with a monitor that tells the people who can fix the data, and look for a supporting function (validation at entry) instead of an SLO.

## What is not a non-functional requirement

- Quality-in-use measures go in neither column. They stay as the stage 1 measures that the SLOs serve.
- Functional suitability is not a functional requirement, and functions are not non-functional requirements. "Calculates the amount" is a function; "zero calculation errors" is a quality requirement, measured here.
- An SLO is a target to keep, with no end. The backlog holds targets to change, which close when done. Do not put SLOs in the backlog; put the work that protects or raises them there.
