---
name: cuj-to-quality-in-use
description: Stage 1 of 4 in the quality chain. Turns the users, critical user journeys (CUJs) and requirements a person provides into quality-in-use requirements (who must reach which goal, in which context of use, judged by effectiveness, efficiency, satisfaction, freedom from risk and context coverage) and means KPIs, by writing A÷B formulas where user goals and intermediate outcomes meet, and counting each narrowed to the context (quality-in-use measure) and overall (KPI). Drafts everything first, then asks only about points of high breadth or severity and records the rest as assumptions. Use it whenever someone has personas or user types, CUJs, user stories, a PRD or business goals (KGI, OKR) and wants user-outcome metrics, KPIs tied to user goals, UX quality requirements, or a starting point for deriving functional requirements, quality attributes, NFRs or SLOs from user needs, even if they never say "quality in use". Writes docs/quality/. Next stage is quality-in-use-to-functional-requirements.
license: MIT
metadata:
  version: "0.3.0"
---

# CUJ → quality in use and KPIs

Stage 1 of the quality chain. It takes the users, CUJs and requirements that the user provides and produces quality-in-use requirements (QIU) and means KPIs (KPI). Stage 2 turns these into functional requirements.

Quality in use is the degree to which a specific user can reach their own goal, in a specific context of use, effectively, efficiently, with satisfaction and without risk. The subject is always a user, and the goal is the user's goal, not the business's. Everything below the stage depends on getting this right: whose journey is protected decides which qualities matter and why.

## Read first

- `references/conventions.md`: the chain, requirements versus design, the working language, judgment basis marks, shared facts, the question protocol, assumptions and how to finish a run. Follow it exactly.
- `references/artifacts.md`: where files go and the schemas for `context.yaml`, QIU, KPI and QAS.
- `references/quality-in-use-model.md`: the five characteristics and nine sub-characteristics, with examples.
- `references/formula-and-kpi.md`: how one formula yields both the quality-in-use measure and the KPI.
- `references/report-template.md`: the report layout and a worked example.
- `references/glossary.md`: terms for the working language.

## Inputs

This stage works from what the user gives. It normalizes and classifies users, CUJs, KGIs and outcomes, but does not invent them.

- Users or stakeholders, CUJs, and requirements: business goals and how they are measured, intermediate outcomes, user needs, constraints. Notes, PRDs, user stories and slide text all count. Inputs often carry design decisions as well (a vendor, a platform); record them, but build nothing on them (step 1).
- Existing artifacts under `docs/quality/`. On a re-run, start from what changed since the report's `Inputs as of` commit, and update the files in place (`conventions.md`, "Re-running a stage").
- On a re-run, what the later stages left for this one, handled as inputs, not as hand edits: shared facts they added to or corrected in `context.yaml`; the gaps their reports list for stage 1 under "Open gaps" (a missing user, a data role); values that stage 4 proposed for the placeholders in QIU measures, which you write into the measures; functions that stage 2 added to produce the data a QIU or KPI lacked, after which you set `observable` and close the open gap; and answers recorded in the QAS files this stage wrote, including those the design chain passed on.

Essential inputs: at least one user and one CUJ. If either is missing, ask for it before drafting. Anything else that is missing becomes a visible gap in the draft and a point to rate afterwards.

A listed user often has no CUJ of their own. If the input describes what that user does ("registers employees and sets approval routes"), draft a CUJ from that description only, mark it `inferred`, and ask whether to keep it: it adds a CUJ, so its breadth is high. Take its goal and steps from the description; where the input gives no steps, leave `steps: []` and record the gap, rather than writing steps yourself. If the input says nothing about what the user does, draft no CUJ: ask what their journey is, or whether to exclude them this cycle. A CUJ drafted from what the user said in an answer is not asked about again.

## Workflow

### 1. Boundary, shared facts and business requirements

- Write the system boundary and the intended purpose, from the input. Describe the boundary by what users do in it and what it leaves out, not by platforms or components.
- Record the facts that items will rely on as shared facts in `context.yaml` (`conventions.md`, "Shared facts"):
  - requirement-level constraints, such as a contract or a business decision ("this is a demo; no real money moves"), under `constraints` (CON);
  - systems outside the boundary that the product exchanges data with or calls, under `external_systems` (EXT), named by their role ("the payment provider"), never by product name, with `creates_data: true` when they create data the users rely on;
  - volumes, peaks, when the product is used, telemetry that already exists, baselines, and existing SLOs or SLAs, under `operational_facts` (OPF). Telemetry that already exists is a fact; telemetry that does not is design.
