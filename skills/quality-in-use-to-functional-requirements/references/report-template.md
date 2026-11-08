# Stage 2 report template and worked example

## Contents
- Report template (`docs/quality/2-functional-requirements.md`)
- Worked example: online store
- Example items

Write the report in the working language. Translate the headings. Keep the IDs as they are, and write the basis marks, statuses and other enum values with the glossary's terms for the working language.

The report never restates what an item says: no statements, acceptance criteria, quality hints or other free text copied from the YAML items. Its tables show IDs, titles, enum values and references. It holds in full only what no item records: the functions considered and not added, the untraced functions, the coverage notes, the decisions, the open gaps and what is out of scope. Every copy of an item's text is another place for the report and the items to drift apart.

## Report template

```markdown
# Functional requirements: <system name>

Status: draft | answers applied · Updated: <date> · Inputs as of: <commit, or "none"> · Stage 2 of 4

## Summary
<Two or three sentences: how many FRs, how many new and how many existing, how many from proposals; how many questions were asked, how many assumptions are open.>

## Inputs
| Source | What it gave |
|---|---|
<Documents and upstream artifacts, with the shared facts this stage added from them, by ID. The user's answers are under "Decisions".>

## Functions by CUJ step
| CUJ | Step | FRs | Note |
|---|---|---|---|
<Every step of every CUJ. For a step performed outside the system, say so in the note.>

## Quality-in-use coverage
| QIU | How it is covered | FRs | Note |
|---|---|---|---|
<How it is covered: new function, extension, an FR carries it as quality only, or data comes from elsewhere (for the measure; the note names the source, such as an OPF). A QIU covered in more than one way has a row for each.>

## Functional requirements
| ID | Title | Origin | Existing | Extends | CUJ step | Traces to | Basis |
|---|---|---|---|---|---|---|---|

## Functions considered and not added
| Candidate | For | Reason |
|---|---|---|

## Function proposals
| FRP | Proposed by | For | Decision | Result |
|---|---|---|---|---|
<For: the QRs in the proposal's `origin`. Decision: accepted or rejected; a provisional decision names its QAS. Result: the FR written or changed, or the FR that already does what was proposed.>

## Untraced functions
<Features in the inputs that trace to no CUJ step, QIU or KPI, each with the number of the question asked about it.>

## Decisions
| # | Question | Answer | Items |
|---|---|---|---|
<The questions asked and the answers in effect, numbered per stage and continued on a re-run. An answer that overturns an earlier one replaces its row.>

## Open assumptions
| QAS | Title | Kind | Breadth | Severity | Items that depend on it |
|---|---|---|---|---|---|
<The open assumptions this stage wrote, and the open upstream ones its items depend on.>

## Placeholders
| Name | Stands for | Used in |
|---|---|---|
<Tunable thresholds in this stage's acceptance criteria, and the upstream placeholders that its quality hints use. When stage 4 has proposed a value, name its QAS or decision; this stage writes the value into its FRs.>

## Open gaps
<Include the functions you expect stage 3 to propose for laws, standards, risk analysis or policies, and the gaps in stage 1's part of context.yaml (a user, a data role, a CUJ step). The external systems, constraints, operational facts and design inputs this stage added are in context.yaml, named under "Inputs" or "Decisions".>
## Out of scope
## Next
Run `functional-requirements-to-product-quality`. After resolving proposals, name the items in their `origin` and `affects` and the stages to re-run for them. Name stage 1 when this run added a shared fact its items may cite, or a function that produces the data a QIU or KPI lacked.
```

## Worked example: online store

Inputs: the stage 1 artifacts of the worked example in `cuj-to-quality-in-use`, and a feature list of the live store: checkout with card payment (a declined card shows the reason and the buyer can pick another method), order history, product pages with a stock status taken from the warehouse system once a night, and a monthly sales report for accounting. The excerpts show the report as it stands after the re-run that resolved three proposals from stages 3 and 4 and wrote the value that stage 4 proposed for `T_repeat` into FR-02.

Header and summary:

```markdown
Status: answers applied · Updated: 2026-02-03 · Inputs as of: 4f2a9c1 · Stage 2 of 4

## Summary
Ten FRs: three existing, six new (one of them an extension) and one from a proposal. Of the three proposals, one was accepted and two were rejected, one of them provisionally. Five questions were asked; three assumptions are open, one of them from stage 4.
```

Functions by CUJ step (excerpt):

| CUJ | Step | FRs | Note |
|---|---|---|---|
| CUJ-01 Purchase | Cart | FR-04, FR-05 | |
| CUJ-01 | Payment | FR-01, FR-02, FR-03, FR-08, FR-10 | |
| CUJ-02 Isolate a checkout failure | Failing component | FR-06 | |
| CUJ-03 Month-end close | Receive the report | FR-09 | |
| CUJ-03 | Reconcile with payments | FR-07 | Accounting reconciles by hand; the system supplies the list of mismatches (question 2) |
| CUJ-03 | Close | | Accounting closes the month itself, outside the system |

