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
- Likelihood (1 to 5): how likely the failure is in this system, given its architecture, dependencies and history.
- Score: impact × likelihood.

Starting anchors, to adjust to the organization:

| Value | Impact | Likelihood |
|---|---|---|
| 5 | The CUJ is blocked for most users, or people are harmed, or the law is broken | Happens every week, or is already happening |
| 4 | The CUJ fails for a segment, or a significant loss | About monthly |
| 3 | Noticeable degradation; a workaround exists | A few times a year |
| 2 | Minor annoyance | About yearly or less |
| 1 | Barely noticeable | Very unlikely |

Likelihood rests on facts the inputs rarely give: the architecture, the volumes, the incident history. Ask about those premises once, and score from the answers; do not ask about each score.

For complex systems, quality attribute scenarios arranged in a utility tree give a more rigorous ranking; the handling rules below stay the same.

## Handling

- `emphasized`: the top two to four QRs per CUJ. These get measures, targets and SLOs in stage 4.
- `minimum-line`: kept, with just enough monitoring that a drop gets noticed. A low score does not mean the quality does not matter.
- `excluded`: set aside for this cycle, with a one-line reason ("Not this cycle: single cloud region, no migration planned"). The reason lets a later reader tell a decision from an omission.

Rules:

- A QR counts toward the limit of every CUJ whose QIU it traces to. Keep `traces_to` true even when that crowds a CUJ; crowding is a question for the business, not a reason to trim the trace.
- Only requirements that come from users can be excluded for a low score. A requirement from a law or another non-user source is `emphasized` or `minimum-line` by its score, never `excluded`; say in `handling_reason` that it is kept because of its source. Such requirements do not count toward the two to four emphasized items.
- Internal QRs take the handling of the external QR they support and do not count toward the limit.
- If more than four candidates look indispensable for one CUJ, ask. The CUJ may need to be split, or the business may need to choose.
- Show the table to the business and get agreement. A priority list that nobody agreed to is overturned at every incident. Mark every QR's handling as needing agreement.

## Example: online store purchase

CUJ: cart → payment → order confirmation.

| Characteristic | Failure scenario | Impact | Likelihood | Score | Handling |
|---|---|---|---|---|---|
| Reliability > availability | The payment API stops and nobody can buy | 5 | 3 | 15 | Emphasized: success-rate SLO |
| Performance efficiency > time behaviour | Payment is slow and buyers abandon checkout | 4 | 3 | 12 | Emphasized: latency SLO |
| Data > currentness | Stock updates lag, sold-out items are ordered, then cancelled | 4 | 3 | 12 | Emphasized: freshness SLO |
| Functional suitability > functional correctness | Wrong totals; double charges | 5 | 2 | 10 | Emphasized: correctness SLI from reconciliation |
| Data > accuracy | A wrongly registered price is sold as it is | 5 | 2 | 10 | Emphasized with a question: a fifth emphasized item for this CUJ |
| Security > confidentiality | Card data leaks | 5 | 2 | 10 | Emphasized, outside the limit: it comes from the security risk analysis, not from users. Managed through audits and vulnerability-fix deadlines, not an SLO |
| Usability > user error protection | Input mistakes make payments fail | 3 | 3 | 9 | Minimum line |
| Compatibility > interoperability | Calls to the payment provider fail | 4 | 2 | 8 | Minimum line: monitor the provider's success rate |
| Maintainability > modifiability | Changes before a sale are not ready in time | 3 | 2 | 6 | Minimum line: internal quality and delivery metrics |
| Portability > adaptability | Moving to another runtime fails | 2 | 1 | 2 | Excluded this cycle: no move planned |

## Example: food delivery order

| Characteristic | Failure scenario | Impact | Likelihood | Score | Handling |
|---|---|---|---|---|---|
| Performance efficiency > time behaviour | Order confirmation is slow at lunch peak and customers give up | 4 | 4 | 16 | Emphasized: latency SLO |
| Data > currentness | Sold-out dishes are still shown; the restaurant cancels after the order | 4 | 4 | 16 | Emphasized: sold-out propagation SLO |
| Data > accuracy | The estimated arrival time is wrong and customers wait | 3 | 4 | 12 | Emphasized: arrival-time error SLI |
| Reliability > availability | The order API stops and nobody can order | 5 | 2 | 10 | Emphasized: success-rate SLO |
| Maintainability > analysability | During an incident, nobody can tell which restaurant or payment method fails | 3 | 3 | 9 | Kept through trace IDs in the Definition of Done |
| Functional suitability > functional correctness | Settlement amounts to restaurants are miscalculated | 4 | 2 | 8 | Minimum line: daily reconciliation |
| Data > accuracy (law) | A missing allergen registration harms a customer | 5 | 1 | 5 | Minimum line, never excluded: it comes from food labelling law. Checked by mandatory validation at registration |

## Requirements that do not come from users

Some product quality requirements never come from users, however carefully you ask them. Put them straight into this stage. The more carefully you follow the path from users, the easier these are to miss.

| Type | Example | Usually becomes |
|---|---|---|
| Laws and regulations | Electronic transaction records are stored, in a searchable form, for the legally required period. Allergens are shown correctly on menus. | Data quality (availability, accuracy, compliance) plus functions (search, mandatory fields) |
| Standards and guidelines | Following accessibility guidelines, the purchase can be completed with the keyboard alone. | Usability > accessibility |
| Security requirements from risk analysis | Against account takeover with leaked passwords, logins from unusual environments require additional authentication. | Security > authenticity plus a function |
| Development policies and constraints | Frameworks to use, coding rules; a constraint on how the product behaves, such as keeping each customer's data in its own tenant | Internal quality; external quality (often security) for a behavioural constraint |

The examples in this table show the shape of such requirements. They are not sources: never take what a law or standard requires from them, or from your own knowledge.

- Set `entry_point` with the type, the source (the law's or standard's name, or the risk analysis) and `brought_by` (the role that brought it, such as legal or security), instead of tracing to a QIU. When the inputs name no role, draft the one you expect, mark it `inferred` and ask.
- When the inputs give the content, draft the QRs from it.
- When the inputs name a source but not its content, draft QRs only for the categories the inputs already raise (a hint, an upstream open gap), mark them `inferred`, and ask what the source requires.
- When the inputs name none, draft no QRs and do not assert obligations. Ask whether there are any, and list the categories you suspect.
- When the requirement also needs a function that no FR provides, write an FRP.

## Conflicts

Quality requirements often pull against each other. Record each conflict on both QRs, with how it is resolved:

- A fraud check adds latency to payment: reserve part of the response time for it, and let stage 4 size that part.
- A cache lowers the load on the stock API but makes the stock shown older: cache entries must not outlive the freshness threshold.
- Encryption of personal data adds processing cost: encrypt only the sensitive columns.

If you cannot tell how the business wants a conflict resolved, ask.

## From external to internal

For each emphasized external QR, ask which properties of the code and design support it, and add them as internal QRs linked with `implies_internal`. They take the handling of the external QR and need no score of their own:

- Checkout responds fast at peak (external) → the number of database queries per order confirmation is capped (internal).
- Engineers isolate failures quickly (maintenance quality in use) → every service logs the trace ID (internal, analysability).
- The shipping calculation changes often (modifiability) → shipping is a separate component with its own tests (internal, modularity and testability).

An internal requirement that supports no external one does not belong here. It is a development policy; take it in as a requirement that does not come from users, or leave it out.
