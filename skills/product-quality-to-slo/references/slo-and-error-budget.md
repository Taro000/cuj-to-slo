# SLOs, error budgets, policies and alerts

## Contents
- Terms
- Targets
- Windows
- Tolerance is the error budget
- Error budget arithmetic
- Error budget policy
- Burn-rate alerts
- Batch and deadline SLOs
- SLOs and the backlog

## Terms

| Term | Meaning |
|---|---|
| SLI | A measure of the service's behaviour, normally good events ÷ valid events |
| SLO | The SLI's target and window, for example 99.9% over 28 days |
| SLA | A contract with customers; breaking it costs refunds or credits. Set it looser than the SLO |
| Error budget | 1 − SLO: the amount of failure allowed in the window |
| Error budget policy | Actions agreed in advance for when the budget is about to run out, or has run out |
| Burn rate | How fast the budget is being used. 1 means the budget runs out exactly at the end of the window |

## Targets

- Do not hand targets down from the top. "Revenue +10%, so purchase completion +2 points, so p99 1.5 s" works only once the effect of each stage on the next is known, and nobody knows that before the release.
- The first target is provisional. Start from numbers the inputs state, or from baselines (load-test results, current production figures). With neither, choose a conservative value: one the current system most likely meets already, so that the first window measures instead of paging. A target that is too strict pages people over nothing, and teaches them to ignore pages. Tighten it once a few windows of data exist.
- Mark every target `needs_agreement: true`, and keep `target_status: provisional` until the user says it was agreed.
- Thresholds inside an SLI ("within T seconds", "by 7:00") are set the same way: from the inputs, then baselines, then a conservative value (for a latency threshold, a longer time). A placeholder left by an earlier stage gets its first value here, and still needs agreement. Write what each value is based on next to it.
- Do not propose a value that a law, a contract or another party sets, such as a legal retention period. Keep the placeholder and say who sets it. Define a model's cut-off (a confidence score, a similarity score) by the result it must give ("the cut-off at which no more than the agreed share of claims is flagged by mistake"), not by a raw number on a scale you do not know.
- A proposed value is an agreement item, not a question: asking cannot make you surer of a number nobody has measured. Ask for the facts that would let you set it instead.
- If an SLA exists or is planned, it must be looser than the SLO, so that the SLO's alerts fire before the contract is broken.

## Windows

Use a rolling 28-day window unless the inputs say otherwise. Twenty-eight days always holds four of each weekday, so weekly patterns do not distort the comparison between windows. Use a calendar month only when the SLO must line up with monthly reporting, and say so.

## Tolerance is the error budget

Write the acceptable tolerance as the error budget, and draw no second line below the target. If the target is 99% and the requirement adds "down to 98.5% is 'watch'", then on the day the budget runs out and releases stop, the requirement still says "watch". Keep intermediate steps ("at 50% remaining, put reliability work first") in the policy only, so that the requirement and the policy never call for different actions.

## Error budget arithmetic

- Allowed bad events per window = valid events per day × days in the window × (1 − target).
- Full-outage equivalent = minutes in the window × (1 − target).

| Target | 28 days | 30 days |
|---|---|---|
| 99% | 6 h 43 min | 7 h 12 min |
| 99.5% | 3 h 22 min | 3 h 36 min |
| 99.9% | 40.3 min | 43.2 min |
| 99.95% | 20.2 min | 21.6 min |
| 99.99% | 4.0 min | 4.3 min |

Example: 300,000 order confirmations a day, over 28 days. A latency SLO of 99% within 1.5 s allows 84,000 slow confirmations. An availability SLO of 99.9% allows 8,400 failed confirmations, about 40 minutes of full outage.

When the traffic is unknown, write the formula and ask for the volume instead of guessing it.

The full-outage equivalent assumes traffic spread evenly over the window. When traffic is concentrated (office hours, month-end), an outage at the peak uses the budget faster than the minutes suggest; say so next to the number.

## Error budget policy

An SLO whose budget can run out without anything changing is only a chart. Agree in advance, between the business and development, what happens. A starting draft:

| Condition | Action agreed in advance |
|---|---|
| Remaining budget falls below 50% | The next sprint puts reliability work first |
| One incident consumes 20% or more | A postmortem is required, and at least one root-cause fix goes first |
| The budget is exhausted | For the rest of the window, release only fixes and security patches |
| 50% or more remains at the end of every window | Tighten the SLO, or spend the surplus on release speed and experiments |
| A data SLO's budget is exhausted | Ticket both the fix of the cause and the correction of the bad data already stored. Fixing the cause does not repair data that was already written |

Adjust the thresholds and actions to the organization. Mark the policy as needing agreement, with approver candidates from both sides. A policy without agreement is not followed on the day the budget runs out.

## Burn-rate alerts

Alert when the burn rate exceeds a threshold over both a long window and a short window. The long window shows that enough budget is being spent to matter; the short window shows that it is still happening, so the alert stops soon after recovery.

| Notify | Long window | Short window | Burn rate |
|---|---|---|---|
| Page (respond now) | 1 h | 5 min | 14.4 |
| Page (respond now) | 6 h | 30 min | 6 |
| Ticket (next business day) | 3 d | 6 h | 1 |

- Budget spent when the alert fires = burn rate × long window ÷ SLO window.
- Time until the budget runs out at that rate = SLO window ÷ burn rate.

These hold when events are spread evenly over time. With uneven traffic, the share spent when an alert fires is burn rate × (events in the long window ÷ events in the SLO window), which is larger at the busiest hours than the table shows.

| Burn rate | Long window | Spent at alert (30 days) | Spent at alert (28 days) | Exhausted in (28 days) |
|---|---|---|---|---|
| 14.4 | 1 h | 2.0% | 2.1% | 1.9 days |
| 6 | 6 h | 5.0% | 5.4% | 4.7 days |
| 1 | 3 d | 10.0% | 10.7% | 28 days |

For services with little traffic, a handful of failures can trip the short windows. Lengthen the windows, or require a minimum number of events before alerting, and say which you chose.

## Batch and deadline SLOs

A nightly job judged at 7:00, or a monthly close, produces its events in bursts: every tenant's result is decided at the same moment. Burn-rate windows do not fit. One late tenant out of a dozen can spend a large share of the budget at once, and a page sent after 7:00 cannot change that night's result. For these SLOs:

- Give the budget share of one bad run: 1 ÷ (runs per window × (1 − target)). With 12 runs a night over 28 days at a 99% target, the budget allows 3.36 bad runs, so one late tenant uses about 30% of it. If one bad run uses most of the budget, the target or the unit is wrong: count per tenant-run instead of per night, or loosen the target.
- Alert on progress while there is still time to act: "at 5:30, a tenant's run has not reached the share of work that finishes by 7:00". This is an operational trigger written as a `monitor`, not a second line below the target.
- After a miss, open a ticket. Do not page for a deadline that has already passed.

## SLOs and the backlog

An SLO is a target to keep, with no end. A backlog item is a target to change, and it closes when done. Do not put an SLO in the backlog. Put there the work that protects the SLO or raises it.
