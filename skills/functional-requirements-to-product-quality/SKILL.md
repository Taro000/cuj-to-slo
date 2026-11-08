---
name: functional-requirements-to-product-quality
description: Stage 3 of 4 in the quality chain. Derives product and data quality requirements for the functional requirements from quality-in-use-to-functional-requirements. Walks all eight product quality characteristics (31 sub-characteristics) and all fifteen data quality characteristics, writes failure scenarios, scores impact × likelihood, keeps two to four emphasized quality requirements per CUJ and records why the rest are minimum lines or excluded, writes external quality before internal, takes in requirements that never come from users (laws, standards, security risk analysis, development policies), records conflicts, and proposes missing functions to stage 2. States properties, never designs. Drafts first, then asks only about points of high breadth or severity and records the rest as assumptions. Use it when someone wants quality attributes or "-ilities", a prioritized list of quality characteristics, data quality requirements, or NFR candidates for functions or CUJs. Next stage is product-quality-to-slo.
license: MIT
metadata:
  version: "0.3.0"
---

# Functional requirements → product quality and data quality

Stage 3 of the quality chain. It takes the functional requirements (FR) from stage 2, with the quality-in-use requirements (QIU) they serve, and produces quality requirements (QR) of two kinds: product quality (properties of the software and system) and data quality (properties of the data the system holds). Stage 4 turns them into measurable non-functional requirements and SLOs.

Product quality and data quality are two separate strands, and both lead to quality in use. Building quality requirements from product quality alone loses things like data freshness and accuracy, which hit the business directly but are not properties of the software. The system can show a stale stock count correctly and fast: every external metric is perfect while buyers order items that no longer exist.

Specifying and measuring every sub-characteristic is not possible. Walk all of them as a checklist so that nothing is missed by accident, then keep only the few that matter most for the business, and write down why the others were set aside.

A quality requirement says what the product and its data must be, not how they are built. Fault tolerance does not mean a second provider, and freshness does not mean a cache: those are designs that do not exist yet, and choosing them here would be overturned once the design weighs them (`conventions.md`, "Requirements, not design").

## Read first

- `references/conventions.md`: the chain, requirements versus design, shared facts, the working language, judgment basis marks, the question protocol with its decisions and assumptions, and how to finish a run. Follow it exactly.
- `references/artifacts.md`: where files go, and the schemas for QR, FRP and QAS (and the upstream items you read).
- `references/product-quality-model.md`: eight characteristics and 31 sub-characteristics, with examples.
- `references/data-quality-model.md`: fifteen characteristics in two viewpoints, with examples.
- `references/telling-apart.md`: pairs that are easily confused, across the three models.
- `references/prioritization.md`: scoring, handling, requirements that do not come from users, and conflicts.
- `references/report-template.md`: the report layout and a worked example.
- `references/glossary.md`: terms for the working language.

## Inputs

- `docs/quality/fr/` from stage 2, and `context.yaml`, `qiu/`, `kpi/` from stage 1. If they do not exist, accept the same information from the user and record its source in the report.
- `frp/`: the function proposals. On a re-run, take in the ones this stage raised that stage 2 has resolved (`conventions.md`, "Re-running a stage"). The FR that an accepted proposal became joins the `applies_to` of the QRs in its `origin`. A rejected proposal leaves those QRs without the function they needed: revisit their failure scenarios and scores, and say under "Open gaps" which QR is left without it.
- The shared facts in `context.yaml`: `constraints` (CON), `external_systems` (EXT) and `operational_facts` (OPF). Refer to them by ID in `premises` and in text, and never copy their values or wording beyond a short label (`conventions.md`, "Shared facts"). No QR refers to `design_inputs`.
- `qas/`: the assumptions of earlier stages. Re-rate the open ones that this stage's judgments depend on, and ask about those that call for it in this run (`conventions.md`, "Assumptions").
- `context.yaml → hints` from stage 1: statements whose subject is the product or the data, and requirements that do not come from users. Use every hint, or say in the report why it was set aside. The FRs' `quality_hints` from stage 2 are used the same way.
- Requirements that do not come from users: laws and regulations, standards and guidelines, security requirements from risk analysis, development policies and constraints. Take them from the hints, the input or the user.
- Facts about a product that is already running (its dependencies, data stores, incident history) are operational facts. They make failure scenarios and likelihoods concrete: cite them by their OPF ID. For a product not built yet, never ask about its architecture or assume one.

