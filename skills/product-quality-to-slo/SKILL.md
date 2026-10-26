---
name: product-quality-to-slo
description: Stage 4 of 4 in the quality chain. Turns product and data quality requirements (from functional-requirements-to-product-quality) into non-functional requirements and SLIs/SLOs. Decides for each requirement whether it becomes an SLO on production behaviour, a CI gate or Definition-of-Done item for internal quality, an audit or deadline (security, usability, maintainability), or a light monitor for minimum lines. Writes each SLI as good events ÷ valid events with its measurement point, counting rules, exclusions and blind spots, so two people get the same number; sets provisional SLO targets (never handed down from KPIs), windows and error budgets; drafts an error budget policy and multi-window burn-rate alerts; proposes missing supporting functions back to stage 2. Drafts first, then asks about every uncertain point. Use it for NFRs, SLOs, SLIs, error budgets, burn-rate alerts, latency or availability targets, data freshness or completeness SLOs, or "how do we measure this quality requirement".
license: MIT
metadata:
  version: "0.2.0"
---

# Product and data quality → non-functional requirements, SLIs and SLOs

Stage 4, the last stage of the quality chain. It takes the quality requirements (QR) from stage 3 and produces non-functional requirements (NFR) with measures and targets, SLI definitions (SLI) for the ones that become SLOs, error budget policies (EBP), and function proposals (FRP) for stage 2 when a supporting function is missing.

A "how well" requirement that cannot be measured cannot be checked. Writing a requirement as a measure with a target, and then using that target as the criterion, is what an SLO is. SLOs suit external quality observed in production. Internal quality belongs in CI gates and the Definition of Done. Some characteristics, such as security, usability and maintainability, are not sensibly SLOs at all, and forcing them into one produces numbers that nobody acts on.

The most common failure at this stage is an SLI defined only by a name and a formula. "Availability 99.9%" with no statement of what counts, where it is measured and what is excluded gives a different value to each person who computes it, and the dashboard and the monthly report disagree.

## Read first

- `references/conventions.md`: the chain, the working language, judgment basis marks and the question protocol. Follow it exactly.
- `references/artifacts.md`: where files go, and the schemas for NFR, SLI, EBP and FRP (and the upstream items you read).
- `references/allocation.md`: which kind of NFR each quality requirement becomes.
- `references/sli-design.md`: SLI shapes, the six reshaping rules, measurement points, patterns by system type, and the SLI definition.
- `references/slo-and-error-budget.md`: targets, windows, error budgets, the policy and burn-rate alerts.
- `references/report-template.md`: the report layout and a worked example.
- `references/glossary.md`: terms for the working language.

## Inputs

- `docs/quality/qr/` from stage 3, with `fr/`, `qiu/`, `kpi/` and `context.yaml`. If they do not exist, accept the same information from the user and record its source in the report.
- Operational facts: what telemetry exists (load balancer logs, application events, job logs, data timestamps), traffic volumes, peak workloads, existing SLOs or SLAs, and baselines (load tests, current production figures). Start from `context.yaml → operational_facts`, recorded by stage 1, and the QRs' `stated_numbers`, then add what the user gives.

Essential input: at least one QR whose handling is `emphasized` or `minimum-line`. If there is none, ask for it (or suggest running `functional-requirements-to-product-quality`) before drafting. Missing operational facts are gaps and questions; never invent traffic numbers. When the volume is unknown, leave the error budget as a formula.

If function proposals from stage 3 are still `proposed`, go on, but say in `basis_note` which NFRs depend on them, and remind the user at the end that stage 2 must resolve them.

## Workflow

### 1. Allocate each quality requirement

Take every `emphasized` and `minimum-line` QR (skip `excluded` ones) and ask the three questions in `allocation.md`:

- Does it need a supporting function that no FR provides, or an FR that behaves differently? Write an FRP. Do not add or change FRs yourself.
- Can only something outside the software meet it? Write one line under "Out of scope".
- How well must it be met? Write one or more NFRs of kind `slo`, `gate`, `audit` or `monitor`.

An emphasized QR gets as many NFRs as it needs, often an SLO plus a gate. A minimum-line QR gets one light NFR: a monitor when a drop shows in production events, otherwise the lightest gate or audit that would notice it. One NFR can serve several QRs.

An SLO needs bad events that the team can reduce by changing the system or how it is run. When the natural SLI would count the same events as a quality-in-use measure, or bad events that only customers can prevent (data they enter), follow "SLI or quality-in-use measure" in `allocation.md`.

### 2. Design the SLI for each SLO

For every NFR of kind `slo`, follow `sli-design.md`:

- Shape it as good events ÷ valid events, for one CUJ.
- Apply the six reshaping rules, and record in `reshaped` which ones changed the measure.
- Pick the patterns that fit each CUJ: request-based (web and API), client-side (mobile and frontend), data freshness, completeness and correctness (data platforms and batch), per tenant (B2B SaaS). One system often needs several.
- Measure as close to the user as you can observe. Name the measurement method, and never mix two methods in one SLI.
- Name what is measured: the path, screen or job, and for data SLIs the tables and fields. When the FR gives only the kind of interface, ask for the name; the SLI cannot be counted without it.
- When the SLI needs data that no function produces (an event at a step, a client-side timestamp), write an FRP for it, and name the SLI you would use until it exists.
- Fill in every required field of the SLI definition, and write down the blind spots.
- Apply the two-person test: from the definition alone, would two people computing independently get the same value? If not, tighten the counting rules until they would.

### 3. Set the SLO

Follow `slo-and-error-budget.md`:

- The target is provisional. Start from the numbers stated in the inputs or from baselines; with neither, choose a conservative value. Never derive it from a KPI or KGI target. Mark it as needing agreement.
- The thresholds inside the SLI (the T in "within T seconds", a job's deadline) follow the same rule. Upstream placeholders get their first value here, except values that a law, a contract or someone else sets (a legal retention period): leave those as placeholders and say who sets them.
- A value you propose because nothing gives one is an agreement item, not a question: list it under "Needs agreement" with what it is based on. Ask instead for the facts that would let you set it (baselines, volumes, existing SLAs).
- Say what every number you propose is based on, in any kind of NFR: in `target_rationale` for SLOs, in `basis_note` for the others.
- The window is a rolling 28 days, unless the inputs say otherwise.
- Write the tolerance as the error budget (1 − target). Do not draw a second "watch" line below the target.
- Compute the budget volume (bad events allowed per window) and, for availability, the full-outage equivalent, when the traffic is known. For a batch or deadline SLO, give the share of the budget that one bad run uses.
- If an SLA exists or is planned, it must be looser than the SLO.

### 4. Draft the error budget policy

Write an EBP with the conditions and the actions agreed in advance (`slo-and-error-budget.md`). Mark it as needing agreement from both the business and development: a policy nobody agreed to is not followed on the day the budget runs out.

### 5. Compute the alerts

For each SLO whose events are spread over the window (requests, page views), compute the multi-window, multi-burn-rate thresholds for its window, with the budget spent when each alert fires. For a batch or deadline SLO, whose events are judged once per run, use the deadline pattern in `slo-and-error-budget.md` instead: a progress alert before the deadline and the budget share of one bad run.

### 6. Write the other non-functional requirements

- `gate`: where it runs (CI, a release check such as a load test in staging, or the Definition of Done), what it checks, the threshold.
- `audit`: the mechanism (audit, vulnerability-fix deadline, penetration test, usability evaluation, accessibility check, recovery drill), the criterion and the cadence or deadline.
- `monitor`: the signal, the threshold and who is notified. This is the light watch, mostly for minimum lines.

Order the NFRs by the handling of the QRs they serve, emphasized first, so that the user can agree on the few that matter most before the rest.

### 7. Build the traceability table

One row per chain: user and need → QIU → FR → QR → NFR and SLI. Every NFR must appear in some row. A row that cannot be completed shows a gap upstream.

### 8. Report, questions, items

Write the draft report following `report-template.md`. Ask about every uncertain point as `conventions.md` describes, apply the answers and check the exit criteria. Then write the NFR, SLI, EBP and FRP items. Compute the budgets, volumes and alert thresholds with a script, not by hand.

## Exit criteria

- Every `emphasized` and `minimum-line` QR has at least one NFR, an FRP or a line under "Out of scope".
- Every NFR of kind `slo` has an SLI with every required field, a window, a target with its status, an error budget and an EBP.
- Every SLI passes the two-person test.
- Every SLO has its alerts: burn-rate thresholds for its window, or, for a batch or deadline SLO, a progress alert and the budget share of one bad run.
- No target is derived from a KPI or KGI target.
- Quality-in-use measures are not turned into NFRs; they remain the stage 1 measures that the SLOs serve.
- Every proposed target, threshold, policy and exclusion is marked as needing agreement, with what it is based on.

## Pitfalls

- Keeping an average, or a time-based ratio. Averages hide the slow tail; time-based ratios cannot express a failure that hits only some users or some functions.
- An SLI defined by a name and a formula only.
- Measuring inside the application when users experience something else, such as client-side waits or third-party screens.
- A second line below the target ("98.5% means watch"). On the day the budget runs out, the requirement and the policy then call for different actions.
- Handing targets down from the top ("revenue +10% → completion +2 points → p99 1.5 s").
- Forcing SLOs onto security, usability or maintainability.
- An SLO whose bad events the team cannot reduce, such as errors in data that customers enter. Nobody can act on its budget.
- Paging after a deadline has passed. Once a nightly run has missed 7:00, a page cannot change that night; alert while there is still time to act.
- Putting SLOs in the backlog. An SLO is a target to keep, with no end; the backlog holds the work that protects it or raises it.

## Finish

Tell the user which files were written, how many NFRs of each kind, which SLOs carry provisional targets, what needs agreement, which FRPs were raised (re-run `quality-in-use-to-functional-requirements` to resolve them, then stages 3 and 4 for the affected items), and which questions remain open.
