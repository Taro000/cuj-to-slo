# One formula, two counts

## Contents
- From KGI to formula
- The same formula, narrowed and overall
- Four things to fix before writing a formula
- Why users and contexts come before formulas
- When the two do not overlap
- Measure shapes by characteristic
- Scales and criteria
- Targets

## From KGI to formula

Cause comes before effect along this chain: product quality → quality in use → user behaviour (means KPI) → KGI.

- KGI: the measure of the business purpose, such as revenue or profit. Its target is set by the business, and it is the only target set in advance.
- Intermediate outcome: a user behaviour (in B2B, also a customer organization's decision, such as renewing) that the business wants to change in order to reach the KGI. Write it in words, with one of five verbs: maintain, eliminate, decrease, increase, create. No formula yet.
- Means KPI: the yardstick that counts an intermediate outcome. It measures a means, so keep it separate from the KGI, which measures the purpose.

## The same formula, narrowed and overall

When the user's goal and the business's goal meet in the same event, write one formula A ÷ B that counts that event.

- Counted within one context of use, it is the quality-in-use measure.
- Counted over everyone, it is the means KPI.

Match the two by what A and B count, not by what they are called. A net promoter score, for example, is a difference between two shares, not one A ÷ B, so it is not the overall count of a satisfaction measure.

| Same event (A ÷ B) | Narrowed: quality-in-use measure | Overall: means KPI |
|---|---|---|
| Orders confirmed ÷ checkouts started | Task completion rate: buyers on smartphones who finished card checkout | Purchase completion rate |
| Answers of 4 or more to a satisfaction question ÷ answers | Satisfaction, counted per user type and task | Share of satisfied answers across all users |
| Double charges and wrong bills ÷ payments | Economic risk, per payment method and user type | Billing error rate |
| Orders confirmed in the app ÷ checkouts started in the app | Customers on phones at lunch peak paying by card | Order completion rate |
| Claims approved without being returned ÷ claims submitted | First-pass approval for new employees who photograph receipts on mobile | First-pass approval rate |
| Report opens where yesterday's data was complete ÷ report opens before 9:00 | Store managers on the morning shift | Daily report completeness at open |
| Ad clicks ÷ ad impressions | No counterpart: it counts the business's goal, not the user's | Click-through rate |

## Four things to fix before writing a formula

1. Whose goal, and which goal. Count the user's goal. A formula that counts what the business gets from users (clicks, time on site) falls outside the overlap.
2. Which context of use. Fix the device, network, user type and task. Only rates from the same context are comparable.
3. Whether the denominator holds only people who needed the task. If people who never needed it are included, the rate measures demand or season instead.
4. Whether the product's contribution can be isolated. Compare with an A/B test, or choose an event that sits close to the product's behaviour.

## Why users and contexts come before formulas

You can define a KPI however you like. That is exactly the danger: write the formula first and it gets built from whatever is easy to log (clicks, page transitions), and only afterwards does anyone ask whether it matches the user's goal. Decide the user and the context first; the formula follows from them.

## When the two do not overlap

- An outcome with no matching user event gets a means KPI only, without passing through quality in use. Ad revenue is one example. Churn in B2B is another: a customer company decides it, not a user in a CUJ. Record why in the KPI's `overlap_note`.
- A KPI outside the overlap can move against quality in use. A clickbait headline raises the click-through rate and lowers the share of people who reach what they came for (effectiveness).
- A quality-in-use measure that the business does not count overall has no KPI. That is fine; leave `kpi: null`. When the input names no KPIs at all, whether the business counts something overall is unknown: draft a KPI only where the event directly counts a stated outcome, and ask.

## Measure shapes by characteristic

| Characteristic | Typical shape | Example |
|---|---|---|
| Effectiveness | Completed tasks ÷ attempted tasks | Orders confirmed ÷ checkouts started |
| Efficiency | Tasks completed within a time ÷ tasks completed | Repeat purchases finished within T seconds ÷ repeat purchases |
| Satisfaction | Answers at or above a level ÷ answers | Answers of 4 or 5 to "Would you use this again?" ÷ answers |
| Freedom from risk | Harmful events ÷ transactions, or loss amount per period | Double charges ÷ payments; refund cost per month |
| Context coverage | The same formula as another QIU, counted in another context; judge the gap | Completion on slow networks, compared with completion on broadband (`compared_with`) |

A threshold such as "within T seconds" or "by 7:00" is part of the criterion. Take it from the input; when the input gives none, keep the named placeholder and list it under "Needs agreement".

When the product cannot see the event (reconciliation happens in the customer's own system; satisfaction needs a survey nobody runs yet), keep the formula anyway, set `observable: false`, and record the gap. Replacing it with an event that happens to be logged is the failure described above.

## Scales and criteria

- Subjective measures are usually nominal or ordinal. With an ordinal scale you can only say "higher" or "lower". Never average a five-point rating; count the share at or above a level, such as "the share who answered 4 or more".
- Counts and durations are ratio scales: division and percentiles are fine.
- Write criteria as proportions, such as "90% of users finish the task within T minutes". This form carries straight down to SLOs later.

## Targets

Do not hand the KGI target down the chain ("revenue +10%, so completion +2 points"). That only works once the effect of one stage on the next is known, and before the first release nobody knows it. Leave quality-in-use and KPI targets undecided. Mark a target provisional only when the input gives a baseline, and mark every target as needing agreement.
