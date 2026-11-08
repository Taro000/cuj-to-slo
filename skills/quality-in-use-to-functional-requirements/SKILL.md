---
name: quality-in-use-to-functional-requirements
description: Stage 2 of 4 in the quality chain. Derives functional requirements from quality-in-use requirements and means KPIs (the output of cuj-to-quality-in-use) - the functions each CUJ step needs, plus the functions a quality-in-use need implies, such as saved details for efficiency, refusing a second charge for the same order for economic risk, letting engineers follow one failed request end to end, or a reconciliation report for accounting. Each function states behaviour seen from outside the system, never a mechanism, traces to its QIU or KPI and gets acceptance criteria; every "how well" is left for stage 3. When re-run, it accepts or rejects later stages' function proposals. Drafts first, then asks only about points of high breadth or severity and records the rest as assumptions. Use it to turn CUJs, QIUs or KPIs into functional requirements, user stories, a backlog or acceptance criteria, or to find which features a user outcome needs. Next stage is functional-requirements-to-product-quality.
license: MIT
metadata:
  version: "0.3.0"
---

# Quality in use and KPIs → functional requirements

Stage 2 of the quality chain. It takes the quality-in-use requirements (QIU) and means KPIs (KPI) from stage 1 and produces functional requirements (FR): what the system does so that each user can reach their goal in their context of use. Stage 3 then specifies how well each function must behave. How the system does it is left to design.

Functional requirements come from two places. Some come straight from the steps of a CUJ (the cart, the payment, the confirmation). Others only appear when you ask what a quality-in-use need requires of the system: a buyer who must never be charged twice needs the system to refuse a second charge for the same order; an engineer who must find failures fast needs a way to follow one failed request end to end through the system. Teams that list only the first kind miss the second kind, and those functions then drop out of estimates. This stage exists to catch both.

## Read first

- `references/conventions.md`: the chain, requirements versus design, shared facts, the working language, judgment basis marks, the question protocol, assumptions and how to finish a run. Follow it exactly.
- `references/artifacts.md`: where files go, and the schemas for FR, FRP and QAS (and the stage 1 items you read).
- `references/deriving-functions.md`: how each quality-in-use characteristic turns into functions, and how to keep "what" apart from "how well" and from "how".
- `references/report-template.md`: the report layout and a worked example.
- `references/glossary.md`: terms for the working language.

## Inputs

- `docs/quality/context.yaml`, `qiu/`, `kpi/` from stage 1, and its report `1-quality-in-use.md`, whose open gaps often point at functions. If they do not exist, accept the same information from the user (users, CUJs, quality-in-use requirements with their contexts of use) and record its source in the report.
- The shared facts in `context.yaml`: constraints (CON), external systems (EXT) and operational facts (OPF). Refer to them by ID. The design inputs (DIN) are kept for design; no FR refers to one.
- The original inputs listed in the stage 1 report's "Inputs" table. They are where you learn what the product already does.
- Any existing functional specification, feature list or backlog. Map onto it instead of duplicating it.
- `context.yaml → hints`. A hint whose subject is the product can describe a function (flag an address that does not exist); take the function and leave the "how well" for stage 3. Hints that come from laws, standards, risk analysis or policies are stage 3's to take in; when you expect them to need functions (a search the law requires, a change history), list them under open gaps, because they will come back as proposals.
- Open function proposals in `docs/quality/frp/` (status `proposed`), raised by stages 3 and 4.
- Open assumptions in `docs/quality/qas/` that your judgments depend on, whichever stage wrote them (`conventions.md`, "Assumptions").
- On a re-run, the values that stage 4 proposed for placeholders in this stage's FRs (its report's "Placeholders", with the QAS or the answer behind each). Write them into the criteria and quality hints that use them, and list that QAS in the FR's `premises`.

Stage 2 adds or corrects shared facts itself (`conventions.md`, "Shared facts"). When an input or an answer gives a system outside the boundary, a constraint or a fact about the running product (records that already exist) that `context.yaml` lacks, write a new EXT, CON or OPF entry with the next ID, a `source` naming the input or the question ("stage 2, question 3") and `basis: stated` or `confirmed`; an FR's `interface` and `premises` then cite the EXT ID. A design decision that `design_inputs` does not hold becomes a new DIN entry, and stays out of every FR. A system or constraint that you only infer adds a shared fact, which is high in breadth: ask about it, and until it has an ID, name the system by its role in `interface`. The rest of `context.yaml` (system, KGIs, outcomes, users and their data roles, CUJs, hints) stays stage 1's: list a gap there under "Open gaps" for stage 1.

Essential inputs: at least one CUJ with its steps, and at least one quality-in-use requirement. If either is missing, ask for it (or suggest running `cuj-to-quality-in-use`) before drafting.

## Workflow

### 1. Functions from CUJ steps

For each CUJ, take each step and write the functions the system performs in it: what the user can do and what the system does in response.