- Record each design decision the input states (a product name, a platform, a data store, a mechanism) once under `design_inputs` (DIN), for the design chain. No item refers to a DIN entry or copies it. When the decision carries a property that matters to the requirements, write that property as a constraint in requirement terms ("card payments are taken by an external payment provider; the store never handles card numbers").
- Items refer to shared facts by ID, in `premises` and in their text, with at most a short label ("up to the sale peak, OPF-03"). They never copy a fact's values or wording, so that a changed fact changes one entry.
- Later stages add and correct shared facts too, each new one with the next ID. Never delete an entry of `context.yaml` or reuse its number: an entry that no longer holds gets `status: retired`.
- KGI: the measure of the business purpose (revenue, profit and so on), with its target. The KGI is the only item whose target may be set in advance, and the business sets it. If the input has no KGI, leave a gap and ask: everything below depends on it.
- Intermediate outcomes: the behaviours the business wants more or less of in order to reach the KGI. They are usually user behaviours; in B2B they can also be decisions of customer organizations, such as renewing, churning or expanding. Phrase each one with one of five verbs: maintain, eliminate, decrease, increase, create, using the glossary's word for the working language. For example: "Increase the number of buyers who complete checkout." Rewrite vague verbs such as "improve" or "strengthen" into one of the five; if the rewrite might change the meaning, ask, because it changes an outcome. Do not write formulas yet.
- When the input gives reasons behind an outcome (the top reasons customers churn, for example), use them as failure scenarios for the CUJs they point to, not as outcomes of their own, unless the input states them as goals.
- Record under `hints`, for stage 3, the statements whose subject is the product or the data, and the requirements that do not come from users (laws, standards and guidelines, security risk analysis, development policies). When a statement names a product or a mechanism, the decision goes under `design_inputs`, and only the need it serves goes into a hint or an item, in requirement terms. A law or standard named without its content goes into a hint as it is, and stage 3 asks what it requires; ask about it here only when a judgment of this stage depends on its content.

### 2. Users

- List every user in the input and classify each one:
  - primary: uses the system for their own goal;
  - secondary-operation: administrators, security administrators, people who enter data;
  - secondary-maintenance: people who maintain or modify the system, analyze failures, migrate or install it;
  - indirect: receives outputs without using the system.
- Mark data producers and data consumers: `data_role` for people; for systems outside the boundary that create data the users rely on (the warehouse system's stock counts, a partner's price feed), `creates_data: true` on their EXT entry. Producers matter later, because a data error can reach users without any software defect.
- Mark each user as considered or excluded. Give the reason for every exclusion. An exclusion that neither the input nor the boundary settles changes a user, so ask about it.
- When a category is empty (often secondary or indirect users), do not invent anyone. Ask whether someone in that category should be considered, and give the examples you suspect: the answer may add a user, so its breadth is high.

### 3. CUJs and context of use

For each outcome, identify the users and CUJs that produce it. For each CUJ, write the user's goal and the steps. Then decide the contexts of use: the kind of device, network, user type, task, environment and any other condition that changes the result (tenant size, data volume), which are the conditions held fixed when counting. Results are comparable only within the same context of use; context coverage (step 4) is the one deliberate comparison across contexts.

Write kinds of device and situations only: smartphone, shared PC, one hand, outdoors, unstable network. Never write a platform, an operating system, a browser or a version, and never draft an assumption about one. A platform choice made here gets copied into many items, and the design overturns it once it weighs the choice. A platform the input states is a design input (step 1).

When the input names only one context for a CUJ, the narrowed count and the overall count may be the same. Say so in the report, under "One formula, two counts", rather than inventing a second context.

### 4. Quality-in-use requirements

For each CUJ, walk all five characteristics and nine sub-characteristics in `quality-in-use-model.md`. Keep a characteristic when you can write a concrete failure scenario that would hurt the outcome and that the inputs point to (a need, a requirement, a step, or a failure the inputs describe), not one that could be imagined for any product. List the others in the report as "not targeted", with a one-line reason, so that a later reader can tell an omission from a decision. Put characteristics set aside for the same reason in one row; one row can cover several CUJs. "Not targeted" is your judgment, not a business exclusion, unless it drops something the input asked for: then it is an exclusion, which is a business decision (`conventions.md`, "Decisions").

Put each QIU under the CUJ whose steps produce what its measure counts. Being charged more than the price shown is counted at payment, so its QIU belongs to the CUJ that pays, even though the price was shown in an earlier CUJ. A QIU under the wrong CUJ makes stage 3 weigh its quality requirements against that CUJ's priorities instead of its own.

Write each requirement with the user as the subject: "<user> can <reach their goal> in <context of use>, <how>". If the subject turns into the product ("the system validates the address") or the data ("every order has an address"), it is not quality in use. Put it under `hints` in `context.yaml`, for stage 3, not in a QIU.

A context coverage requirement repeats another QIU's formula in another context of use: one that the requirements name (context completeness) or did not anticipate (flexibility). Narrow the two QIUs to contexts that do not overlap, and name the base QIU in `measure.compared_with`. The gap between the two values is what you judge.

### 5. One formula, two counts

Follow `formula-and-kpi.md`:

