---
name: functional-req-to-product-quality
description: Stage 3 of 4 in the quality chain. Derives product quality and data quality requirements for the functional requirements produced by quality-in-use-to-functional-req. Walks all eight product quality characteristics (31 sub-characteristics) and all fifteen data quality characteristics, writes failure scenarios, scores impact × likelihood, keeps two to four emphasized quality requirements per CUJ and records why the rest are minimum lines or excluded this cycle, writes external quality before internal, takes in requirements that never come from users (laws, standards and guidelines, security risk analysis, development policies), records conflicts, and proposes missing supporting functions back to stage 2. Drafts first, then asks about every uncertain point. Use it when someone wants quality attributes or "-ilities", a prioritized list of quality characteristics, data quality requirements, or NFR candidates for a set of functions or CUJs. Next stage is product-quality-to-slo.
license: MIT
metadata:
  version: "0.1.0"
---

# Functional requirements → product quality and data quality

Stage 3 of the quality chain. It takes the functional requirements (FR) from stage 2, with the quality-in-use requirements (QIU) they serve, and produces quality requirements (QR) of two kinds: product quality (properties of the software and system) and data quality (properties of the data the system holds). Stage 4 turns them into measurable non-functional requirements and SLOs.

Product quality and data quality are two separate strands, and both lead to quality in use. Building quality requirements from product quality alone loses things like data freshness and accuracy, which hit the business directly but are not properties of the software. An API can return a stale stock count correctly and fast: every external metric is perfect while buyers order items that no longer exist.

Specifying and measuring every sub-characteristic is not possible. Walk all of them as a checklist so that nothing is missed by accident, then keep only the few that matter most for the business, and write down why the others were set aside.

## Read first

- `references/conventions.md`: the chain, the working language, judgment basis marks and the question protocol. Follow it exactly.
- `references/artifacts.md`: where files go, and the schemas for QR and FRP (and the upstream items you read).
- `references/product-quality-model.md`: eight characteristics and 31 sub-characteristics, with examples.
- `references/data-quality-model.md`: fifteen characteristics in two viewpoints, with examples.
- `references/telling-apart.md`: pairs that are easily confused, across the three models.
- `references/prioritization.md`: scoring, handling, requirements that do not come from users, and conflicts.
- `references/report-template.md`: the report layout and a worked example.
- `references/glossary.md`: terms for the working language.

## Inputs

- `docs/quality/fr/` from stage 2, and `context.yaml`, `qiu/`, `kpi/` from stage 1. If they do not exist, accept the same information from the user and record its source in the report.
- `context.yaml → hints` from stage 1: statements whose subject is the product or the data, and requirements that do not come from users. Use every hint, or say in the report why it was set aside. The FRs' `quality_hints` from stage 2 are used the same way.
- Requirements that do not come from users: laws and regulations, standards and guidelines, security requirements from risk analysis, development policies and constraints. Take them from the hints, the input or the user.
- Architecture notes when available (dependencies, data stores, external services). They make failure scenarios concrete.

Essential input: at least one FR that traces to a QIU. If there is none, ask for it (or suggest running `quality-in-use-to-functional-req`) before drafting.

## Workflow

### 1. Group the functions

Group the FRs by the CUJs of the QIUs they trace to, and work one group at a time. An FR's `cuj` field says where the function acts, which can differ: a duplicate check that runs when a claim is submitted serves the approver's CUJ. An FR that serves QIUs of two CUJs belongs to both groups. Functions that trace to no QIU (back-office tools, for example) form a group of their own.

### 2. Walk product quality

For each group, walk all eight characteristics and 31 sub-characteristics in `product-quality-model.md`. For each sub-characteristic, ask: can I write a concrete failure scenario, on these functions, that would hurt a QIU of this CUJ?

- Yes: it is a candidate. Write the failure scenario.
- No: write "not targeted" and a one-line reason. Sub-characteristics that share a reason can share a line.

A failure scenario must be plausible for this system: name the function, dependency, load or data source it comes from. "Any bug could make it wrong" is not a scenario. Functional correctness, for example, is a candidate only where a function computes, transforms or matches something whose error would hurt the QIU (amounts, journal entries, duplicate checks).

Write external quality first: how the system behaves when it runs. Internal quality (properties of the code and design) comes after prioritizing, in step 5.

### 3. Walk data quality

List the data entities these functions create, change or show (orders, prices, stock, addresses and so on). For each entity, walk all fifteen characteristics in `data-quality-model.md`. Inherent characteristics are about the values themselves; system-dependent ones are about how the system keeps and delivers them.

Add the data producers (the people or systems that create the data) to the QR's `stakeholders`. If a producer does not appear in `context.yaml`, or appears there with another data role, keep it by name and ask whether stage 1 should be updated. Do not edit `context.yaml` yourself; the user re-runs stage 1.

When the system itself creates the data (a reading step, a batch), the same incident can show up in both strands. Score it once, in the strand where it is fixed (`telling-apart.md`).

### 4. Take in requirements that do not come from users

