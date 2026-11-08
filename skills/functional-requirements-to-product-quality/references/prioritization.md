# Prioritization, non-user requirements and conflicts

## Contents
- Scoring failure scenarios
- Handling
- Example: online store purchase
- Example: food delivery order
- Requirements that do not come from users
- Conflicts
- From external to internal

## Scoring failure scenarios

Score the failure scenario of each candidate, not the characteristic in the abstract.

- Impact (1 to 5): how badly the failure hurts the quality-in-use requirement it traces to, and through it the outcome and the KGI.
- Likelihood (1 to 5): how likely the failure is in this system, judged from requirement-level facts: the external systems the function relies on, the volumes in the operational facts, the nature of the data, and the incident history of a product that is already running.
- Score: impact × likelihood.

Starting anchors, to adjust to the organization:

| Value | Impact | Likelihood |
|---|---|---|
| 5 | The CUJ is blocked for most users, or people are harmed, or the law is broken | Happens every week, or is already happening |
| 4 | The CUJ fails for a segment, or a significant loss | About monthly |
| 3 | Noticeable degradation; a workaround exists | A few times a year |
| 2 | Minor annoyance | About yearly or less |
| 1 | Barely noticeable | Very unlikely |

Never ask about a design that does not exist yet, and do not invent one to score against. Score from the facts above, and write in `basis_note` what the design must confirm ("the design must confirm how quickly stock changes from EXT-02 can reach the store"). Ask about a missing volume or incident history only when its rating calls for it (`conventions.md`, "Draft first, ask afterwards"), and never about each score.

For complex systems, rating each scenario for its importance to the business and for its difficulty gives a finer ranking; the handling rules below stay the same.

## Handling

- `emphasized`: chosen by at least one CUJ as one of its top two to four QRs. These get measures, targets and SLOs in stage 4.
- `minimum-line`: kept, with just enough monitoring that a drop gets noticed. A low score does not mean the quality does not matter.
- `excluded`: set aside for this cycle, with a one-line reason ("Not this cycle: no replacement of the store is planned"). The reason lets a later reader tell a decision from an omission.

Rules:

- Each CUJ chooses from the QRs that trace to its QIUs, and its limit counts only the QRs it chose. A QR that other CUJs chose is listed for this CUJ without counting, and this CUJ may choose it too. Counting every traced QR against every CUJ makes a widely shared QR (the availability of the main flow) crowd out each CUJ's own priorities, and lowering it to make room takes protection away from the CUJs that chose it. Keep `traces_to` true: never trim a trace to change a count.
- Only requirements that come from users can be excluded for a low score. A requirement from a law or another non-user source is `emphasized` or `minimum-line` by its score, never `excluded`; say in `handling_reason` that it is kept because of its source. Such requirements do not count toward the two to four emphasized items.
- Internal QRs take the handling of the external QR they support and do not count toward the limit.
- If more than four candidates look indispensable for one CUJ, ask. The CUJ may need to be split, or the business may need to choose. Five or more QRs depend on that decision, so its breadth is high.
- The emphasized set of each CUJ, and each exclusion, is a business decision (`conventions.md`, "Decisions"). A priority list that nobody decided is overturned at the first incident. The emphasized sets are always asked: up to three CUJs per multi-select question, each option naming a CUJ and the QRs it chose, with a fourth option saying that all of them are right; then ask what each set marked wrong should be. Exclusions are rated like any other point and asked when a rating is high. An exclusion that is not asked becomes a QAS with `kind: decision`; the QR it excludes lists it in `premises` and stays `assumed` until it is settled.

## Example: online store purchase

CUJ: cart → payment → order confirmation.

| Characteristic | Failure scenario | Impact | Likelihood | Score | Handling |
|---|---|---|---|---|---|
| Reliability > availability | Checkout stops and nobody can buy | 5 | 3 | 15 | Emphasized: success-rate SLO |
| Performance efficiency > time behaviour | Payment is slow and buyers abandon checkout | 4 | 3 | 12 | Emphasized: latency SLO |
| Data > currentness | Stock changes from the warehouse system reach the store late; sold-out items are ordered, then cancelled | 4 | 3 | 12 | Emphasized: freshness SLO |
| Functional suitability > functional correctness | Wrong totals; double charges | 5 | 2 | 10 | Emphasized: correctness SLI from reconciliation |
| Data > accuracy | A wrongly registered price is sold as it is | 5 | 2 | 10 | A fifth item that looks indispensable: the business is asked which four to emphasize |
| Security > confidentiality | Card data leaks | 5 | 2 | 10 | Emphasized, outside the limit: it comes from the security risk analysis, not from users. Managed through audits and vulnerability-fix deadlines, not an SLO |
| Usability > user error protection | Input mistakes make payments fail | 3 | 3 | 9 | Minimum line |
| Compatibility > interoperability | Exchanges with the payment provider fail | 4 | 2 | 8 | Minimum line: monitor the provider's success rate |
| Maintainability > modifiability | Changes before a sale are not ready in time | 3 | 2 | 6 | Minimum line: internal quality and delivery metrics |
| Performance efficiency > resource utilization | Checkout uses so much mobile data that buyers on limited plans give up | 2 | 2 | 4 | Excluded this cycle: nothing suggests that data use costs purchases. Rated low for breadth and severity, so not asked: a QAS with `kind: decision` |

