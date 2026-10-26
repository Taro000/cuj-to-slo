# Deriving functions from quality in use

## Contents
- The question to ask
- Heuristics by characteristic
- Secondary and indirect users
- Functions the measures need
- When no new function is needed
- What, not how well
- Quality hints
- Writing acceptance criteria

## The question to ask

For every quality-in-use requirement: "What must the system do, so that this user can reach this goal, in this context of use?" Answer with behaviour the system performs. Leave how well it performs it for stage 3.

## Heuristics by characteristic

| Characteristic | Functions it tends to need | Examples |
|---|---|---|
| Effectiveness | A function for every step of the task, the whole life of the task (create, change, cancel, return), recovery when a step fails, and a review step before anything irreversible | Online store: change and cancel an order, choose another payment method after a decline. Hospital: book, change and cancel an appointment on the web, instead of "changes by phone only". |
| Efficiency | Reuse of what users already entered, sensible defaults, shortcuts, bulk actions, search and filters | Saved address and card; reorder in one tap; copy the previous prescription; approve several expense claims at once. |
| Satisfaction > usefulness | Showing the result and what follows from it | Delivery tracking with an estimated arrival time; a receipt and invoice after payment. |
| Satisfaction > trust | Transparency about amounts and decisions, and a history the user can check | Show the final total, fees included, before confirmation; explain why a claim was returned; show the user's own order history. |
| Satisfaction > pleasure | Discovery and visible progress | Recommendations; finding nearby restaurants; a trend chart of check-up results. |
| Satisfaction > comfort | Input that needs less physical effort | Barcode or camera input instead of typing; a one-handed layout option; a dark mode for night wards. |
| Freedom from risk > economic | Prevention, confirmation before charging, detection, correction | A unique key per payment so a second payment for the same order is refused; duplicate-receipt detection; refunds; daily reconciliation with a list of mismatches. |
| Freedom from risk > health and safety | Filters, warnings and checks at the point of harm, and notices | Filter dishes by allergen; check the patient's wristband against the medicine's barcode before recording; recall notices to buyers of the item. |
| Freedom from risk > environmental | Options that avoid waste | Unattended delivery and time slots; declining disposable cutlery; picking the medicine closest to expiry first. |
| Context coverage > context completeness | A way to work in each context the requirements name | Offline recording on the ward; a low-bandwidth checkout; the currencies and languages of the named regions. |
| Context coverage > flexibility | Usually no specific function: unanticipated contexts cannot be designed one by one. Say so, and let stage 3 handle it through adaptability and modifiability | (none) |

## Secondary and indirect users

- Secondary users (operation): entry functions with validation for the people who enter data; bulk import; role and permission management; switches the operators need, such as stopping new orders when a restaurant is overloaded.
- Secondary users (maintenance): a trace ID carried by every request across services; diagnostic endpoints and structured logs; feature flags; automated deployment and rollback.
- Data producers: the functions they use to create or update the data (a sold-out button for restaurant staff; stock-change events from the warehouse system). A data error can reach the user without any software defect, so the functions that create data deserve the same attention as the ones that show it.
- Indirect users: the outputs they receive, with the content they need: the daily sales report, the reconciliation list, the shipping data file. When the output must arrive by a certain time, the deadline is quality, not function; put it in `quality_hints`.

## Functions the measures need

A quality-in-use measure or a KPI that nobody can count is a wish. When the event a measure counts happens where the product cannot see it, or the inputs name no data source for it, ask whether a function should produce the data:

| Measure needs | Function it may need |
|---|---|
| The result of a check done in another system (reconciliation in the customer's ERP) | Let the user record the result in the product, or import it |
| Answers from users (trust, usefulness) | A one-question survey shown at the end of the task |
| An event inside a step (a corrected field, a returned claim and its reason) | Emit the event, with the fields the measure counts |

Such a function traces to the QIU or KPI it makes countable. When the data can come from elsewhere, such as existing analytics events or a survey the business already runs, add no function and say where the data comes from in the coverage table. Existing telemetry and infrastructure are not FRs; stage 4 uses them as operational facts.

## When no new function is needed

Often the right answer is that the quality attaches to an existing function.

| QIU | Answer |
|---|---|
| Buyers finish checkout without waiting at busy times | No new function. The existing checkout must be fast and available: quality hints for stage 3. |
| Buyers are never charged twice | New function: a unique key per payment request; a second payment for the same order is refused. |
| Engineers find the failing component quickly | New function: a trace ID on every request across services. |
| Accounting closes the month with figures that match the payments | New function: daily reconciliation of orders against payments, with a list of mismatches. |

Write the "no new function" cases in the coverage table, naming the FR that carries the quality.

## What, not how well

| Function (FR, this stage) | Quality (stage 3 and 4) |
|---|---|
| The system calculates the order total, including shipping, coupons and points | The total is always correct, and nobody is charged twice |
| The system confirms the order | It responds within 1.5 s for 99% of requests |
| The system produces the daily sales report | It arrives by 9:00 every morning |
| The system shows the stock status on product pages and in the cart | The stock shown is never more than five minutes behind the warehouse |
| The system attaches a trace ID to every request across services | 99% of payment requests leave a complete trace |
| The system refuses to save a menu item whose allergen field is empty | (the function itself is the protection; stage 3 adds the data accuracy of the field) |

Functional suitability is not a functional requirement. "Calculates the amount" is a function; "makes no calculation errors" is a quality requirement.

## Quality hints

Write each hint as "<quality-in-use characteristic> (<QIU ID>) - <what must hold, how well>", using the characteristic's exact name: "Economic risk mitigation (QIU-03) - no double charge, even when the payment provider times out". Do not name product or data quality characteristics; stage 3 chooses them. When several FRs share one deadline or target, write the hint on each FR, word it the same way, and stage 3 will treat it as one requirement.

## Writing acceptance criteria

- Describe behaviour that anyone can observe, including what happens on error.
- One criterion per behaviour; Given/When/Then works well.
- No numbers for speed, availability or freshness: those are quality.
- An error path is what the function does when its input or its state is wrong: a malformed file, a declined card, a record that no longer exists. A function that only displays something may have none. Who may use a function is security behaviour for stage 3; do not invent permission errors to fill the criterion.

Example for "The system attaches a unique key to every payment request and refuses a second payment for the same order":

- Given an order that is already paid, when a payment request with the same key arrives, then the system returns the first result and charges nothing.
- Given a payment request whose first attempt timed out, when the buyer retries, then the system reuses the same key.
- Given two different orders, when each is paid, then each gets its own key and both are charged.
