# Conventions shared by the quality chain skills

Four skills form one chain. Each reads the artifacts of the stages above it and writes its own.

| Stage | Skill | Reads | Writes |
|---|---|---|---|
| 1 | `cuj-to-quality-in-use` | users, CUJs and requirements given by the user | `context.yaml`, quality-in-use requirements (QIU), means KPIs (KPI) |
| 2 | `quality-in-use-to-functional-requirements` | context, QIU, KPI, open function proposals (FRP) | functional requirements (FR), FRP resolutions |
| 3 | `functional-requirements-to-product-quality` | context, QIU, KPI, FR, requirements that do not come from users | product and data quality requirements (QR), FRP |
| 4 | `product-quality-to-slo` | context, QIU, FR, QR, operational facts | non-functional requirements (NFR), SLI definitions (SLI), error budget policies (EBP), FRP |

An upper stage is the reason for the stages below it; a lower stage is the means of achieving the stages above it. Every item traces up to the item it serves, so anyone can walk from an SLO back to the user it protects.

Stages 3 and 4 never add or change functional requirements themselves. When a quality requirement needs a function that no FR provides, or needs an FR to behave differently (its acceptance criteria let through what the requirement forbids), they write a function proposal (FRP). The user re-runs stage 2, which accepts or rejects each proposal, and then re-runs the later stages for the affected items. This keeps every function in one list, so none of them drops out of estimates.

An FRP states one function, or one change to one FR, as what the system does. Its title and statement say the same thing, and it does not depend on a measurement definition (an SLI). It lists the later-stage items that depend on the decision, so that stage 2 can say what a rejection breaks.

## Scope

