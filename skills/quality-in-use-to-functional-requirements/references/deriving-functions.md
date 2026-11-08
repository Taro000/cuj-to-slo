# Deriving functions from quality in use

## Contents
- The question to ask
- Heuristics by characteristic
- Secondary and indirect users
- Functions the measures need
- When no new function is needed
- What, not how well
- What, not how
- Quality hints
- Writing acceptance criteria

## The question to ask

For every quality-in-use requirement: "What must the system do, so that this user can reach this goal, in this context of use?" Answer with behaviour the system performs, as someone outside the system would see it. Leave how well it performs it for stage 3, and how the system does it for design.

## Heuristics by characteristic

| Characteristic | Functions it tends to need | Examples |
|---|---|---|
| Effectiveness | A function for every step of the task, the whole life of the task (create, change, cancel, return), recovery when a step fails, and a review step before anything irreversible | Online store: change and cancel an order, choose another payment method after a decline. Hospital: book, change and cancel an appointment in the system, instead of "changes by phone only". |
| Efficiency | Reuse of what users already entered, sensible defaults, shortcuts, bulk actions, search and filters | Saved address and card; reorder in one tap; copy the previous prescription; approve several expense claims at once. |
| Satisfaction > usefulness | Showing the result and what follows from it | Delivery tracking with an estimated arrival time; a receipt and invoice after payment. |
| Satisfaction > trust | Transparency about amounts and decisions, and a history the user can check | Show the final total, fees included, before confirmation; explain why a claim was returned; show the user's own order history. |
| Satisfaction > pleasure | Discovery and visible progress | Recommendations; finding nearby restaurants; a trend chart of check-up results. |
| Satisfaction > comfort | Input that needs less physical effort | Barcode or camera input instead of typing; a one-handed layout option; a dark mode for night wards. |
| Freedom from risk > economic | Prevention, confirmation before charging, detection, correction | Charge an order at most once, even when the payment is sent again; detect a receipt submitted twice; refunds; match orders against payments and list the mismatches. |
| Freedom from risk > health and safety | Filters, warnings and checks at the point of harm, and notices | Filter dishes by allergen; check that the medicine matches the patient before it is recorded as given; recall notices to buyers of the item. |
| Freedom from risk > environmental | Options that avoid waste | Unattended delivery and time slots; declining disposable cutlery; picking the medicine closest to expiry first. |
| Context coverage > context completeness | A way to work in each context the requirements name | Recording on the ward while the connection is down; a checkout that works on a slow connection; the currencies and languages of the countries the requirements name. |
| Context coverage > flexibility | Usually no specific function: unanticipated contexts cannot be designed one by one. Say so, and let stage 3 handle it through adaptability and modifiability | (none) |

## Secondary and indirect users

- Secondary users (operation): entry functions with validation for the people who enter data; bulk import; role and permission management; switches the operators need, such as stopping new orders when a restaurant is overloaded.
- Secondary users (maintenance): a way to follow one failed request end to end through the system, starting from something the engineer already has, such as the order; a way to see which part of the system a failure is in; a way to switch a new behaviour off until it is fixed. Write what the engineer can do. How the system makes it possible (IDs carried between services, what the logs contain) is design.
- Data producers: the functions they use to create or update the data (a sold-out button for restaurant staff; taking in the stock changes the warehouse system makes). A data error can reach the user without any software defect, so the functions that create data deserve the same attention as the ones that show it.
- Indirect users: the outputs they receive, with the content they need: the monthly sales report, the list of mismatches, the shipping data file. When the output must arrive by a certain time, the deadline is quality, not function; put it in `quality_hints`.

## Functions the measures need

A quality-in-use measure or a KPI that nobody can count is a wish. When what a measure counts happens where the product cannot see it, or the inputs name no data source for it, ask whether a function should produce the data:

| Measure needs | Function it may need |
|---|---|
| The result of a check done in another system (reconciliation in the customer's ERP) | Let the user record the result in the product, or import it |
| Answers from users (trust, usefulness) | A one-question survey shown at the end of the task |
| Something that happens inside a step (a buyer reaching the payment step, a corrected field, a returned claim and its reason) | The system records that it happened, with what the measure counts: "the system records that a buyer reached the payment step" |

Write such a function as behaviour, never as an event, a log line or a field: which events, logs or fields carry the data, and how it is collected, is design. The function traces to the QIU or KPI it makes countable; tell the user to re-run stage 1 for that item, so that its `observable` and its open gap follow. When the data can come from elsewhere, such as telemetry that already exists, records a team already keeps or a survey the business already runs, add no function, and say where the data comes from in the coverage table. Telemetry and records that already exist are operational facts (OPF), not FRs: one that `context.yaml` lacks becomes a new OPF entry, written by this stage.

## When no new function is needed

Often the right answer is that the quality attaches to an existing function.

| QIU | Answer |
|---|---|
| Buyers finish checkout without waiting at busy times | No new function. The existing checkout must be fast and available: quality hints for stage 3. |
| Buyers are never charged twice | New function: the system charges an order at most once, even when the payment request is sent again. |
| Engineers find the failing component quickly | New function: the system lets engineers follow one failed request end to end. |
| Accounting closes the month with figures that match the payments | New function: the system matches orders against payments and lists the mismatches for accounting. |

Write the "no new function" cases in the coverage table, naming the FR that carries the quality.

## What, not how well

| Function (FR, this stage) | Quality (stage 3 and 4) |
|---|---|
| The system calculates the order total, including shipping, coupons and points | The total is always correct, and nobody is charged twice |
| The system confirms the order | It responds within 1.5 s for 99% of requests |
| The system produces the monthly sales report for accounting | It arrives in time for the month-end close |
| The system shows the stock status on product pages and in the cart | The stock shown is never more than five minutes behind the warehouse |
| The system lets engineers follow a failed checkout end to end, by its order | Every failed checkout can be followed, with no step missing |
| The system refuses to save a menu item whose allergen field is empty | (the function itself is the protection; stage 3 adds the data accuracy of the field) |

Functional suitability is not a functional requirement. "Calculates the amount" is a function; "makes no calculation errors" is a quality requirement.

## What, not how

A statement and its acceptance criteria describe what someone outside the system can see: what a user sees and can do, what another system receives, what is recorded and for whom. The mechanism that produces it is design, and there is always more than one. A mechanism written here is copied into every later stage, and each copy has to be rewritten when design picks another.

| How (design: keep it out) | What (FR, this stage) |
|---|---|
| The system attaches a unique key to every payment request | The system charges an order at most once, even when the payment request for it is sent again |
| The system attaches a trace ID to every request and passes it between services | The system lets engineers follow one failed checkout end to end, by its order |
| The system consumes stock-change events from the warehouse system | When the stock of an item changes in the warehouse system (EXT-02), the system shows the new stock status |
| A nightly job matches orders against payments | The system matches each order against the payments taken for it and lists the mismatches for accounting |
| The system retries a payment call that timed out | When the payment provider does not answer, the buyer sees that the payment is not finished and can pay again without being charged twice |

Check every statement, criterion and interface for keys, tokens, IDs carried between services, caches, queues, retries, batches and events, paths and endpoints, tables and fields, and the names of products, platforms and data stores. Each is a design choice, unless it already exists in a running product; then it may be named as a fact.

## Quality hints

Write each hint as "<quality-in-use characteristic> (<QIU ID>) - <what must hold, how well>", using the characteristic's exact name in the working language (`glossary.md`): "Economic risk mitigation (QIU-03) - no double charge, even when the payment provider does not answer". Do not name product or data quality characteristics; stage 3 chooses them. When several FRs share one deadline or target, write the hint on each FR, word it the same way, and stage 3 will treat it as one requirement.

## Writing acceptance criteria

- Describe behaviour that anyone outside the system can observe, including what happens on error, and never the mechanism behind it.
- One criterion per behaviour; Given/When/Then works well.
- No numbers for speed, availability or freshness: those are quality.
- An error path is what the function does when its input or its state is wrong: a malformed file, a declined card, a record that no longer exists. A function that only displays something may have none. Who may use a function is security behaviour for stage 3; do not invent permission errors to fill the criterion.

Example for "The system charges an order at most once, even when the payment request for it is sent again":

- Given an order that is already paid, when a payment request for the same order arrives again, then the system returns the first result and charges nothing.
- Given a payment that showed the buyer no result, when the buyer pays for the same order again, then the order is charged at most once and the buyer sees whether it was paid.
- Given two different orders with the same items and total, when each is paid, then both are charged.

None of them says how the system tells that two payment requests are for the same order; design decides that.