- Whether a function already exists is a fact about the product. Take it from a feature list, a specification or the original inputs, and map onto the existing function (`existing: true`) rather than creating a new one. When the inputs do not say, draft your reading. Several FRs usually hang on the same reading of the inputs, so rate it as one point: when the rating calls for a question, ask about all such FRs in one multi-select question, by groups of functions when there are many; otherwise record the reading as an assumption (QAS) in the `premises` of those FRs. When one fact settles them all (nothing has been built yet), ask that fact once.
- A step performed outside the system (in another system, or by hand) gets no function of its own. Under that step, write the functions the system performs to support it (the file another system imports, the list of mismatches), and say in the report that the step itself is outside the boundary.

### 2. Functions from quality-in-use requirements

For each QIU, ask: what must the system do so that this user can reach this goal in this context of use? Use the heuristics per characteristic in `deriving-functions.md`. There are three possible answers, and the third is common:

- A new function is needed (for example, saved cards for repeat-purchase efficiency).
- An existing function needs to be extended (for example, cancellation added to order history). Write the extension as its own FR with `existing: false` and `extends` naming the existing FR, so that the new work is visible.
- No new function is needed: the quality attaches to a function that exists, or that another step already needs. "Checkout without being kept waiting" needs no new function; it needs the checkout to be fast, which is stage 3's job. Record this in the coverage table in the report, so that the QIU does not look forgotten. In a product not built yet, the FR that carries the quality is new, not existing; the coverage table says so.

Secondary and indirect users' QIUs produce functions too: ways for maintainers to find and follow failures, entry and validation functions for the people who enter data, reports and reconciliation outputs for indirect users.

Measures need data. When a QIU or KPI has `observable: false`, or its measure has no data source in the inputs, decide whether a function should produce the data, and write it as behaviour: "the system records that a buyer reached the payment step", "the system asks one question at the end of the task", "the system lets accounting record the result of each reconciliation". Never write it as an event, a log line or a field; those are design. Such a function traces to that QIU or KPI like any other, and stage 1 then updates the item's `observable` and its open gap: tell the user to re-run stage 1 for it. If the data can come from elsewhere (telemetry or records that already exist, which are OPF entries, or a survey run outside the product), no function is needed; say so in the coverage table. In a product not built yet, no measure has a data source in the inputs: group the recording into as few functions as the measures allow (one that records how each request progresses can serve several QIUs and KPIs), each tracing to every QIU and KPI it serves. A source that you learn of in this stage is a new OPF entry (see "Inputs").

Record the candidates you considered and did not add (notifications, bulk actions, defaults) under "Functions considered and not added", with a one-line reason, so a reviewer can add them back. When you cannot tell from the inputs whether a QIU needs a candidate, rate the point (`conventions.md`, "Draft first, ask afterwards"): ask when either rating is high; otherwise keep your drafted choice and record it as a QAS, which the FRs serving that QIU list in `premises`. Leaving a candidate out because it is not worth building this cycle is a business decision (`kind: decision`).

### 3. Write each FR as "what": never "how well", never "how"

Stage 3 decides how well a function behaves, and design decides how the system makes it happen. An FR stays clear of both.

- The subject is the system: "The system <does what> when <trigger>."
- Acceptance criteria describe behaviour observable from outside the system, including error paths (see `deriving-functions.md`). Use Given/When/Then or a short checklist.
- The criteria of an existing FR describe the behaviour the inputs describe. Do not add behaviour you only suppose it has: if a behaviour is needed and the inputs do not show that the product has it, it is new work, so write it as an extension.
- Not how well. Move every "how well" out of the FR into `quality_hints`: response times, availability percentages, "zero errors", freshness, deadlines. "Calculates the order total" is a function; "never miscalculates the total" is a quality requirement for stage 3.
- Not how. The statement and its criteria say what someone outside the system can see, not the mechanism that produces it: no keys, tokens, IDs carried between services, caches, retries, queues, or batch versus event. A mechanism is one design among several; written here, it is copied downstream, and every copy has to be rewritten when design picks another.
  - Before: "The system attaches a unique key to every payment request and refuses a key it has already seen."
  - After: "The system charges an order at most once, even when the payment request for it is sent again."
- A function with a tunable threshold (low reading confidence, near-duplicate claims) names the threshold as a placeholder in the criterion ("confidence below C_reading") and lists it under "Placeholders" in the report (`conventions.md`, "Numbers").
- Record the interface by kind and for whom: "screen: buyer checkout", "notification: driver", "file: for accounting". Name each external system by its EXT ID. Never write a path, an endpoint or a protocol name for a new function, even when an input suggests one: that is a design input (DIN). The interface of a function that already exists in a running product may be named as it exists. Quote list items that contain ": " (`interface: ["screen: buyer checkout", EXT-01]`).
- List in `premises` the shared facts and assumptions the FR relies on, including the EXT IDs in its interface. An FR whose premises include an open QAS has `basis: assumed`.

### 4. Function proposals from later stages

On a re-run, process every FRP whose status is `proposed`. Leave the other FRs as they are, unless an accepted proposal reaches them.