Quality-in-use coverage:

| QIU | How it is covered | FRs | Note |
|---|---|---|---|
| QIU-01 Buyers finish card checkout on smartphones | an FR carries it as quality only | FR-01 | |
| QIU-01 | new function | FR-08, FR-10 | FR-08 comes from a stage 1 hint, FR-10 from FRP-01 |
| QIU-01 | data comes from elsewhere | | Counted from the application events that already exist (OPF-02) |
| QIU-02 Repeat buyers finish a second purchase quickly | new function | FR-02 | |
| QIU-03 Buyers are never charged twice or billed wrongly | new function | FR-03 | |
| QIU-04 Buyers receive what was shown as in stock | an FR carries it as quality only | FR-04 | |
| QIU-04 | extension | FR-05 | |
| QIU-05 SREs isolate a checkout failure in time | new function | FR-06 | |
| QIU-05 | data comes from elsewhere | | Counted from the SREs' incident records (OPF-05, question 3) |
| QIU-06 Accounting closes the month on figures that match payments | an FR carries it as quality only | FR-09 | |
| QIU-06 | new function | FR-07 | Closing the month stays with accounting. The list as it stands at each month end also gives the measure its data |

Functional requirements:

| ID | Title | Origin | Existing | Extends | CUJ step | Traces to | Basis |
|---|---|---|---|---|---|---|---|
| FR-01 | Confirm the order and take payment | cuj-step | yes | | CUJ-01 Payment | QIU-01, KPI-01 | stated |
| FR-02 | Saved address and card | quality-in-use | no | | CUJ-01 Payment | QIU-02 | assumed |
| FR-03 | Charge an order at most once | quality-in-use | no | | CUJ-01 Payment | QIU-03, KPI-02 | inferred |
| FR-04 | Show the stock status | cuj-step | yes | | CUJ-01 Cart | QIU-04 | stated |
| FR-05 | Show stock changes from the warehouse | quality-in-use | no | FR-04 | CUJ-01 Cart | QIU-04, KPI-03 | inferred |
| FR-06 | Follow a failed checkout end to end | quality-in-use | no | | CUJ-02 Failing component | QIU-05 | inferred |
| FR-07 | List orders and payments that do not match | quality-in-use | no | | CUJ-03 Reconcile with payments | QIU-06 | inferred |
| FR-08 | Flag a delivery address that does not exist | quality-in-use | no | | CUJ-01 Payment | QIU-01 | inferred |
| FR-09 | Send accounting the monthly sales report | cuj-step | yes | | CUJ-03 Receive the report | QIU-06 | stated |
| FR-10 | Keep checkout working when the payment provider fails | proposal | no | | CUJ-01 Payment | QIU-01 | inferred |

Functions considered and not added (excerpt):

| Candidate | For | Reason |
|---|---|---|
| Reorder a past order in one tap | QIU-02 | Saved address and card (FR-02) already remove the re-entry that QIU-02 names. |
| Notify the buyer when an item in the cart sells out | QIU-04 | The cart already flags the item before payment (FR-04). |

Function proposals:

| FRP | Proposed by | For | Decision | Result |
|---|---|---|---|---|
| FRP-01 Keep checkout working when the payment provider fails | functional-requirements-to-product-quality | QR-08 | accepted | FR-10 |
| FRP-02 Update the stock shown when the warehouse's stock changes | functional-requirements-to-product-quality | QR-03 | rejected | FR-05 |
| FRP-03 Record when the order result is shown to the buyer | product-quality-to-slo | QR-02 | rejected (provisional, QAS-13) | |

Decisions (excerpt):

| # | Question | Answer | Items |
|---|---|---|---|
| 1 | The feature list may not name every function the live store has. Which of these does the store already have: saved address and card, refusing a second charge for the same order, following a failed checkout end to end, flagging a delivery address that does not exist? | None of them. The feature list names every function the live store has. | FR-02, FR-03, FR-06, FR-08 |
| 2 | Accounting reconciles orders with payments by hand (CUJ-03). Should the system give accounting a list of the orders and payments that do not match? | Yes. The system gives accounting the list of mismatches; accounting still reconciles them and closes the month itself. | FR-07 |
| 3 | QIU-05 counts the checkout failures whose failing component SREs found within 15 minutes of the alert, and the store records neither time. Do the SREs' incident records hold both? | Yes, for every checkout incident. | OPF-05 |
| 5 | Stage 3 proposes keeping checkout working when the payment provider fails (FRP-01, for QR-08). Build it this cycle, or reject it for now? | Build it this cycle. | FRP-01, FR-10 |

Question 3 was asked because its answer adds a shared fact (breadth high). The answer is OPF-05 in `context.yaml`, written by stage 2, with "stage 2, question 3" as its `source` and `basis: confirmed`.

Open assumptions:

| QAS | Title | Kind | Breadth | Severity | Items that depend on it |
|---|---|---|---|---|---|
| QAS-12 | First value of T_repeat (stage 4) | decision | medium | low | FR-02 |
| QAS-04 | Saved cards stay available on every device the buyer signs in on | judgment | low | low | FR-02 |
| QAS-13 | Recording the buyer's wait at checkout is not built this cycle | decision | low | low | FRP-03 |

Placeholders:

| Name | Stands for | Used in |
|---|---|---|
| T_repeat | The seconds within which a repeat purchase should finish (from stage 1); stage 4 proposed its value (QAS-12) | QIU-02, FR-02 |

Next (excerpt):

- Re-run `functional-requirements-to-product-quality` for FR-10 and for QR-08 and QR-03, the QRs of FRP-01 and FRP-02. Then re-run `product-quality-to-slo` for NFR-01, which waited on FRP-01, and for QR-02, which is left without the function FRP-03 proposed.
- Re-run `cuj-to-quality-in-use` for QIU-05 and QIU-06: OPF-05 and FR-07 give their measures data, so their `observable` and open gaps follow.

## Example items

`docs/quality/fr/FR-03.yaml`:

```yaml
id: FR-03
title: Charge an order at most once
status: active
basis: inferred
basis_note: Not in the live store (question 1); the behaviour is inferred from QIU-03
premises: [EXT-01]
statement: The system charges an order at most once, even when the payment request for it is sent again.
origin: quality-in-use
cuj: CUJ-01
cuj_step: Payment
traces_to: [QIU-03, KPI-02]
proposal: null
existing: false
extends: null
interface: ["screen: buyer checkout", EXT-01]
acceptance_criteria:
  - Given an order that is already paid, when a payment request for the same order arrives again, then the system returns the first result and charges nothing.
  - Given a payment that showed the buyer no result, when the buyer pays for the same order again, then the order is charged at most once and the buyer sees whether it was paid.
  - Given two different orders with the same items and total, when each is paid, then both are charged.
quality_hints:
  - Economic risk mitigation (QIU-03) - no double charge, even when the payment provider does not answer
```

`docs/quality/fr/FR-06.yaml`, a function for a secondary user (maintenance). The design decides how each step of a checkout is recorded; the FR says only what an SRE can find:

```yaml
id: FR-06
title: Follow a failed checkout end to end
status: active
basis: inferred
basis_note: Not in the live store (question 1); the behaviour is inferred from QIU-05. The design must confirm how each step is recorded.
premises: [EXT-01]
statement: The system lets SREs look up a failed checkout by its order and follow every step the system took for it, end to end.
origin: quality-in-use
cuj: CUJ-02
cuj_step: Failing component
traces_to: [QIU-05]
proposal: null
existing: false
extends: null
interface: ["screen: SREs"]
acceptance_criteria:
  - Given a failed checkout, when an SRE looks it up by its order, then every step the system took for it and the outcome of each can be found.
  - Given a checkout that failed because the payment provider (EXT-01) refused or did not answer, when an SRE looks it up, then the steps found show that the failure came from the payment provider.
  - Given an order for which no checkout exists, when an SRE looks it up, then the system says that no checkout was found.
quality_hints:
  - Efficiency (QIU-05) - every failed checkout can be followed this way, with no step missing
```

`docs/quality/frp/FRP-01.yaml` after the re-run. Stage 2 sets `status` and writes `resolution`; for a provisional decision, it also lists the QAS in `premises` and sets `basis`. The rest is the proposing stage's, except `affects`, to which a later stage adds the items that wait on the decision (stage 4 added NFR-01):

```yaml
id: FRP-01
title: Keep checkout working when the payment provider fails
status: accepted
basis: inferred
basis_note: null
premises: [EXT-01]
proposed_by: functional-requirements-to-product-quality
origin: [QR-08]
changes: null
reason: Checkout must continue when the payment provider fails; no FR covers that case.
suggested_statement: The system keeps confirming orders while the payment provider is failing.
affects: [NFR-01]
placeholders: []
resolution: Accepted as FR-10, to be built this cycle (question 5).
```

`docs/quality/qas/QAS-13.yaml`, the provisional decision behind the rejection of FRP-03. It was not asked: only NFR-02 relies on the function, and NFR-02 keeps its SLO without it. FRP-03 lists QAS-13 in `premises` and has `basis: assumed`:

```yaml
id: QAS-13
title: Recording the buyer's wait at checkout is not built this cycle
status: open
stage: 2
kind: decision
statement: FRP-03 is rejected for this cycle. The part of the checkout wait spent on the buyer's device stays unmeasured until a later cycle builds the function.
why_unsure: Stage 4 proposed the function to see the whole wait the buyer has; nothing in the inputs says whether that is worth building now.
breadth: low
severity: low
rating_note: Only NFR-02 relies on the function, and it keeps its SLO without it; the proposal can be accepted in any later run
answer: null
answered_in: null
```