Follow `prioritization.md` ("Requirements that do not come from users"). However carefully you trace from users, these never appear on that path. Trace each one to its source (the law, the standard, the risk analysis) and to the role who brought it (legal, security).

- When the inputs give the requirement's content, draft QRs from it.
- When the inputs name a law, standard or policy but not what it requires, draft QRs only for the categories the inputs already raise (a hint, an upstream open gap), mark them `inferred`, and ask what the source requires.
- When the inputs name none, draft no QRs. Ask whether any apply, and list the categories you suspect (for example, "card-data handling rules may apply").

Never take the content of an obligation from your own knowledge or from the examples in this skill. A wrong legal requirement written confidently is worse than an open question.

### 5. Prioritize

Score each candidate's failure scenario for impact (1 to 5) and likelihood (1 to 5), following `prioritization.md`. Per CUJ, the top two to four QRs become `emphasized`. The rest become `minimum-line` (kept, with just enough monitoring that a drop gets noticed) or `excluded` for this cycle, with a reason.

- Count a QR in every CUJ whose QIU it traces to. Never narrow `traces_to` to fit the limit.
- If more than four look indispensable for one CUJ, ask instead of keeping them all. The CUJ may need splitting, or the business may need to choose.
- A requirement from a law or another non-user source is never `excluded`. It is `emphasized` or `minimum-line` by its score, and does not count toward the limit.
- A QIU may end up with only `minimum-line` QRs. Say so in the report, so that the user sees it.
- Likelihood depends on facts the inputs rarely give (architecture, volumes, incident history). Ask about those premises, not about each score.
- The whole priority table is a business decision: mark it as needing agreement.

Then, for each `emphasized` external QR, ask which internal qualities (properties of the code and design) support it, and add those as internal QRs, linked with `implies_internal`. An internal QR takes the handling of the external QR it supports, needs no score of its own, and does not count toward the limit. An internal requirement earns its place only by supporting an external one.

### 6. Write the quality requirements

Draft one QR per requirement. The subject is the product or the data, never a person. Fill in the model, layer, characteristic, statement, `applies_to`, `traces_to` (or `entry_point`), stakeholders, failure scenario, score (none for internal QRs), handling and conflicts.

Do not write measures or targets: stage 4 writes them. When the inputs give numbers ("1.5 s at 300 requests per second"), copy them into `stated_numbers` with their source; a stated number that defines the requirement (a 7:00 deadline) may also appear in the statement. Placeholders from earlier stages (`T_repeat`, `N_onboarding_days`) keep their names: use them in the statement and list them in `placeholders`, and stage 4 gives them values. Do not invent any number.

### 7. Check each one against the confusable pairs

Check every QR against `telling-apart.md`. Getting the model or the characteristic wrong sends the fix to the wrong place: wrong values in the data are not fixed by correcting the code. Note in the report each QR you moved, merged or split after the check.

### 8. Record conflicts

Record conflicts between QRs: a fraud check that adds latency, or a cache that lowers load but makes the data older. Say how each conflict is resolved, or ask. When a QR contradicts an FR's acceptance criteria (the FR lets through what the QR forbids), write an FRP with `changes` naming that FR, so that stage 2 decides.

### 9. Propose missing supporting functions

When a QR needs a function that no FR provides (failing over to a second payment provider, sending stock changes as events), or a change to an FR, write an FRP. Do not add or change FRs yourself. Stage 2 decides, so that every function stays in one list. A function needed only to measure a QR is not proposed here; stage 4 proposes it when it designs the SLI.

### 10. Report, questions, items

Write the draft report following `report-template.md`. Ask about every uncertain point as `conventions.md` describes, apply the answers and check the exit criteria. Then write the QR and FRP items.

## Exit criteria

- For every group, every product sub-characteristic and every data characteristic is either a candidate or has a one-line reason.
- Every hint (`context.yaml → hints`, the FRs' `quality_hints`) is used by a QR or set aside with a reason.
- Every CUJ has two to four emphasized QRs, or a question that asks the business to decide.
- Every QR has `applies_to`, `traces_to` or an entry point, a failure scenario, a handling, and a score unless it is internal. Every excluded QR has a reason.
- Requirements that do not come from users are captured, or the user has confirmed that there are none.
- Every missing supporting function has an FRP.
- No QR contains a measure or target that the inputs did not state.

## Pitfalls

- Trying to measure every characteristic. Check everything; measure few.
- Forgetting data. A correct, fast API returning stale values scores perfectly on every external metric while users are hurt.
- Dropping a legal or standards requirement because its score is low.
- Writing internal quality before external quality.
- Quality requirements without the functions they apply to. Nobody knows where to build them or where to measure them.
- Confusing pairs such as data completeness and security integrity, or data accuracy and functional correctness (`telling-apart.md`).

## Finish

Tell the user which files were written, how many QRs there are per handling, the emphasized QRs per CUJ, the proposals raised (and that stage 2 must be re-run to resolve them), what needs agreement, which questions remain open, and that the next stage is `product-quality-to-slo`.