These skills produce software requirements for the system inside the boundary in `context.yaml`. When a need can be met only outside the software (business procedures, operations, facilities, training), or only by another system outside the boundary (a customer's own accounting system, for example), write one line in the report's "Out of scope" section saying what and why, and do not turn it into a requirement. Recording it keeps the gap visible without pretending the software will close it.

## Working language

- Ask questions in the language the user writes in.
- Write artifacts in the language of the project's existing documentation. If the project has none, use the user's language.
- Keep YAML keys, IDs and enum values in English exactly as `artifacts.md` defines them, so that every stage can read every other stage's files. Write free-text values in the working language.
- Use the terms in `glossary.md` for the working language, and use each one consistently. Characteristic names are a vocabulary, not prose: never paraphrase them.
- In reports, write the basis marks and statuses with the glossary's terms for the working language. In YAML, use the English enum values.

## Judgment basis

Mark every judgment with where it came from:

- `stated`: written in the input or in an upstream artifact. Rewording that keeps the meaning (into one of the five outcome verbs, for example) is still `stated`.
- `inferred`: your own judgment, made with enough evidence that you are not unsure. Anything the input does not say is `inferred`, even when it seems obvious.
- `confirmed`: the user answered a question about it.

A judgment you are unsure about is still drafted, as `inferred`, and also becomes a question. Never present an `inferred` judgment as if it were `stated`. People tend to accept a confident-looking draft without checking it; the basis mark tells them where to look.

An item holds several judgments (a CUJ's goal, its steps, its outcome), and its `basis` is the weakest of them. An item is `confirmed` only when every inferred judgment in it has been answered. When a question settled only part of an item, keep the item `inferred` and say in `basis_note` which parts are stated, inferred and confirmed. In the later stages most items stay `inferred`: they are proposals, and agreement (`status: agreed`) is what settles them.

## Numbers

Never invent a number. When a requirement needs a threshold or target that the inputs do not give (a duration, a deadline, a count, a share), write a named placeholder, set `needs_agreement: true`, and list it under "Needs agreement". Give each placeholder a name that no other placeholder in the chain uses, such as `T_repeat` or `N_onboarding_days`, and say what it stands for. A number taken from the inputs keeps its source. Only stage 4 proposes values for missing numbers, and it marks each one as provisional and says what the value is based on.

A value you would have to propose because nothing gives one is an agreement item, not a question: asking cannot make you surer of a number nobody has measured. Ask instead for the facts that would settle it (a baseline, a volume, an existing contract).

## Draft first, ask afterwards

1. Read every input and every existing artifact under the artifacts directory. On a re-run, read this stage's inputs and its own items, and the items of other stages only where the changes lead.
2. If an essential input is missing, so that no meaningful draft is possible (each skill names its essential inputs), ask only for that input before drafting. This is a request for input, not a judgment question.
3. Draft the whole stage without stopping. Make every judgment yourself, using the rules in the skill. Write the draft report.
4. Then ask about every point you were unsure of: all of them, not a sample. A point is uncertain when:
   - the input is silent or ambiguous about it, and the choice changes this stage or a later one;
   - two or more reasonable options exist, and the wrong one would cause rework downstream;
   - inputs contradict each other or an upstream artifact;
   - it depends on facts that only the user's organization knows: traffic, telemetry, legal obligations, roles, existing systems.

   Do not ask about anything the input already states, or about wording. Do not ask how the method works; do ask about the business facts that a judgment made with the method depends on. Merge questions that hinge on the same fact.
5. Group the questions by theme, and put first the ones where an answer different from your draft would cause the most rework. For each question, give what you drafted, why you are unsure, and the options, with your drafted answer first. When a question asks for a fact (a volume, a name), the drafted option says what the draft does without it ("left as a formula"), and the user types the value.
   - An uncertain point whose answer would change only one item, and nothing that depends on it, is still asked, but not as a question of its own. Collect such points into one last question that lists each assumption in one line and asks which of them are wrong. Its options are the assumptions themselves, with no recommended option; choosing none means every assumption holds.
   - When several items share one yes-or-no judgment ("which of these functions already exist?"), ask it as one multi-select question rather than one question per item.
   - If a structured question tool is available (for example, AskUserQuestion in Claude Code), ask up to 4 questions per call with 2 to 4 options each, put your drafted option first and label it as recommended, and call the tool again for the next batch. Ask multi-select questions with up to 4 items each, splitting longer lists over several questions.
   - Otherwise, ask in chat as a numbered list and wait for the answers.
6. Apply the answers and mark the affected judgments `confirmed`. If an answer overturns a premise, redo the affected part of the draft, then ask any new uncertain points the same way. A redone item keeps its ID while it serves the same purpose, even when its content changes; record the change in the questions and answers. If an answer drops a drafted item, remove it from the report and write its file with `status: retired`, so that its number is never used again and the IDs quoted in the questions and answers keep their meaning.
7. Check the stage's exit criteria. Set the report's status to "answers applied" and write the YAML items. Until then the report is the working copy; from then on the YAML items are the source of truth, and the report must match them. The report's status says nothing about agreement; that lives in each item's `status`.

## Items that need agreement

Priorities, targets and thresholds, exclusions ("excluded this cycle") and error budget policies are business decisions. A priority list that nobody agreed to gets overturned at the first incident. For these items:

- Ask about them only if you are unsure, like any other judgment.
- Set `needs_agreement: true` and list `approver_candidates`. Use roles, not names, unless the input names people.
- List every one of them in the report under "Needs agreement".
- Keep `status: draft` until the user says the agreement was made. Then set `status: agreed` and record who agreed in the changelog.

## Re-running a stage

- A re-run handles what changed: new or changed upstream items, and the open proposals addressed to this stage. Leave the other items as they are unless a change reaches them.
- Update in place. Keep existing IDs and never renumber. A new item takes the number after the highest one in use, retired items included.
- When an item is no longer needed, set `status: retired` and add a changelog entry. Do not delete the file: other items may trace to it. A drafted item that an answer dropped before any file was written also gets a file, with `status: retired`, so that its number is never used again.
- Whenever an item's meaning changes, bump `version` and add a changelog entry with the date, the change and the reason.
- Keep answers from earlier rounds. Do not ask the same question again unless the inputs it depended on have changed. Number questions per stage and continue the numbering on a re-run; when citing another stage's question, name the stage ("stage 3, question 2").
- Add a "Re-run on <date>" section at the top of the report: what triggered the re-run, which items changed, and which items of later stages are affected.
- End by telling the user which later stages to re-run and for which items: new and changed items, and items whose premises changed (a QR whose proposed function was rejected, an NFR that waited for a proposal). Carry forward any re-run an earlier stage asked for that has not happened yet.
