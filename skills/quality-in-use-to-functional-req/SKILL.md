---
name: quality-in-use-to-functional-req
description: Stage 2 of 4 in the quality chain. Derives functional requirements from quality-in-use requirements and means KPIs (the output of cuj-to-quality-in-use) - the functions each CUJ step needs, plus the functions a quality-in-use need implies, such as saved details for efficiency, duplicate-charge prevention for economic risk, trace IDs for the engineers who fix failures, or reconciliation reports for accounting. Each function traces to its QIU or KPI and gets functional acceptance criteria, with every "how well" left for stage 3. When re-run, it accepts or rejects the function proposals raised by later stages. Drafts first, then asks about every uncertain point. Use it when someone wants to turn CUJs, quality-in-use requirements or KPIs into functional requirements, user stories, a backlog or acceptance criteria, or asks which features a user outcome needs. Next stage is functional-req-to-product-quality.
license: MIT
metadata:
  version: "0.1.0"
---

# Quality in use and KPIs → functional requirements

Stage 2 of the quality chain. It takes the quality-in-use requirements (QIU) and means KPIs (KPI) from stage 1 and produces functional requirements (FR): what the system does so that each user can reach their goal in their context of use. Stage 3 then specifies how well each function must behave.

Functional requirements come from two places. Some come straight from the steps of a CUJ (the cart, the payment, the confirmation). Others only appear when you ask what a quality-in-use need requires of the system: a buyer who must never be charged twice needs a duplicate-payment guard; an engineer who must find failures fast needs trace IDs. Teams that list only the first kind miss the second kind, and those functions then drop out of estimates. This stage exists to catch both.

## Read first

- `references/conventions.md`: the chain, the working language, judgment basis marks and the question protocol. Follow it exactly.
- `references/artifacts.md`: where files go, and the schemas for FR and FRP (and the stage 1 items you read).
- `references/deriving-functions.md`: how each quality-in-use characteristic turns into functions, and how to keep "what" apart from "how well".
- `references/report-template.md`: the report layout and a worked example.
- `references/glossary.md`: terms for the working language.

## Inputs

- `docs/quality/context.yaml`, `qiu/`, `kpi/` from stage 1, and its report `1-quality-in-use.md`, whose open gaps often point at functions. If they do not exist, accept the same information from the user (users, CUJs, quality-in-use requirements with their contexts of use) and record its source in the report.
- The original inputs listed in the stage 1 report's "Inputs" table. They are where you learn what the product already does.
- Any existing functional specification, feature list or backlog. Map onto it instead of duplicating it.
- `context.yaml → hints`. A hint whose subject is the product can describe a function (flag an address that does not exist); take the function and leave the "how well" for stage 3. Hints that come from laws, standards, risk analysis or policies are stage 3's to take in; when you expect them to need functions (a search the law requires, a change history), list them under open gaps, because they will come back as proposals.
- Open function proposals in `docs/quality/frp/` (status `proposed`), raised by stages 3 and 4.

Essential inputs: at least one CUJ with its steps, and at least one quality-in-use requirement. If either is missing, ask for it (or suggest running `cuj-to-quality-in-use`) before drafting.

## Workflow

### 1. Functions from CUJ steps

For each CUJ, take each step and write the functions the system performs in it: what the user can do and what the system does in response.

- Whether a function already exists is a fact about the product. Take it from a feature list, a specification or the original inputs, and map onto the existing function (`existing: true`) rather than creating a new one. When the inputs do not say, draft your reading and ask about all such FRs in one question.
- A step performed outside the system (in another system, or by hand) gets no function of its own. Under that step, write the functions the system performs to support it (the file another system imports, the list of mismatches), and say in the report that the step itself is outside the boundary.

### 2. Functions from quality-in-use requirements

For each QIU, ask: what must the system do so that this user can reach this goal in this context of use? Use the heuristics per characteristic in `deriving-functions.md`. There are three possible answers, and the third is common:

- A new function is needed (for example, saved cards for repeat-purchase efficiency).
- An existing function needs to be extended (for example, cancellation added to order history). Write the extension as its own FR with `existing: false` and `extends` naming the existing FR, so that the new work is visible.
- No new function is needed: the quality attaches to a function that already exists. "Checkout without being kept waiting" needs no new function; it needs the existing checkout to be fast, which is stage 3's job. Record this in the coverage table in the report, so that the QIU does not look forgotten.

Secondary and indirect users' QIUs produce functions too: diagnostic and tracing functions for maintainers, entry and validation functions for the people who enter data, reports and reconciliation outputs for indirect users.

Measures need data. When a QIU or KPI has `observable: false`, or its measure has no data source in the inputs, decide whether a function should produce the data (record the reconciliation result, ask a one-question survey in the product, emit an event at the step). Such a function traces to that QIU or KPI like any other. If the data can come from elsewhere (existing analytics events, a survey run outside the product), no function is needed; say so in the coverage table.