Stage 3 adds or corrects shared facts itself (`conventions.md`, "Shared facts"). A fact that you learn from an input or an answer and that `context.yaml` lacks (a system outside the boundary, a constraint, a fact about the running product, a design decision in a policy) becomes a new EXT, CON, OPF or DIN entry with the next ID, a `source` naming the input or the question ("stage 3, question 2") and `basis: stated` or `confirmed`; the "Inputs" table or the "Decisions" row names it. The rest of `context.yaml` (system, KGIs, outcomes, users and their data roles, CUJs, hints) stays stage 1's: list a gap there under "Open gaps" for stage 1.

Essential input: at least one FR that traces to a QIU. If there is none, ask for it (or suggest running `quality-in-use-to-functional-requirements`) before drafting.

## Workflow

### 1. Group the functions

Group the FRs by the CUJs of the QIUs they trace to, and work one group at a time: walk it (steps 2 and 3), prioritize it and write its QR files (steps 5 to 7) before you start the next group. Take in the requirements that do not come from users (step 4), and the conflicts and proposals that span groups (steps 8 and 9), after the groups. The walk is large: working out every group before writing the first file saves nothing if the run stops. An FR's `cuj` field says where the function acts, which can differ: a duplicate check that runs when a claim is submitted serves the approver's CUJ. An FR that serves QIUs of two CUJs belongs to both groups. Functions that trace to no QIU (back-office tools, for example) form a group of their own.

### 2. Walk product quality

For each group, walk all eight characteristics and 31 sub-characteristics in `product-quality-model.md`. For each sub-characteristic, ask: can I write a concrete failure scenario, on these functions, that would hurt a QIU of this CUJ?

- Yes: it is a candidate. Write the failure scenario.
- No: write "not targeted" and a one-line reason. Sub-characteristics that share a reason can share a line.

A failure scenario must be plausible for this system: name the function, the external system (by its EXT ID), the load or the data source it comes from. Never name an internal component or mechanism that you assume (a cache, a queue, a second server): for a product not built yet, those are design. For a product that is already running, a scenario may cite what it has (a data store, a dependency, a past incident) by its OPF ID. "Any bug could make it wrong" is not a scenario. Functional correctness, for example, is a candidate only where a function computes, transforms or matches something whose error would hurt the QIU (amounts, journal entries, duplicate checks).

Write external quality first: how the system behaves when it runs. Internal quality (properties of the code and design) comes after prioritizing, in step 5. The exception is a CUJ whose users change the software itself (an engineer adding a feature): for them, maintainability is the quality they experience, so walk and score its sub-characteristics as external candidates of that CUJ.

### 3. Walk data quality

List the data entities these functions create, change or show (orders, prices, stock, addresses and so on). For each entity, walk all fifteen characteristics in `data-quality-model.md`. Inherent characteristics are about the values themselves; system-dependent ones are about how the system keeps and delivers them.

A data QR's `stakeholders` include the producers of its data: the users (USR IDs) and the external systems (EXT IDs) that create it. A producer that `context.yaml` does not list, or lists with another data role (`data_role`, `creates_data`), changes a user or a shared fact, so its breadth is high: ask about it, unless an input states it. A system outside the boundary is a shared fact that stage 3 writes itself: once it is stated or confirmed, add it as a new EXT entry with `creates_data: true`, or correct `creates_data` on its entry, and cite its ID. A person who produces the data, and a user's data role, stay stage 1's: list the change under "Open gaps" for stage 1, name the person by their role until then, and tell the user to re-run stage 1.

When the system itself creates the data (a step that reads receipts or builds journal entries), the same incident can show up in both strands. Score it once, in the strand where it is fixed (`telling-apart.md`).

### 4. Take in requirements that do not come from users

Follow `prioritization.md` ("Requirements that do not come from users"). However carefully you trace from users, these never appear on that path. Trace each one to its source (the law, the standard, the risk analysis) and to the role who brought it (legal, security).

