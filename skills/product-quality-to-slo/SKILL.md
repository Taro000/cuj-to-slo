---
name: product-quality-to-slo
description: Stage 4 of 4 in the quality chain. Turns product and data quality requirements (from functional-requirements-to-product-quality) into non-functional requirements and SLIs/SLOs. Decides whether each becomes an SLO, a CI gate or Definition-of-Done item, an audit or deadline (security, usability, maintainability), or a light monitor. Writes each SLI as good events ÷ valid events in domain terms, with its unit event, vantage, counting rules, exclusions and blind spots, so two people counting the same events get the same number, and leaves its sources and fields to the design; sets provisional targets (never handed down from KPIs), windows and error budgets; drafts an error budget policy and burn-rate alerts; proposes missing functions to stage 2. Drafts first, asks only about points of high breadth or severity, and records the rest as assumptions. Use it for NFRs, SLOs, SLIs, error budgets, latency or availability targets, data freshness or completeness SLOs, or "how do we measure this quality requirement".
license: MIT
metadata:
  version: "0.3.0"
---

# Product and data quality → non-functional requirements, SLIs and SLOs

Stage 4, the last stage of the quality chain. It takes the quality requirements (QR) from stage 3 and produces non-functional requirements (NFR) with measures and targets, SLI definitions (SLI) for the ones that become SLOs, error budget policies (EBP), function proposals (FRP) for stage 2 when a supporting function is missing, and assumptions (QAS) for the points it did not ask about.

A "how well" requirement that cannot be measured cannot be checked. Writing a requirement as a measure with a target, and then using that target as the criterion, is what an SLO is. SLOs suit external quality observed in production. Internal quality belongs in CI gates and the Definition of Done. Some characteristics, such as security, usability and maintainability, are not sensibly SLOs at all, and forcing them into one produces numbers that nobody acts on.

The most common failure at this stage is an SLI defined only by a name and a formula. "Availability 99.9%" with no statement of what counts, from where it is observed and what is excluded gives a different value to each person who computes it, and the dashboard and the monthly report disagree.

This stage says what is counted, not how it is collected. An SLI names the events of the domain that count, the conditions, and the vantage they are observed from. The design later binds each SLI to the interface or event that produces its events, with the fields its counting rules need, and the method. Telemetry that already exists is a fact and may be named; choosing any other source here is a design decision made too early (`conventions.md`, "Requirements, not design").

## Read first

- `references/conventions.md`: the chain, requirements versus design, shared facts, the working language, judgment basis marks, the question protocol, decisions and assumptions, re-runs and how a run finishes. Follow it exactly.
- `references/artifacts.md`: where files go, and the schemas for NFR, SLI, EBP, FRP and QAS (and the upstream items you read).
- `references/allocation.md`: which kind of NFR each quality requirement becomes.
- `references/sli-design.md`: SLI shapes, the six reshaping rules, vantages, patterns by system type, and the SLI definition.
- `references/slo-and-error-budget.md`: targets, windows, error budgets, the policy and burn-rate alerts.
- `references/report-template.md`: the report layout and a worked example.
- `references/glossary.md`: terms for the working language.

## Inputs

- `docs/quality/qr/` from stage 3, with `fr/`, `frp/`, `qiu/`, `kpi/`, `qas/` and `context.yaml`. If they do not exist, accept the same information from the user and record its source in the report.
- Operational facts: volumes, peaks, when the product is used, telemetry that already exists, existing SLOs or SLAs, and baselines (load tests, current production figures). Start from `context.yaml → operational_facts` (OPF) and the QRs' `stated_numbers`, then add what the user gives.
- Telemetry that already exists is a fact: an SLI names it by its OPF ID in `existing_source`. Never ask which telemetry, tool or log to use; the design decides.

