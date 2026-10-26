---
name: cuj-to-quality-in-use
description: Stage 1 of 4 in the quality chain. Turns the users, critical user journeys (CUJs) and requirements a person provides into quality-in-use requirements (who must reach which goal, in which context of use, judged by effectiveness, efficiency, satisfaction, freedom from risk and context coverage) and means KPIs, by writing one A÷B formula per intermediate outcome and counting it narrowed to the context (quality-in-use measure) and overall (KPI). Drafts everything first, then asks about every uncertain point. Use it whenever someone has personas or user types, CUJs, user stories, a PRD or business goals (KGI, OKR) and wants user-outcome metrics, KPIs tied to user goals, UX quality requirements, or a starting point for deriving functional requirements, quality attributes, NFRs or SLOs from user needs, even if they never say "quality in use". Writes docs/quality/. Next stage is quality-in-use-to-functional-req.
license: MIT
metadata:
  version: "0.1.0"
---

# CUJ → quality in use and KPIs

Stage 1 of the quality chain. It takes the users, CUJs and requirements that the user provides and produces quality-in-use requirements (QIU) and means KPIs (KPI). Stage 2 turns these into functional requirements.

Quality in use is the degree to which a specific user can reach their own goal, in a specific context of use, effectively, efficiently, with satisfaction and without risk. The subject is always a user, and the goal is the user's goal, not the business's. Everything below the stage depends on getting this right: whose journey is protected decides which qualities matter and why.

## Read first

- `references/conventions.md`: the chain, the working language, judgment basis marks and the question protocol. Follow it exactly.
- `references/artifacts.md`: where files go and the schemas for `context.yaml`, QIU and KPI.
- `references/quality-in-use-model.md`: the five characteristics and nine sub-characteristics, with examples.
- `references/formula-and-kpi.md`: how one formula yields both the quality-in-use measure and the KPI.
- `references/report-template.md`: the report layout and a worked example.
- `references/glossary.md`: terms for the working language.

## Inputs

This stage works from what the user gives. It normalizes and classifies users, CUJs, KGIs and outcomes, but does not invent them.

- Users or stakeholders, CUJs, and requirements: business goals and how they are measured, intermediate outcomes, user needs, constraints. Notes, PRDs, user stories and slide text all count.
- Existing artifacts under `docs/quality/`. On a re-run, update them in place.

Essential inputs: at least one user and one CUJ. If either is missing, ask for it before drafting. Anything else that is missing becomes a visible gap in the draft and a question afterwards.

A listed user often has no CUJ of their own. If the input describes what that user does ("registers employees and sets approval routes"), draft a CUJ from that description only, mark it `inferred`, and ask whether to keep it. Take its goal and steps from the description; where the input gives no steps, leave `steps: []` and record the gap, rather than writing steps yourself. If the input says nothing about what the user does, draft no CUJ: ask what their journey is, or whether to exclude them this cycle.

## Workflow

### 1. Boundary and business requirements

- Write the system boundary, the intended purpose and the constraints, from the input.
- KGI: the measure of the business purpose (revenue, profit and so on), with its target. The KGI is the only item whose target may be set in advance, and the business sets it. If the input has no KGI, leave a gap and ask.
- Intermediate outcomes: the behaviours the business wants more or less of in order to reach the KGI. They are usually user behaviours; in B2B they can also be decisions of customer organizations, such as renewing, churning or expanding. Phrase each one with one of five verbs: maintain, eliminate, decrease, increase, create, using the glossary's word for the working language. For example: "Increase the number of buyers who complete checkout." Rewrite vague verbs such as "improve" or "strengthen" into one of the five; if the rewrite might change the meaning, ask. Do not write formulas yet.
- When the input gives reasons behind an outcome (the top reasons customers churn, for example), use them as failure scenarios for the CUJs they point to, not as outcomes of their own, unless the input states them as goals.
- Record what the input says for later stages in `context.yaml`: statements whose subject is the product or the data, and requirements that do not come from users (laws, standards and guidelines, security risk analysis, development policies), under `hints`; telemetry, volumes, peaks, existing SLOs or SLAs and baselines under `operational_facts`. Stages 3 and 4 read them.

### 2. Users

- List every user in the input and classify each one:
  - primary: uses the system for their own goal;
  - secondary-operation: administrators, security administrators, people who enter data;
  - secondary-maintenance: people who maintain or modify the system, analyze failures, migrate or install it;
  - indirect: receives outputs without using the system.
- Mark data producers and data consumers: `data_role` for people, `system.data_producers` for systems (an OCR step, a nightly batch, an external feed). Producers matter later, because a data error can reach users without any software defect.
- Mark each user as considered or excluded. Give the reason for every exclusion.
- When a category is empty (often secondary or indirect users), do not invent anyone. Ask whether someone in that category should be considered, and give the examples you suspect.

### 3. CUJs and context of use