- Accept by writing a new FR, or by changing the FR named in `changes`, whichever keeps each function in one FR. An FR that already exists in the product (`existing: true`) keeps describing what the product does (step 3), so a change to it becomes an extension: a new FR whose `extends` names it. A new FR (`existing: false`) is changed in place: it keeps its ID and cites the FRP in `basis_note`. A new FR, extensions included, has `origin: proposal` and `proposal: <FRP ID>`; it traces to the QIUs that the proposing QRs trace to, and to any KPI whose count it produces. A QR that comes from a law, a standard or a policy traces to no QIU; the FR's `traces_to` is then empty and `proposal` carries the trace. Set `cuj` and `cuj_step` to the main step where the function acts, or to null when it acts on no single step, and write its quality hints as "<QR ID> - <what must hold>". Write the FR as behaviour (step 3), even when the proposal is worded as a mechanism.
- Then check the accepted function against the other FRs. Change any FR that now contradicts it, and any line under "Out of scope" or "Functions considered and not added" that it overturns.
- Reject with the reason in `resolution` (for example, "existing FR-04 already does this", or "only a business procedure can meet this need", which also goes in "Out of scope"). Say in the report which QRs in its `origin` are left without the function they needed.
- Whether a proposed function is worth building this cycle is a business decision (`conventions.md`, "Decisions"). A proposal whose need the user has already settled (a QR they emphasized, a confirmed constraint, an answer in a later stage) is accepted without a question or a QAS: cite that answer in `resolution`. Rate any other drafted decision like any other point; a rejection that takes protection away from a quality a CUJ emphasizes is high in severity. When either rating is high, ask, and the answer is the decision. Otherwise adopt your draft provisionally: write a QAS with `kind: decision`, list it in the FRP's `premises` and set the FRP's `basis` to `assumed`. With `status` and `resolution`, these are the only fields of a proposal that stage 2 writes. When the provisional decision accepts the proposal, list the QAS in the `premises` of the FR it becomes as well.
- Set the FRP's `status` to `accepted` or `rejected`, and write its `resolution`.

Finally, tell the user which items the decisions affect, and which stages to re-run for them: the new and changed FRs (re-run `functional-requirements-to-product-quality`); the QRs in each proposal's `origin`, which get the function or are left without it (re-run the stage that raised the proposal, named in `proposed_by`); and the items in each proposal's `affects`, which waited for the decision (re-run the stages that wrote them, usually `product-quality-to-slo`).

### 5. Untraced functions

If the input lists features that trace to no CUJ step, QIU or KPI, do not create FRs for them and do not drop them silently. List them in the report under "Untraced functions" and ask whether a user or outcome is missing upstream, or whether they are out of scope. A missing user or CUJ would change stage 1, so the point is high in breadth.

### 6. Report, questions, items

Write the FR items, and the FRP updates, as you draft them, and the draft report that indexes them, following `report-template.md`. Then list the points you were unsure of, rate them, ask about those that call for a question and record every other one as a QAS, as `conventions.md` describes ("Draft first, ask afterwards", "Decisions", "Assumptions"). Apply the answers to the items in place, check the exit criteria, and finish the run (`conventions.md`, "Finishing a run").

## Exit criteria

- Every QIU is covered by at least one FR, or the coverage table says which FR carries it as quality only. Every measure without a data source has a function that produces the data, or the coverage table says where the data comes from.
- Every FR traces to a CUJ step, a QIU or KPI, or an accepted proposal.
- No FR statement or acceptance criterion contains a "how well" criterion; those are in `quality_hints`.
- No FR statement, acceptance criterion or interface names a mechanism, a path or endpoint of a new function, a platform, a product or a data store.
- Every new FR has acceptance criteria that include at least one error path, where an error path exists.
- Every FRP that was `proposed` is now accepted or rejected, with a reason in `resolution`, and no accepted function contradicts another FR.
- Every item that depends on an open QAS lists it in `premises` and has `basis: assumed`.
- Every shared fact this stage learned is in `context.yaml` with its source, and every gap in stage 1's part of `context.yaml` is under "Open gaps".
- The report and the items agree (same IDs, titles, traces and basis), and the report restates no item's text.

## Pitfalls

- Listing only the functions of the CUJ steps. The functions implied by quality (refusing a second charge for the same order, following a failed request end to end, matching orders against payments) then never reach the backlog or the estimates.
- Writing quality as a function. "Zero calculation errors" or "responds within 1.5 s" in an FR hides a quality requirement where nobody will measure it.
- Writing a mechanism as a function. Keys, tokens, retries, queues and endpoint paths choose a design before design starts. When design chooses another way, every FR that copied the choice has to be rewritten, and until then nobody can check the FR from outside the system.
- Turning every QIU into a new feature. Often the honest answer is that no new function is needed and the quality attaches to an existing one.
- Adding features that nobody upstream needs. A function without a trace is scope creep until someone names the user it serves.

## Finish

Finish the run as `conventions.md` describes ("Finishing a run"): list the open assumptions as a skim list that needs no answer, tell the user which stages to run or re-run and for which items (stage 1 too, when this run added a shared fact or a function that produces the data a QIU or KPI lacked), commit the files this run wrote, and apply and commit again any correction that arrives after the commit. Also say which files were written or updated, how many FRs are new and how many existing, and how many came from proposals. The next stage is `functional-requirements-to-product-quality`.