## Example: food delivery order

| Characteristic | Failure scenario | Impact | Likelihood | Score | Handling |
|---|---|---|---|---|---|
| Performance efficiency > time behaviour | Order confirmation is slow at lunch peak and customers give up | 4 | 4 | 16 | Emphasized: latency SLO |
| Data > currentness | Sold-out dishes are still shown; the restaurant cancels after the order | 4 | 4 | 16 | Emphasized: sold-out propagation SLO |
| Data > accuracy | The estimated arrival time is wrong and customers wait | 3 | 4 | 12 | Emphasized: arrival-time error SLI |
| Reliability > availability | Ordering stops and nobody can order | 5 | 2 | 10 | Emphasized: success-rate SLO |
| Maintainability > analysability | During an incident, nobody can tell which restaurant or payment method fails | 3 | 3 | 9 | Minimum line, kept by the Definition of Done: every failed order can be followed to its restaurant and payment method |
| Functional suitability > functional correctness | Settlement amounts to restaurants are miscalculated | 4 | 2 | 8 | Minimum line: reconciliation finds a wrong settlement before it is paid out |
| Data > completeness (law) | A missing allergen registration harms a customer | 5 | 1 | 5 | Minimum line, never excluded: it comes from food labelling law. A dish cannot be saved without its allergens |

## Requirements that do not come from users

Some product quality requirements never come from users, however carefully you ask them. Put them straight into this stage. The more carefully you follow the path from users, the easier these are to miss.

| Type | Example | Usually becomes |
|---|---|---|
| Laws and regulations | Electronic transaction records are stored, in a searchable form, for the legally required period. Allergens are shown correctly on menus. | Data quality (availability, accuracy, compliance) plus functions (search, mandatory fields) |
| Standards and guidelines | Following accessibility guidelines, the purchase can be completed with the keyboard alone. | Usability > accessibility |
| Security requirements from risk analysis | Against account takeover with leaked passwords, logins from unusual environments require additional authentication. | Security > authenticity plus a function |
| Development policies and constraints | Coding rules that every change follows; a constraint on how the product behaves, such as keeping each customer's data out of every other customer's sight | Internal quality; external quality (often security) for a behavioural constraint |

The examples in this table show the shape of such requirements. They are not sources: never take what a law or standard requires from them, or from your own knowledge.

- Set `entry_point` with the type, the source (the law's or standard's name, or the risk analysis) and `brought_by` (the role that brought it, such as legal or security), instead of tracing to a QIU. When the inputs name no role, draft the one you expect and mark it `inferred`.
- When the inputs give the content, draft the QRs from it.
- When the inputs name a source but not its content, draft QRs only for the categories the inputs already raise (a hint, an upstream open gap), and ask what the source requires. The content of a named law, standard or policy is always asked.
- When the inputs name none, draft no QRs and do not assert obligations. Ask whether there are any, and list the categories you suspect.
- A policy that chooses a design (a framework, a product, a data store, where the system runs) becomes no QR. It is a design input: if `context.yaml → design_inputs` does not hold it yet, stage 3 adds it as a new DIN entry, with the policy as its `source` (`conventions.md`, "Shared facts"). If the policy carries a property that matters to the requirements, take in that property, stated without the design.
- When the requirement also needs a function that no FR provides, write an FRP.

## Conflicts

Quality requirements often pull against each other. Record each conflict on both QRs, with how it is resolved, stated as what must hold rather than as a mechanism:

- Riding out a failing payment provider adds waiting time to payment: reserve part of the response time for it, and let stage 4 size that part.
- Carrying the sale-peak load of stock lookups pulls toward showing older stock: whatever the design does to carry the load, the stock shown is never older than the freshness threshold.
- Keeping personal data unreadable to anyone not authorized adds processing cost: the protection covers the personal data that the inputs name as sensitive, not every item.

A conflict that only the business can resolve (which quality gives way, and how far) is a decision (`conventions.md`, "Decisions"): rate it, and either ask or record a QAS with `kind: decision`. Otherwise it is resolved by the design, within what both QRs require, or by an FRP when the resolution needs a function that no FR provides.

## From external to internal

For each emphasized external QR, work out which properties of the code and design support it, and add them as internal QRs linked with `implies_internal`. They take the handling of the external QR and need no score of their own. State each one as a property that any design must have, without choosing the design:

- Checkout responds fast at peak (external) → the work done to confirm one order does not grow with the size of the catalogue or of the buyer's order history (internal).
- Engineers isolate failures quickly (maintenance quality in use) → every failed request can be followed through each part of the system that handled it (internal, analysability).
- The shipping calculation changes often (modifiability) → a change to the shipping rules stays within one part of the code and can be tested on its own (internal, modularity and testability).

An internal requirement that supports no external one does not belong here. It is a development policy; take it in as a requirement that does not come from users, or leave it out.