For each outcome, identify the users and CUJs that produce it. For each CUJ, write the user's goal and the steps. Then decide the contexts of use: device, network, user type, task, environment and any other condition that changes the result (tenant size, data volume), which are the conditions held fixed when counting. Results are comparable only within the same context of use; context coverage (step 4) is the one deliberate comparison across contexts.

When the input names only one context for a CUJ, the narrowed count and the overall count may be the same. Say so in the report rather than inventing a second context.

### 4. Quality-in-use requirements

For each CUJ, walk all five characteristics and nine sub-characteristics in `quality-in-use-model.md`. Keep a characteristic when you can write a concrete failure scenario that would hurt the outcome. List the others in the report as "not targeted", with a one-line reason, so that a later reader can tell an omission from a decision. Put characteristics set aside for the same reason in one row; one row can cover several CUJs. "Not targeted" is your judgment, not a business exclusion, so it does not need agreement, unless it drops something the input asked for: then it is an exclusion.

Write each requirement with the user as the subject: "<user> can <reach their goal> in <context of use>, <how>". If the subject turns into the product ("the system validates the address") or the data ("every order has an address"), it is not quality in use. Put it under "Hints for later stages" in the report and under `hints` in `context.yaml` for stage 3, not in a QIU.

A context coverage requirement repeats another QIU's formula in another context of use: one that the requirements name (context completeness) or did not anticipate (flexibility). Narrow the two QIUs to contexts that do not overlap, and name the base QIU in `measure.compared_with`. The gap between the two values is what you judge.

### 5. One formula, two counts

Follow `formula-and-kpi.md`:

1. Before writing any formula, fix four things: whose goal is counted; which context of use; whether the denominator contains only people who needed to do the task; and whether the product's contribution can be isolated.
2. When the user's goal and the business's goal meet in one event, write the formula A ÷ B for that event. Counted within the context of use, it is the quality-in-use measure, which goes in the QIU. Counted over everyone, it is the means KPI, which goes in a KPI item that points back to the QIU. Match the two by what A and B count, not by their names.
3. When they do not meet, say so. An outcome with no matching user event (ad revenue, or churn decided by a customer company) gets a KPI only, with an `overlap_note`. A QIU that the business does not count overall gets no KPI. Note in the report that a KPI with no overlap can move against quality in use.
4. Whether the business counts something overall is a fact about the business. When the input names no KPIs, draft a KPI only where the formula's event directly counts a stated outcome, mark it `inferred`, and ask about all of these KPIs in one question.
5. A threshold inside a formula ("within N days", "by 7:00") is part of the criterion. Take it from the input; otherwise write a named placeholder, as `conventions.md` ("Numbers") describes.
6. If the product cannot observe the event a formula needs (it happens in another system, or it needs a survey the input does not mention), keep the formula, because it says what matters. Set `observable: false` and record the gap; stage 2 decides whether a function is needed to observe it.
7. Respect the scale. Satisfaction ratings are ordinal: count the share of answers at or above a level, and never average them. Prefer proportion criteria such as "90% of users finish within T minutes".

### 6. Targets

Do not derive QIU or KPI targets from the KGI target. How much one stage moves the next is not known yet, so a target passed down from the KGI would look precise without any basis. Leave targets `undecided`, or `provisional` when the input gives a baseline, and set `needs_agreement: true` on any target you write.

### 7. Report, questions, items

Write the draft report following `report-template.md`. Ask about every uncertain point as `conventions.md` describes, apply the answers, and check the exit criteria. Then write `context.yaml` and the QIU and KPI item files.

## Exit criteria

- The KGI has a measure and a target, or the gap is recorded as an open question.
- Every outcome uses one of the five verbs and has no formula of its own.
- Every considered user has at least one CUJ, from the input or drafted from what the input says the user does. Every QIU has a context of use. Every excluded user has a reason.
- Every formula yields both a narrowed quality-in-use measure and an overall KPI, or the report says why the two do not overlap.
- Every QIU has a user as its subject and a measure with a numerator, a denominator and narrowing conditions. Every context coverage QIU names the QIU it is compared with.
- No number appears that the input does not give; every threshold not given is a named placeholder listed under "Needs agreement".

## Pitfalls

- Starting from a characteristic ("99.9% availability") before deciding whose CUJ is protected. The priorities and targets that follow then have no basis.
- Writing the formula first. Easy-to-log events (clicks, page views) creep in, and nobody checks whether they match the user's goal.
- Counting what the business gets from users (click-through, time on site) as quality in use.
- Comparing rates measured in different contexts of use, other than in a context coverage requirement that names what it is compared with.
- A denominator that includes people who never needed the task. It then measures demand or seasonality, not quality.

## Finish

Tell the user which files were written and how many items of each kind, what needs agreement, which gaps remain open, and that the next stage is `quality-in-use-to-functional-req`.
