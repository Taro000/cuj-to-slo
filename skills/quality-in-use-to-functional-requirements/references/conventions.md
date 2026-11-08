# Conventions shared by the quality chain skills

Four skills form one chain. Each reads the artifacts of the stages above it and writes its own.

| Stage | Skill | Reads | Writes |
|---|---|---|---|
| 1 | `cuj-to-quality-in-use` | users, CUJs and requirements given by the user | `context.yaml`, quality-in-use requirements (QIU), means KPIs (KPI), assumptions (QAS) |
| 2 | `quality-in-use-to-functional-requirements` | context, QIU, KPI, open function proposals (FRP) | functional requirements (FR), FRP resolutions, QAS, shared facts it learns |
| 3 | `functional-requirements-to-product-quality` | context, QIU, KPI, FR, FRP, requirements that do not come from users | product and data quality requirements (QR), FRP, QAS, shared facts it learns |
| 4 | `product-quality-to-slo` | context, QIU, FR, FRP, QR, operational facts | non-functional requirements (NFR), SLI definitions (SLI), error budget policies (EBP), FRP, QAS, shared facts it learns |

An upper stage is the reason for the stages below it; a lower stage is the means of achieving the stages above it. Every item traces up to the item it serves, so anyone can walk from an SLO back to the user it protects.

Stages 3 and 4 never add or change functional requirements themselves. When a quality requirement needs a function that no FR provides, or needs an FR to behave differently (its acceptance criteria let through what the requirement forbids), they write a function proposal (FRP). The user re-runs stage 2, which accepts or rejects each proposal, and then re-runs the later stages for the affected items. This keeps every function in one list, so none of them drops out of estimates.

An FRP states one function, or one change to one FR, as what the system does. Its title and statement say the same thing, and it does not depend on a measurement definition (an SLI). It lists the later-stage items that depend on the decision, so that stage 2 can say what a rejection breaks; a later stage whose items come to depend on an open proposal adds them to its `affects`. The proposing stage writes the proposal; stage 2 writes only its `status` and `resolution`, and, when its decision is provisional, the QAS in its `premises` and its `basis`. A stage whose proposal stage 2 has resolved takes the result in on its next run ("Re-running a stage").

## Scope