- When the inputs give the requirement's content, draft QRs from it.
- When the inputs name a law, standard or policy but not what it requires, draft QRs only for the categories the inputs already raise (a hint, an upstream open gap), and ask what the source requires. The content of a named source is always asked, whatever its ratings (`conventions.md`, "Draft first, ask afterwards"). When the answer is that nobody has checked the source yet, do not ask again: keep the QRs to the categories named, and list the content under "Open gaps" with who should check it.
- When the inputs name none, draft no QRs. Ask whether any apply, and list the categories you suspect (for example, "card-data handling rules may apply"): a missed obligation is legal harm, so its severity is high.
- A policy that chooses a design (a framework, a product, a data store) becomes no QR. It is a design input: if `context.yaml → design_inputs` does not hold it yet, add it as a new DIN entry yourself, with the policy as its `source` (`conventions.md`, "Shared facts"). When the policy carries a property that matters to the requirements, take in that property, stated without the design: a policy to build a web app for one browser on one kind of phone carries "only that kind of smartphone is supported", while the web app and the browser stay in the DIN.
- A QR from a non-user source that also protects a QIU keeps both `entry_point` and `traces_to`. It still does not count toward any CUJ's limit.

Never take the content of an obligation from your own knowledge or from the examples in this skill. A wrong legal requirement written confidently is worse than an open question.

### 5. Prioritize

Score each candidate's failure scenario for impact (1 to 5) and likelihood (1 to 5), following `prioritization.md`. Each CUJ chooses two to four of the QRs that trace to its QIUs, highest score first. A QR chosen by at least one CUJ becomes `emphasized`. The rest become `minimum-line` (kept, with just enough monitoring that a drop gets noticed) or `excluded` for this cycle, with a reason.

- A CUJ's limit counts the QRs it chose. A QR that other CUJs chose and that also traces to this CUJ is listed for it without counting, and this CUJ may choose it too. Never narrow `traces_to` to change what a CUJ can choose.
- Use the QIUs where stage 1 placed them, even when this run decides to move some: stage 1 moves them on its next run, and this stage then chooses again for the CUJs affected.
- If more than four look indispensable for one CUJ, ask instead of keeping them all: five or more QRs depend on that decision, so its breadth is high. The CUJ may need splitting, or the business may need to choose.
- A requirement from a law or another non-user source is never `excluded`. It is `emphasized` or `minimum-line` by its score, and does not count toward the limit.
- A QIU may end up with only `minimum-line` QRs. Say so in the report, so that the user sees it.
- Judge likelihood from requirement-level facts: the external systems a function relies on, the volumes in the operational facts, the nature of the data, and the incident history of a product that is already running (its OPF entries). Never ask about a design that does not exist; write in `basis_note` what the design must confirm. Ask about a missing volume or incident history only when its rating calls for it.
- The emphasized set of each CUJ is always asked, grouped as `conventions.md` says ("Decisions"). Each exclusion is a business decision too: rate it, ask it when a rating is high, and otherwise write a QAS with `kind: decision`, listed in the `premises` of the QR it excludes.

Then, for each `emphasized` external QR, work out which internal qualities (properties of the code and design) support it, and add those as internal QRs, linked with `implies_internal`. State each one as a property that any design must have, without choosing the design: "a change to the shipping rules stays within one part of the code", not "shipping is a separate service". An internal QR takes the handling of the external QR it supports, needs no score of its own, and does not count toward the limit. An internal requirement earns its place only by supporting an external one.

### 6. Write the quality requirements

Draft one QR per requirement. The subject is the product or the data, never a person. Fill in the model, layer, characteristic, statement, `applies_to`, `traces_to` (or `entry_point`), stakeholders, failure scenario, score (none for internal QRs), handling, conflicts and `premises` (the shared facts and assumptions the QR depends on).

State what must hold (how the system behaves, a property of its code, or a property of the data), never a design: no platform, vendor product, interface path, data store, mechanism or measurement tool. Refer to shared facts by ID ("up to the sale peak, OPF-03") instead of copying them.

Do not write measures or targets: stage 4 writes them. When the inputs give numbers, copy them into `stated_numbers` with their source ("1.5 s at the sale peak, OPF-03 (PRD)"); when a number is itself a shared fact (a volume or a peak in `operational_facts`), refer to its ID instead of copying it, and list the fact in `premises`. A stated number that defines the requirement (a 7:00 deadline) may also appear in the statement. Use a placeholder from an earlier stage in a QR statement only when the QR states the same quantity, and keep its name. A person's task time (`T_repeat`) stays in its QIU: the system's response time is another quantity, so do not move it into a time-behaviour QR. When a statement needs a threshold that neither the inputs nor an earlier stage give (how far the stock shown may lag the warehouse), name a placeholder of its own (`T_stock_lag`). List every placeholder a QR uses in its `placeholders`. Stage 4 proposes the values; on your next run, write them into the statements that use them. A conflict note refers to a placeholder by name only, so that a value in a note does not make the QR depend on its QAS. Do not invent any number.