Record the candidates you considered and did not add (notifications, bulk actions, defaults) under "Functions considered and not added", with a one-line reason, so a reviewer can add them back. When you cannot tell from the inputs whether a QIU needs a candidate, it is an uncertain point: ask.

### 3. Write each FR as "what", never "how well"

- The subject is the system: "The system <does what> when <trigger>."
- Acceptance criteria describe observable behaviour, including error paths (see `deriving-functions.md`). Use Given/When/Then or a short checklist.
- The criteria of an existing FR describe the behaviour the inputs describe. Do not add behaviour you only suppose it has: if a behaviour is needed and the inputs do not show that the product has it, it is new work, so write it as an extension.
- Move every "how well" out of the FR into `quality_hints`: response times, availability percentages, "zero errors", freshness, deadlines. "Calculates the order total" is a function; "never miscalculates the total" is a quality requirement for stage 3.
- A function with a tunable threshold (low reading confidence, near-duplicate claims) names the threshold as a placeholder in the criterion ("confidence below C_reading") and lists it under "Needs agreement", as `conventions.md` ("Numbers") describes.
- Record the interface (screen, API, job, event, file). Use the name the inputs give; otherwise write only its kind, which you can tell from the step ("screen: approver web"), and leave the name to stage 4, which asks for the interfaces it measures.

### 4. Function proposals from later stages

On a re-run, process every FRP whose status is `proposed`. Leave the other FRs as they are, unless an accepted proposal reaches them.

- Accept by changing the FR named in `changes`, or by writing a new FR, whichever keeps each function in one FR. A changed FR gets a new version and a changelog entry that cites the FRP. A new FR has `origin: proposal` and `proposal: <FRP ID>`; it traces to the QIUs that the proposing QRs trace to, and to any KPI whose count it produces. A QR that comes from a law, a standard or a policy traces to no QIU; the FR's `traces_to` is then empty and `proposal` carries the trace. Set `cuj` and `cuj_step` to the main step where the function acts, or to null when it acts on no single step, and write its quality hints as "<QR ID> - <what must hold>".
- Then check the accepted function against the other FRs. Change any FR that now contradicts it, and any line under "Out of scope" or "Functions considered and not added" that it overturns.
- Reject with the reason in `resolution` (for example, "existing FR-04 already does this", or "only a business procedure can meet this need", which also goes in "Out of scope"). A rejection because the function is not worth building this cycle is an exclusion: set `needs_agreement: true` and list it under "Needs agreement". Say in the report which QR is left without the function it needed.
- Set the FRP's `status` to `accepted` or `rejected`, bump its version, and add a changelog entry.
- If you are unsure whether the proposal is worth a function, draft your decision and ask.

Finally, tell the user which later-stage items the decisions affect, using each FRP's `affects`: new and changed FRs and QRs left without a function (re-run `functional-req-to-product-quality`), and the NFRs that waited for a proposal (then re-run `product-quality-to-slo`).

### 5. Untraced functions

If the input lists features that trace to no CUJ step, QIU or KPI, do not create FRs for them and do not drop them silently. List them in the report under "Untraced functions" and ask whether a user or outcome is missing upstream, or whether they are out of scope.

### 6. Report, questions, items

Write the draft report following `report-template.md`. Ask about every uncertain point as `conventions.md` describes, apply the answers and check the exit criteria. Then write the FR items and update the FRP items.

## Exit criteria

- Every QIU is covered by at least one FR, or the coverage table says which existing FR carries it as quality only. Every measure without a data source has a function that produces the data, or the coverage table says where the data comes from.
- Every FR traces to a CUJ step, a QIU or KPI, or an accepted proposal.
- No FR statement or acceptance criterion contains a "how well" criterion; those are in `quality_hints`.
- Every new FR has acceptance criteria that include at least one error path, where an error path exists.
- Every FRP that was `proposed` is now accepted or rejected, with a reason, and no accepted function contradicts another FR.
- The report's tables and the FR items agree: same IDs, same statements, same traces.

## Pitfalls

- Listing only the functions of the CUJ steps. The functions implied by quality (unique payment keys, trace IDs, reconciliation) then never reach the backlog or the estimates.
- Writing quality as a function. "Zero calculation errors" or "responds within 1.5 s" in an FR hides a quality requirement where nobody will measure it.
- Turning every QIU into a new feature. Often the honest answer is that no new function is needed and the quality attaches to an existing one.
- Adding features that nobody upstream needs. A function without a trace is scope creep until someone names the user it serves.

## Finish

Tell the user which files were written or updated, how many FRs are new and how many existing, how many came from proposals, what needs agreement, which questions remain open, and that the next stage is `functional-req-to-product-quality`.