Stage 4 adds or corrects shared facts itself (`conventions.md`, "Shared facts"), so that items can cite them. A fact that you learn in this stage from an input or an answer (a volume, an existing SLA, a constraint from a contract, a system outside the boundary), or a correction to one, goes into `context.yaml` as a new or updated OPF, CON, EXT or DIN entry: a new entry takes the next ID in its list, its `source` names the input or the question ("stage 4, question 1"), and its `basis` is `stated` or `confirmed`. The rest of `context.yaml` (system, KGIs, outcomes, users and their data roles, CUJs, hints) stays stage 1's: list a gap there under "Open gaps" for stage 1.

Essential input: at least one QR whose handling is `emphasized` or `minimum-line`. If there is none, ask for it (or suggest running `functional-requirements-to-product-quality`) before drafting. Never invent traffic numbers. A missing volume or peak is a shared fact, so its breadth is high and you ask for it; until it is known, leave the budget volume as a formula.

If function proposals are still `proposed`, go on. Keep `affects` current on every proposal, accepted ones included, when this stage's items depend on it or change because of it. Add each NFR or SLI that depends on an open one to that proposal's `affects`, say in its `basis_note` which proposal it waits on, and remind the user at the end that stage 2 must resolve them. On a re-run, take in the proposals this stage raised that stage 2 has resolved (`conventions.md`, "Re-running a stage"). When one is accepted, switch the SLI that waited on it to what the new function makes countable (a new SLI when the vantage changes), and retire the stand-in SLI if nothing else uses it. When one is rejected, keep the SLI you used meanwhile, and say under "Open gaps" which QR is left without the function it needed.

## Workflow

### 1. Allocate each quality requirement

Work in groups (`conventions.md`, "Draft first, ask afterwards"): take the QRs of one CUJ at a time through steps 1 to 3 and 6, and write their NFR, SLI and FRP files before you start the next CUJ. A QR that serves several CUJs is handled with the first of them, and the QRs with an entry point form a group of their own. Steps 4, 5 and 7 come after the groups.

Take every `emphasized` and `minimum-line` QR (skip `excluded` ones) and answer the three questions in `allocation.md`:

- Does it need a supporting function that no FR provides, or an FR that behaves differently? Write an FRP. Do not add or change FRs yourself.
- Can only something outside the software meet it? Write one line under "Out of scope".
- How well must it be met? Write one or more NFRs of kind `slo`, `gate`, `audit` or `monitor`.

An emphasized QR gets as many NFRs as it needs, often an SLO plus a gate. A minimum-line QR gets one light NFR: a monitor when a drop shows in production events, otherwise the lightest gate or audit that would notice it. One NFR can serve several QRs.

An SLO needs bad events that the team can reduce by changing the system or how it is run. When the natural SLI would count the same events as a quality-in-use measure, or bad events that only customers can prevent (data they enter), follow "SLI or quality-in-use measure" in `allocation.md`.

Every NFR states the conditions it holds under (`conditions`: workload, users, transactions). Refer to operational facts by their ID ("up to the sale peak (OPF-03)"), list them in `premises`, and copy neither their values nor their wording. Never state how or when the servers run: when the product is used is an operational fact, how it is run is design.

### 2. Design the SLI for each SLO

For every NFR of kind `slo`, follow `sli-design.md`:

- Shape it as good events ÷ valid events, for one CUJ.
- Apply the six reshaping rules, and record in `reshaped` which ones changed the measure.
- Pick the patterns that fit each CUJ: request-based, user-side, data and batch (freshness, completeness, correctness, run success), per tenant (B2B SaaS). One system often needs several.
- Choose the vantage (user-side, system-boundary, synthetic or data) as close to the user as the requirement needs, and say why in `vantage_reason`. Never mix two vantages in one SLI; a second vantage makes a second SLI.
- Name what is measured in the terms of the domain: the CUJ step and the FR, the purpose of the screen, the output of the job, the data entity and its attributes. Never ask for paths, tables or fields; the design binds them.
- When the input names telemetry that already exists (an OPF) and it sees from the chosen vantage, put its ID in `existing_source`; otherwise leave it null.
- When counting needs the system to record something it does not record yet (that a step was reached, when a result was shown to the user), write an FRP for that behaviour, stated without reference to the SLI, and name the SLI you would use until it exists. List the items that wait on it in its `affects`.
- Fill in every required field of the SLI definition, and write down the blind spots.
- Apply the two-person test: from the definition alone, would two people counting the same events independently get the same value? If not, tighten the counting rules until they would.