1. Before writing any formula, fix what the events named in the outcome mean (what counts as "matched", "completed" or "cancelled"); a meaning the inputs do not settle changes an outcome, so it is asked. Then fix four things: whose goal is counted; which context of use; whether the denominator contains only people who needed to do the task; and whether the product's contribution can be isolated.
2. When the user's goal and the business's goal meet in one event, write the formula A ÷ B for that event. Counted within the context of use, it is the quality-in-use measure, which goes in the QIU. Counted over everyone, it is the means KPI, which goes in a KPI item that points back to the QIU. Match the two by what A and B count, not by their names. Write A and B as events of the domain ("orders confirmed"), never as log lines, fields or tools; where an event is observed, and with what, is for the design to decide.
3. When they do not meet, say so. An outcome with no matching user event (ad revenue, or churn decided by a customer company) gets a KPI only, with an `overlap_note`. A QIU that the business does not count overall gets no KPI. Note in the report that a KPI with no overlap can move against quality in use.
4. Whether the business counts something overall is a fact about the business. When the input names no KPIs, draft a KPI only where the formula's event directly counts a stated outcome. Whether the business counts these KPIs is one point: when its rating calls for a question, ask about the drafted KPIs together in one multi-select question, with a way to say that the business counts an outcome with a different formula, and to give it; otherwise record one assumption that every drafted KPI lists in `premises`.
5. A threshold inside a formula ("within N days", "by 7:00") is part of the criterion. Take it from the input; otherwise write a named placeholder, as `conventions.md` ("Numbers") describes.
6. If the product cannot observe the event a formula needs (it happens in another system, or it needs a survey the input does not mention), keep the formula, because it says what matters. Set `observable: false` and record the gap under "Open gaps"; stage 2 decides whether a function is needed to observe it.
7. Respect the scale. Satisfaction ratings are ordinal: count the share of answers at or above a level, and never average them. Prefer proportion criteria such as "90% of users finish within T minutes".

### 6. Targets

Do not derive QIU or KPI targets from the KGI target. How much one stage moves the next is not known yet, so a target passed down from the KGI would look precise without any basis. Leave targets `undecided`, or propose a `provisional` one when the input gives a baseline. A provisional target is a business decision (`conventions.md`, "Decisions"): when it is asked, the answer sets the target and its status becomes `confirmed`; otherwise it stays `provisional`, recorded as an assumption with `kind: decision`. A target the input states keeps its source, and its `target_status` is `confirmed`: the business has already decided it.

### 7. Report, questions, items

Write `context.yaml` and the QIU and KPI files as you draft them, and the draft report that indexes them, following `report-template.md`. Then list the points you were unsure of and rate them. Record each point you do not ask about as an assumption (a QAS file, listed in the `premises` of the items that depend on it), and ask about the rest, as `conventions.md` describes ("Draft first, ask afterwards", "Assumptions"). In this stage most questions come from points that change a user, a CUJ, an outcome or a KGI, which are always high in breadth: a CUJ drafted from a user's description, an empty user category, an exclusion the input does not settle, a verb rewrite that might change an outcome, a missing KGI. A context-of-use detail that only one QIU depends on is usually an assumption. Never ask about, or draft an assumption about, a platform, a product or a tool.

List every named placeholder under "Placeholders". Apply the answers to the items in place, check the exit criteria, and finish the run (`conventions.md`, "Finishing a run").

## Exit criteria

- The KGI has a measure and a target, or the gap is recorded under "Open gaps".
- Every outcome uses one of the five verbs and has no formula of its own.
- Every considered user has at least one CUJ, from the input or drafted from what the input says the user does. Every QIU has a context of use. Every excluded user has a reason.
- Every formula yields both a narrowed quality-in-use measure and an overall KPI, or the report says why the two do not overlap.
- Every QIU has a user as its subject and a measure with a numerator, a denominator and narrowing conditions. Every context coverage QIU names the QIU it is compared with.
- No number appears that the input does not give; every threshold not given is a named placeholder listed under "Placeholders".
- No item names a platform, product, interface path, data store, mechanism or measurement tool, and every design decision found in the input is under `design_inputs`, where no item refers to it.
- The facts that items rely on are CON, EXT or OPF entries, cited by ID in `premises` and never copied.
- Each point you were unsure of is under "Decisions" or in a QAS file, and each point rated high was asked.
- Every item that depends on an open QAS lists it in `premises` and has `basis: assumed`.

## Pitfalls

- Starting from a characteristic ("99.9% availability") before deciding whose CUJ is protected. The priorities and targets that follow then have no basis.
- Writing the formula first. Easy-to-log events (clicks, page views) creep in, and nobody checks whether they match the user's goal.
- Counting what the business gets from users (click-through, time on site) as quality in use.
- Comparing rates measured in different contexts of use, other than in a context coverage requirement that names what it is compared with.
- A denominator that includes people who never needed the task. It then measures demand or seasonality, not quality.
- Writing a platform, a product or a tool into an item because the input mentioned it in passing. Every stage below copies it, and the design overturns it; record it once under `design_inputs` instead.
- Copying a shared fact's value into items instead of citing its ID. When the fact changes, nobody finds the copies.

## Finish

Finish the run as `conventions.md` ("Finishing a run") describes. List the open assumptions as a skim list that needs no answer. Tell the user which files were written and how many items of each kind, which gaps remain open, and that the next stage is `quality-in-use-to-functional-requirements` (on a re-run, which later stages to re-run and for which items). Then commit the files this run wrote. A correction that arrives after the commit is applied like an answer and committed again.