### 7. Check each one against the confusable pairs

Check every QR against `telling-apart.md`. Getting the model or the characteristic wrong sends the fix to the wrong place: wrong values in the data are not fixed by correcting the code. Note in the report each QR you moved, merged or split after the check.

### 8. Record conflicts

Record conflicts between QRs: riding out a failing payment provider, which adds waiting time to checkout, or carrying the sale-peak load in a way that would show older stock. Write the resolution on both QRs as what must hold, not as a mechanism. A conflict that only the business can resolve (which quality gives way, and how far) is a decision: rate it like any other (`conventions.md`, "Decisions"). Otherwise, record how it is resolved. When a QR contradicts an FR's acceptance criteria (the FR lets through what the QR forbids), write an FRP with `changes` naming that FR, so that stage 2 decides.

### 9. Propose missing supporting functions

A function that stage 4 proposed only to measure a QR does not become one of the QR's `applies_to`, even after stage 2 accepts it. When a QR needs a function that no FR provides (confirming orders while the payment provider is failing, refunding a charge for an order that was never confirmed), or a change to an FR, write an FRP. Write it as behaviour, not mechanism: "the system keeps taking payments while the payment provider is failing", not "route payments to a secondary provider". Do not add or change FRs yourself. Stage 2 decides, so that every function stays in one list. A function needed only to measure a QR is not proposed here; stage 4 proposes it when it defines the SLI.

### 10. Report, questions, items

Write the QR and FRP items as you draft them, and the draft report that indexes them, following `report-template.md`. Then list the points you were unsure of and rate them. Record each point you do not ask about as a QAS item, listed in the `premises` of the items that depend on it, and ask about the rest (`conventions.md`, "Draft first, ask afterwards", "Decisions", "Assumptions"). The points this stage meets most are named in the steps above: a missing data producer (step 3), the content of a named source (step 4), the emphasized sets, which are always asked, and the exclusions (step 5), and conflicts that only the business can resolve (step 8). Apply the answers to the items in place, check the exit criteria, and finish the run (`conventions.md`, "Finishing a run").

## Exit criteria

- For every group, every product sub-characteristic and every data characteristic is either a candidate or has a one-line reason.
- Every hint (`context.yaml → hints`, the FRs' `quality_hints`) is used by a QR or set aside with a reason.
- Every CUJ's emphasized set is confirmed by an answer under "Decisions", and a CUJ that chose fewer than two or more than four has the answer that allows it.
- Every QR has `applies_to`, `traces_to` or an entry point, a failure scenario, a handling, and a score unless it is internal. Every excluded QR has a reason.
- Requirements that do not come from users are captured, or the user has confirmed that there are none.
- Every missing supporting function has an FRP, written as behaviour.
- No QR contains a measure or target that the inputs did not state. A threshold that the inputs do not give is a named placeholder.
- No QR names a platform, vendor product, interface path, data store, mechanism or measurement tool. A fact about a product that is already running (a data store it has, a dependency, an incident) is an OPF entry, and a QR cites it by ID.
- Every shared fact this stage learned is in `context.yaml` with its source, and every gap in stage 1's part of `context.yaml` is under "Open gaps".
- Every item that depends on an open QAS lists it in `premises` and has `basis: assumed`.

## Pitfalls

- Trying to measure every characteristic. Check everything; measure few.
- Forgetting data. A correct, fast function that shows stale values scores perfectly on every external metric while users are hurt.
- Writing a design into a requirement: a cache for freshness, a second provider for fault tolerance, a database for recoverability. State what must hold; the design decides how.
- Dropping a legal or standards requirement because its score is low.
- Writing internal quality before external quality.
- Quality requirements without the functions they apply to. Nobody knows where to build them or where to measure them.
- Confusing pairs such as data completeness and security integrity, or data accuracy and functional correctness (`telling-apart.md`).

## Finish

Finish the run as `conventions.md` describes ("Finishing a run"): list the open assumptions as a skim list that needs no answer, tell the user which stages to run or re-run and for which items (earlier stages too, when this run added or corrected a shared fact their items cite), commit the files this run wrote, and apply and commit again any correction that arrives after the commit. Also say which files were written, how many QRs there are per handling, the emphasized QRs per CUJ, and the proposals raised: stage 2 must be re-run to resolve them, then this stage for the affected QRs. The next stage is `product-quality-to-slo`.