### 3. Set the SLO

Follow `slo-and-error-budget.md`:

- Start the target from the numbers stated in the inputs or from baselines (an OPF); with neither, choose a conservative value. Never derive it from a KPI or KGI target.
- The thresholds inside the SLI (the T in "within T seconds", a job's deadline) follow the same rule. A threshold that neither the inputs nor an earlier stage named gets a placeholder name of its own ("Numbers" in `conventions.md`); a target needs none.
- List under "Placeholders" every placeholder still open anywhere in the chain: in QIU measures, FR criteria, FRPs, QRs and this stage's items. A placeholder stays open until an answer decides its value; one whose written value rests on an open QAS stays listed with that QAS. Name the decided ones in one line under the table. Propose a value for each and say what it is based on, except a value that a law, a contract or someone else sets (a legal retention period): leave it open and say who sets it. Write the values that this stage's items use into those items. A value for a placeholder in an earlier stage's item is written by that stage on its next run, from the answer or the QAS: tell the user which stages to re-run, and for which items. Values proposed on the same basis (the task times of one CUJ) may share one QAS. When an answer gives one placeholder two values for two uses, keep its name for one use and name a new placeholder for the other.
- The targets, thresholds and placeholder values you propose, the exclusions that leave real users' events out of the count (planned maintenance), and any window other than the default are business decisions (`conventions.md`, "Numbers" and "Decisions"). Rate each one like any other point. When you ask, the answer is the decision: mark the judgment `confirmed` (a target gets `target_status: confirmed`) and record the question under "Decisions". When the user gives a value and calls it provisional, write it with `target_status: provisional`, say so in `target_rationale`, and write no QAS. When you do not ask, write a QAS with `kind: decision`, list it in the `premises` of every item that uses the value, and leave a target `provisional`.
- Say what every number you propose is based on, in any kind of NFR: in `target_rationale` for SLOs, in `basis_note` for the others.
- The window is a rolling 28 days, unless the inputs say otherwise.
- Write the tolerance as the error budget (1 − target). Do not draw a second "watch" line below the target.
- Compute the budget volume (bad events allowed per window) and, for availability, the full-outage equivalent, when an OPF gives the traffic; list that OPF in `premises`. For a batch or deadline SLO, give the share of the budget that one bad run uses. When one bad event uses the whole budget, follow "Very low traffic" in `slo-and-error-budget.md`.
- If an SLA exists or is planned, it must be looser than the SLO.

### 4. Draft the error budget policy

Write an EBP with the conditions and the actions decided in advance (`slo-and-error-budget.md`). An error budget policy is always asked, whatever its ratings (`conventions.md`, "Decisions"): the user's answer is the policy. It binds every SLO it applies to, and it has to be settled before any budget runs out: a policy first discussed on the day the budget is gone gets argued over instead of followed.

### 5. Compute the alerts

For each SLO whose events are spread over the window (requests, screens shown), compute the multi-window, multi-burn-rate thresholds for its window, with the budget spent when each alert fires. For a batch or deadline SLO, whose events are judged once per run, use the deadline pattern in `slo-and-error-budget.md` instead: a progress alert before the deadline and the budget share of one bad run.

### 6. Write the other non-functional requirements

- `gate`: where it runs (CI, a release check such as a load test at peak workload, or the Definition of Done), what it checks, the threshold.
- `audit`: the mechanism (audit, vulnerability-fix deadline, penetration test, usability evaluation, accessibility check, recovery drill), the criterion and the cadence or deadline.
- `monitor`: the signal in the terms of the domain (the payment provider's success rate), the threshold and who is notified. This is the light watch, mostly for minimum lines.

Order the NFRs by the handling of the QRs they serve, emphasized first, so that the few that matter most are read first.

### 7. Build the traceability table

One row per chain, IDs only: user → QIU → FR → QR → NFR and SLI. Every NFR must appear in some row. A row that cannot be completed shows a gap upstream.

### 8. Items, report, questions

Write the NFR, SLI, EBP and FRP items as you draft them, and the draft report that indexes them (`report-template.md`). Compute the budgets, volumes and alert thresholds with a script, not by hand.

Then list the uncertain points, rate them, ask about the ones that `conventions.md` says to ask about, and record the rest as QAS ("Draft first, ask afterwards"). The error budget policy is always asked. Beyond it, the points asked in this stage are usually a volume or peak that a budget needs (a shared fact), an exclusion that hides failures users feel (planned maintenance), an SLA or any other value promised to someone outside, and the content of a law, standard or contract that sets a value. A first target, a monitor threshold or a placeholder value that few items use usually is not. Re-rate the open assumptions of earlier stages that your judgments depend on, and ask about one when a judgment you ask about depends on it (`conventions.md`, "Assumptions").

Apply the answers to the items in place, and check the exit criteria.

## Exit criteria

- Every `emphasized` and `minimum-line` QR has at least one NFR, an FRP or a line under "Out of scope", except a QR that this run's answers make unnecessary: list it under "Open gaps" for stage 3.
- Every NFR of kind `slo` has an SLI with every required field, a window, a target with its status, an error budget and an EBP.
- Every SLI passes the two-person test.
- Every SLO has its alerts: burn-rate thresholds for its window, or, for a batch or deadline SLO, a progress alert and the budget share of one bad run.
- No target is derived from a KPI or KGI target.
- Quality-in-use measures are not turned into NFRs; they remain the stage 1 measures that the SLOs serve.
- No SLI or NFR names a source, field, tool, path, region or server schedule, except telemetry that already exists, named by its OPF ID in `existing_source`.
- NFR conditions refer to operational facts by ID. No item copies a fact, except a number computed from it, with the fact in `premises`.
- Every proposed target, threshold, policy and exclusion says what it is based on, and is either confirmed by an answer or recorded as a QAS with `kind: decision`. The error budget policy is confirmed by an answer.
- Every placeholder still open in the chain is listed under "Placeholders", with a proposed value and what it is based on, or with who sets it.
- Every NFR or SLI that waits on an open proposal is in that proposal's `affects`.
- Every item that depends on an open QAS lists it in `premises` and has `basis: assumed`.
- The report restates nothing that an item says.

## Pitfalls

- Keeping an average, or a time-based ratio. Averages hide the slow tail; time-based ratios cannot express a failure that hits only some users or some functions.
- An SLI defined by a name and a formula only.
- Choosing a vantage far from the user when users experience something else, such as waits on their own side or on third-party screens.
- Naming a log, a field, a path or a tool in an SLI. It is a design choice made before the design; the design overturns it, and every SLI that copied it has to be rewritten.
- A second line below the target ("98.5% means watch"). On the day the budget runs out, the requirement and the policy then call for different actions.
- Handing targets down from the top ("revenue +10% → completion +2 points → p99 1.5 s").
- Forcing SLOs onto security, usability or maintainability.
- An SLO whose bad events the team cannot reduce, such as errors in data that customers enter. Nobody can act on its budget.
- Paging after a deadline has passed. Once a nightly run has missed 7:00, a page cannot change that night; alert while there is still time to act.
- Putting SLOs in the backlog. An SLO is a target to keep, with no end; the backlog holds the work that protects it or raises it.

## Finish

Tell the user which files were written, how many NFRs of each kind, which SLO targets are confirmed and which are provisional, which FRPs were raised (re-run `quality-in-use-to-functional-requirements` to resolve them, then stages 3 and 4 for the affected items), and which earlier stages to re-run so that they write the placeholder values proposed here. Then finish the run as `conventions.md` describes ("Finishing a run"): the open assumptions as a list that needs no answer, the stages to re-run (earlier ones too, when this run added or corrected a shared fact their items cite), a commit of the files this run wrote, and, when a correction arrives after the commit, the correction applied and committed again.