These skills produce software requirements for the system inside the boundary in `context.yaml`. When a need can be met only outside the software (business procedures, operations, facilities, training), or only by another system outside the boundary (a customer's own accounting system, for example), write one line in the report's "Out of scope" section saying what and why, and do not turn it into a requirement. Recording it keeps the gap visible without pretending the software will close it.

## Requirements, not design

The chain says what the system must do and how well, in the terms of its users and its domain. How the system does it is decided later, by design. A design choice made here gets copied into many items, the design then overturns it once it weighs the choice properly, and every copy has to be rewritten.

The rule: record what already exists; never choose, and never ask about, what does not exist yet.

- What exists is a fact: the kinds of device users carry and the situations they use them in, systems outside the boundary that the business already uses, the current behaviour and telemetry of a product that is already running, contracts and laws. Record it.
- What does not exist yet is design: the platform the product runs on (native or web, operating systems, browsers, versions), products and vendors, frameworks, the names and protocols of interfaces (paths, endpoints, topics), data stores, tables and fields, mechanisms (keys, tokens, trace IDs, caches, queues, retries), measurement tooling and log fields, deployment, regions, and when the servers run. Do not choose it, do not ask about it, and do not draft assumptions about it.
- When the input states a design decision anyway ("payments go through <vendor>", "no native app"), record it once under `design_inputs` in `context.yaml`, for the design chain, and copy it into no item. When the decision carries a property that matters to the requirements, write that property as a constraint in requirement terms ("payments go through an external payment service; no real money moves").
- Name systems outside the boundary by their role ("the external map service"). A product name, when the input gives one, is a design input.
- Write a context of use with the kind of device and the situation (smartphone, one hand, outdoors, unstable network), never with platforms or versions.
- A requirement states behaviour that can be observed from outside the system, not the mechanism that produces it: "the system charges an order at most once, even when the payment request is sent again", not "the system attaches a unique key to every payment request". What users see and act on is behaviour, even when it looks like a mechanism (a code the passenger reads out to the driver, a screen they confirm); what they never see is mechanism.
- When a judgment seems to need a design that does not exist yet (how likely a failure is, where a measure can be taken), make it from the requirement-level facts you have, and say in `basis_note` what the design must confirm. Do not ask about the design.

## Working language

- Ask questions in the language the user writes in.
- Write artifacts in the language of the project's existing documentation. If the project has none, use the user's language.
- Keep YAML keys, IDs and enum values in English exactly as `artifacts.md` defines them, so that every stage can read every other stage's files. Write free-text values in the working language.
- Use the terms in `glossary.md` for the working language, and use each one consistently. Characteristic names are a vocabulary, not prose: never paraphrase them.
- In reports, write the basis marks and statuses with the glossary's terms for the working language. In YAML, use the English enum values.

## Judgment basis

Mark every judgment with where it came from:

- `stated`: written in the input or in an upstream artifact. Rewording that keeps the meaning (into one of the five outcome verbs, for example) is still `stated`.
- `inferred`: your own judgment, made with enough evidence that you are not unsure. Anything the input does not say is `inferred` or `assumed`, even when it seems obvious.
- `assumed`: your own judgment that you were unsure about and did not ask about, because it fell below the bar for questions ("Draft first, ask afterwards"). It is recorded as an assumption (QAS), and every item that depends on it lists the QAS in `premises`.
- `confirmed`: the user answered a question about it, or gave the value in an answer or a correction, even a value they call provisional (say so in `basis_note`).

Never present an `inferred` or `assumed` judgment as if it were `stated`. People tend to accept a confident-looking draft without checking it; the basis mark tells them where to look.

An item holds several judgments (a CUJ's goal, its steps, its outcome), and its `basis` is the weakest of them, from weakest to strongest: `assumed`, `inferred`, then `stated` and `confirmed`. An item is `confirmed` only when every judgment in it that is not stated has been answered. When the parts differ, say in `basis_note` which parts are stated, inferred, assumed and confirmed. In the later stages most items stay `inferred`: they are proposals built on the stages above.

## Shared facts

A fact that several items depend on lives in one place, `context.yaml`, with an ID, and items refer to it by its ID instead of copying it. When the fact changes, one entry changes, and the items to revisit are exactly those that refer to it.

- `constraints` (CON-01 …): requirement-level constraints, such as a contract or a business decision ("this is a demo; no real money moves").
- `external_systems` (EXT-01 …): systems outside the boundary that the product exchanges data with or calls, named by their role.
- `operational_facts` (OPF-01 …): volumes, peaks, when the product is used, baselines, existing SLOs or SLAs, and facts about a product that is already running: its telemetry, dependencies, data stores and incident history.
- `design_inputs` (DIN-01 …): design decisions stated in the input, kept for the design chain. No item refers to them.

Stage 1 records the shared facts it finds. A later stage that learns a new fact (from the user, or from an input it reads, such as a design decision in a policy), or a correction to one, adds or updates the entry itself, so that its items can cite it: a new entry takes the next ID, its `source` names the input or the question ("stage 3, question 2"), its `basis` is `stated` or `confirmed`, and the report's "Decisions" row or "Inputs" table names it. The items that cite a corrected entry are then revisited, in every stage, earlier ones included. The rest of `context.yaml` (system, KGIs, outcomes, users and their data roles, CUJs, hints) belongs to stage 1 alone: a later stage that finds a gap there lists it under "Open gaps" for stage 1. An entry is never deleted and its number never reused; one that no longer holds gets `status: retired`.

An item lists in `premises` the shared facts and the assumptions it relies on directly; it does not copy the premises of the items it traces to, which are found by following the traces. Refer to a shared fact by its ID, and copy neither its values nor its wording beyond a short label ("up to the sale peak, OPF-03"); a reader finds the fact in `context.yaml`. A number computed from a fact (an error budget volume from a daily volume) may be written in the item, with the fact in `premises`, so that it is recomputed when the fact changes.

## Numbers

Never invent a number. When a requirement needs a threshold that the inputs do not give (a duration, a deadline, a count or a share inside its statement), write a named placeholder and list it in the report under "Placeholders". A target needs no placeholder: it lives in the item's `target` and `target_status` fields, `undecided` until a value is proposed. Give each placeholder a name that no other placeholder in the chain uses, such as `T_repeat` or `N_onboarding_days`, and say what it stands for. A number taken from the inputs keeps its source. Only stage 4 proposes values for placeholders. It lists every placeholder still open anywhere in the chain (in QIU measures, FR criteria, FRPs, QRs and its own items) under "Placeholders", with a proposed value and what it is based on, or the role that must set it (a law, a contract). Each proposed value is a business decision ("Decisions"). A value used in stage 4's own items is written there; a value for a placeholder in an earlier stage's item is written by that stage on its next run, from the answer or the QAS, and stage 4 tells the user which stages to re-run. Write the value after the name ("T_repeat (60 s)"), so that every use can still be found by the name, and move a value written the other way round into this form when you touch the item. A value that rests on an open QAS keeps the QAS in the item's `premises`, and the item stays `assumed`. A QIU or KPI target may start as provisional in stage 1 only when the input gives a baseline for it.

A proposed value is a business decision ("Decisions"). Asking the user for a number nobody has measured does not make it surer: ask instead for the facts that would settle it (a baseline, a volume, an existing contract), or, when the value is asked about as a decision, whether they accept the value you propose and what it is based on.

## Draft first, ask afterwards

1. Read every input and every existing artifact under the artifacts directory. On a re-run, start from what changed ("Re-running a stage").
2. If an essential input is missing, so that no meaningful draft is possible (each skill names its essential inputs), ask only for that input before drafting. This is a request for input, not a judgment question.
3. Draft the whole stage without stopping. Make every judgment yourself, using the rules in the skill. Write the items as you draft them, and the draft report that indexes them. Work in parts small enough to write as you go (one CUJ at a time, for example), and write each part's items before you start the next: a stage worked out in full before the first file is written saves nothing if the run stops.
4. List every point you were unsure of. A point is uncertain when:
   - the input is silent or ambiguous about a fact the judgment depends on, and the choice changes this stage or a later one;
   - two or more reasonable options exist, and the wrong one would cause rework downstream;
   - inputs contradict each other or an upstream artifact;
   - it depends on facts that only the user's organization knows: traffic, legal obligations, roles, existing systems;
   - it is a business decision ("Decisions").

   A judgment you make with the skill's rules from what the inputs state is not uncertain only because the inputs did not make it for you: it stays `inferred`. Never list anything the input already states, wording, how the method works, or a design choice ("Requirements, not design"). Merge points that hinge on the same fact.
5. Rate each point on two scales:
   - Breadth, read from the structure rather than guessed:
     - high: it changes the system boundary, a user, a CUJ or its steps, an outcome, a KPI or a KGI, or adds or changes a shared fact; or five or more items depend on it, counting the items of later stages through their traces once those exist;
     - medium: two to four items depend on it;
     - low: one item depends on it.
   - Severity, the harm if the draft is wrong and nobody notices until later:
     - high: legal, safety, financial or personal-data harm; something hard to reverse later (a commitment to someone outside, the shape of data that others rely on); or protection taken away from a CUJ (from stage 3 on, from a quality the CUJ emphasizes);
     - medium: several items would have to be redone, but no harm reaches users or the business;
     - low: easy to correct later, such as a minimum-line item or a value that a later stage proposes anyway.
6. Ask about a point when either rating is high. Ask also, whatever the ratings, about a contradiction between inputs or between an input and an upstream artifact, about the content of a law, standard, contract or policy that the inputs name but do not give, when a judgment in this stage depends on it, and about the business decisions that "Decisions" says are always asked. There is no fixed number of questions; the ratings decide. Every other point becomes an assumption ("Assumptions"): keep the drafted judgment, mark it `assumed`, and write a QAS item.
7. Ask in order of breadth × severity, high and high first, grouped by theme. For each question, give what you drafted, why you are unsure, and the options, with your drafted answer first. When a question asks for a fact (a volume, a name), the drafted option says what the draft does without it ("left as a formula"), and the user types the value.
   - When several items share one yes-or-no judgment ("which of these functions already exist?"), ask it as one multi-select question rather than one question per item.
   - If a structured question tool is available (for example, AskUserQuestion in Claude Code), ask up to 4 questions per call with 2 to 4 options each, put your drafted option first and label it as recommended, and call the tool again for the next batch. Ask multi-select questions with up to 4 items each, splitting longer lists over several questions.
   - Otherwise, ask in chat as a numbered list and wait for the answers.
8. Apply the answers and mark the affected judgments `confirmed`. If an answer overturns a premise, redo the affected part of the draft, then rate the new uncertain points the same way. A redone item keeps its ID while it serves the same purpose, even when its content changes. If an answer drops a drafted item, set its `status: retired`, so that its number is never used again.
9. Check the stage's exit criteria and set the report's status to "answers applied". The items are the source of truth throughout, and the report must match them.
10. Finish the run ("Finishing a run").

## Decisions

Priorities, targets and thresholds, exclusions ("excluded this cycle") and error budget policies are business decisions. A priority list that nobody decided gets overturned at the first incident. Rate a business decision like any other point:

- If either rating is high, ask the user to decide. The answer is the decision: mark the judgment `confirmed` and record the question and its answer under "Decisions" in the report.
- Otherwise, adopt the drafted decision provisionally: write a QAS item with `kind: decision`, and mark the judgments that depend on it `assumed`.
- An error budget policy is always asked: it binds every SLO it applies to, and a policy nobody decided is not followed on the day the budget runs out.
- The emphasized set of each CUJ (stage 3) is always asked: stage 4 sets its SLOs on those requirements, and a set drafted without the business is wrong too often to leave to the skim list. Ask which sets are wrong, up to three CUJs per multi-select question: each option names a CUJ and the QRs it chose, and a fourth option says that all of them are right. Then ask what each set marked wrong should be.

Never present a provisional decision as decided.

## Assumptions

An assumption (QAS) records one judgment made without asking: what was drafted, why it was uncertain, and its ratings. Each one is a file in `qas/`. The QAS files are shared by the whole chain: any stage may record an answer in one.

- Every item that depends on an assumption lists it in `premises`. Find the dependent items by searching `premises`; a QAS does not list them.
- A QAS is `open` until someone settles it: `confirmed` when the user says it holds, `overturned` when the user says it does not, `retired` when nothing depends on it any more, answered or not (another answer can make it moot). A settled QAS stays in its dependents' `premises`, so that they can still be found; their basis follows the answer. A correction that changes part of an assumption sets `overturned`, records the change in `answer`, and leaves the parts it does not mention as they were drafted, now held. The run that retires the last item depending on a QAS retires the QAS too.
- On every run, re-rate the open assumptions that this stage's judgments depend on, and update their `breadth`, `severity` and `rating_note` in place, whichever stage wrote them. When a judgment you would ask about depends on an open assumption, or items now depend on an assumption so widely that its breadth is high, ask about the assumption in this run, whichever stage wrote it, and record the question and its answer under "Decisions" like any other. Write the answer in the QAS (`answer`, `answered_in`) and set its status. If the answer overturns it, redo this stage's dependent items. Whether it holds or not, tell the user to re-run the stage that wrote it, so that its items and report follow the answer.
- When the stage that wrote an assumption runs again, it applies the answer recorded in the QAS to its own items: the dependent judgments become `confirmed`, or are redone when the assumption was overturned.
- The design chain cannot write here. When its report lists an answer to a QAS under "Issues for the quality chain", the stage that wrote the QAS applies it on its next run like an answer: a row under "Decisions", and `answered_in: "design stage N, question M"` in the QAS.

## Current state only

Reports and items describe what holds now. They never record how they got there: no sections about earlier runs, no lists of changes, no changelogs, no version numbers, no "what changed" columns, no dates of changes inside items. The history lives in git, and the reasons for each change live in its commit message ("Finishing a run").

- When an answer overturns an earlier decision, replace the earlier row under "Decisions" and keep its number, so that citations of it stay valid; do not keep both.
- The report's header gives the date it was updated and the commit at which its inputs were read (`Inputs as of`): the commit that was current when the run started, or "none" outside git.

## Re-running a stage

- Find what changed since this stage last ran. Its last run is the last commit whose first line names this skill (`git log -1 --format=%h --grep='^<skill name>' -- <report>`). List the files under the artifacts directory that changed after that commit, and read their diffs (`git diff --name-status <commit> -- docs/quality/`, then `git diff <commit> -- <file>`), together with any uncommitted changes. Do not diff from `Inputs as of`: that header records which inputs the report reflects, and a diff from it shows this stage's own last output as changes too. If someone else committed between `Inputs as of` and the skill's commit (`git log --oneline <inputs as of>..<commit>`), read those changes as well. If the project is not under git, or the commits cannot be found, read every input again and compare it with this stage's items.
- A re-run handles what changed: new or changed upstream items, the open proposals addressed to this stage, the proposals this stage raised that stage 2 has resolved, changed shared facts its items cite, the answers recorded in the assumptions this stage wrote (including those the design chain passed on), and the open gaps and answers that later stages' reports address to this stage. Leave the other items as they are unless a change reaches them. A change that only rewords an upstream item, without changing its meaning, needs nothing here. Keep changes the user made by hand to this stage's items, unless they contradict an input; then ask. To find them, look at the changes to this stage's own files after its last run: one that no skill's commit made, including one not committed yet, is a hand edit. A change made by another skill's commit (its first line names the skill), such as stage 2's resolution of a proposal, a QAS answer or a shared fact added by a later stage, is not a hand edit: handle it as an input. An answer given in another stage that changes this stage's items is cited where it is used ("stage 3, decision 4"); it gets no row under this stage's "Decisions", which holds the questions this stage asked and the answers the design chain passed on.
- Update in place. Keep existing IDs and never renumber. A new item takes the number after the highest one in use, retired items included.
- When an item is no longer needed, set `status: retired`. Do not delete the file: other items may trace to it. When a retired item is needed again for the same purpose, set it back to `active` instead of writing a new one.
- Artifacts written by an earlier version of these skills may carry `changelog`, `version`, `needs_agreement` and `approver_candidates` fields, statuses `draft` or `agreed`, sections about earlier runs, and reports with no `Inputs as of`. On the first re-run that touches such a file, drop those fields and sections, set `draft` and `agreed` to `active` (an agreement the file records counts as `confirmed` for what it covered), and read every input again, since there is no commit to diff from.
- Keep the decisions in effect. Do not ask the same question again unless the inputs it depended on have changed. Number the rows of "Decisions" per stage, questions and corrections in one sequence, and continue the numbering on a re-run, so that `basis_note` can cite them ("decision 7", or "question 7" for a question); when citing another stage's, name the stage ("stage 3, decision 2").

## Finishing a run

1. List the open assumptions that this run wrote or depends on, one line each with their ratings, highest first. Say that no answer is needed, and that any correction the user gives is applied like an answer. Record a correction under "Decisions" with the next number, and set the assumption to `overturned` or `confirmed` as the user says. Silence, or "no corrections", leaves an assumption `open`. When applying the corrections creates new assumptions, list them in the closing message as well.
2. Tell the user which stages to re-run, and for which items: later stages for new and changed items; every stage, earlier ones included, whose items cite a shared fact or an assumption that this run added, changed or settled.
3. Check that every YAML file this run wrote, and every item file of this stage, still parses and that no value is cut off at ` #`, with a YAML parser when one is available; fix any that is, whichever run wrote it. List a value cut off in another stage's file under "Open gaps" for that stage. If the project is under git, commit the files this run wrote, and only those, to the current branch. Add the new files, then commit with the paths named, so that nothing else the user has staged is included (`git add -- <paths>`, then `git commit -m "<message>" -- <paths>`). Write the message in the language of the project's recent commit messages, or in the working language when there are none. The first line names the skill and what the run did; the body says what triggered the run, which items were added, changed or retired and why, and which stages to re-run. Never push. If the commit fails, say so and leave the files as they are.
4. A correction to the open assumptions that arrives after the commit is applied like an answer: update the items and the report, and commit again.
