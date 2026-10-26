# Quality-in-use model

## Contents
- The five characteristics
- Examples by sub-characteristic
- The subject decides the model
- Easily confused with product quality
- One incident through three models

Quality is the degree to which something meets the stated and implied needs of its stakeholders. Quality in use is the quality of the result when a person uses the system: a specific user, in a specific context of use, reaching their own goal. Walk the whole model as a checklist for every CUJ, then keep only the characteristics for which you can write a concrete failure scenario.

## The five characteristics

| Characteristic | Sub-characteristic | Meaning |
|---|---|---|
| Effectiveness | (none) | Users reach their goal accurately and completely. Online store: the share of buyers who finish a purchase correctly. |
| Efficiency | (none) | The time, effort or cost users spend to reach the goal is low. Online store: the time it takes to finish a purchase. |
| Satisfaction | Usefulness | Users feel the goal was achieved, including the results and what follows from them. |
| | Trust | Users are confident that the product will behave as intended. |
| | Pleasure | Using the product gives users the pleasure of meeting a personal need. |
| | Comfort | The product is physically comfortable to use. |
| Freedom from risk | Economic risk mitigation | Limits the risk of losing money, property, business or reputation. |
| | Health and safety risk mitigation | Limits the risk of harm to people's health and safety. |
| | Environmental risk mitigation | Limits the risk of harm to property or the environment. |
| Context coverage | Context completeness | Users get effectiveness, efficiency, freedom from risk and satisfaction in every context of use named in the requirements. |
| | Flexibility | Users get the same results in contexts of use that the requirements did not anticipate. |

## Examples by sub-characteristic

Each example has the form "who, in what situation, ends up how". Users are rarely of one kind: in a hospital, patients, doctors and nurses each use the system for their own goals. The numbers in these examples only illustrate; in a real requirement, a number comes from the input or is a named placeholder.

### Effectiveness
- Online store: buyers order the items they want, with the right quantity and delivery address, all the way to the end.
- Food delivery app: customers receive exactly the dishes they ordered, at the place they specified.
- Hospital: patients book the department they need within the week they wanted.
- B2B expense service: employees submit an expense claim that passes approval without being sent back.
- Data product: store managers see yesterday's sales for their store before the morning meeting and set today's staffing from them.

### Efficiency
- Online store: repeat buyers finish a second purchase in one minute, without re-entering their address or card.
- Food delivery app: restaurant staff mark a dish as sold out in a few seconds, without stopping cooking.
- Hospital: nurses record one medication administration in about 30 seconds.
- B2B expense service: approvers clear a week of claims in five minutes.
- Data product: analysts answer a routine question from the published tables without writing a new query.

### Satisfaction > usefulness
- Online store: buyers find that the delivered item matches its description and are glad they bought it.
- Food delivery app: customers get warm food at the promised time and are glad they ordered.
- Hospital: patients wait less thanks to web booking and are glad they booked that way.

### Satisfaction > trust
- Online store: buyers feel safe entering card details, and believe the total on the confirmation screen.
- Food delivery app: accounting pays restaurants the month-end settlement amounts the app produced, as they are.
- Hospital: doctors rely on the drug-interaction warnings when deciding on prescriptions.
- B2B expense service: finance closes the month with the exported totals without re-checking each claim.

### Satisfaction > pleasure
- Online store: buyers enjoy finding new products they like through recommendations.
- Food delivery app: customers enjoy discovering nearby restaurants they did not know.
- Hospital: patients are pleased to see their improvement in the trend of their check-up results.

### Satisfaction > comfort
- Online store: buyers operating a phone with one hand do not tire their fingers or wrists.
- Food delivery app: couriers wearing gloves operate the app while stopped, without awkward posture.
- Hospital: nurses recording in a dark ward at night do not get eye strain from glare.

### Freedom from risk > economic risk mitigation
- Online store: no double charges or wrong bills, so neither buyers nor the business bear refund or inquiry costs.
- Food delivery app: restaurants rarely lose ingredients and sales to cancellations or remakes after an order.
- Hospital: the hospital rarely wastes appointment slots on double bookings or no-shows.
- B2B expense service: approvers never approve a second claim for the same receipt, so their company never reimburses it twice.

### Freedom from risk > health and safety risk mitigation
- Online store: buyers of a recalled item reliably receive the notice, and no accident happens.
- Food delivery app: customers with allergies never eat a dish containing their allergen by mistake.
- Hospital: no patient is ever given another patient's medication.

### Freedom from risk > environmental risk mitigation
- Online store: buyers choose unattended delivery or a time slot, which cuts the extra trips and emissions of redelivery.
- Food delivery app: customers can decline disposable cutlery, which reduces waste.
- Hospital: pharmacists use the medicines closest to expiry first, which reduces discarded medicine.

### Context coverage > context completeness

A context coverage requirement repeats the formula of another requirement in a different context, and judges the gap between the two values.

- Online store: purchase completion is at the same level in every context the requirements name: smartphone, PC and slow network.
- Food delivery app: customers receive their orders without confusion in every named context: rain, late night and apartment buildings.
- Hospital: nurses record equally well in every named setting: outpatient, ward and emergency.

### Context coverage > flexibility
- Online store: overseas buyers, whom nobody anticipated, complete purchases in the same way.
- Food delivery app: customers can order in the same way for in-store pickup, a use nobody anticipated.
- Hospital: nurses keep recording in a temporary disaster ward that nobody anticipated.

## The subject decides the model

The subject of a quality-in-use requirement is always a user.

| Subject | Model | Example about the delivery address |
|---|---|---|
| A person | Quality in use (effectiveness) | Buyers can place an order with the correct delivery address, all the way to the end. |
| The product | Product quality (user error protection) | The system flags an address that does not exist before the order is confirmed. |
| The data | Data quality (completeness) | Every order has a delivery address. |

Statements whose subject is the product or the data belong to stage 3. Record them as hints for later stages.

## Easily confused with product quality

- Performance efficiency vs efficiency: the product's response speed and resource use, against the time and effort a person spends reaching the goal. Payment can respond in 1.5 s while a form with many fields still makes the purchase take five minutes.
- Reliability vs trust: whether the product actually keeps running (counted from records), against whether users believe it behaves as intended (asked of users). One wrong bill destroys trust even without any downtime.
- Functional suitability vs effectiveness: whether the functions exist and work correctly, against whether users reach their goal to the end. Correct functions do not help if users cannot find them.
- User interface aesthetics and operability vs pleasure and comfort: properties of the screens, such as colours or the number of steps (inspect the screens), against the enjoyment and physical ease that users feel (ask the users).
- Adaptability vs context coverage: whether the product runs on other devices or operating systems, against whether people get the same result in other situations. The product can run everywhere while completion still drops on a slow network.

Usability and accessibility can be measured either as product properties (inspect the screens) or as quality in use (observe people using it). Decide which, before writing anything; otherwise two people will look at different things under the same name.

## One incident through three models

Follow allergies through the food delivery app. Quality in use: "a customer with an allergy never eats a dish containing their allergen by mistake." Product quality: "dishes can be filtered by allergen" and "a menu item cannot be saved with the allergen field empty." Data quality: "every menu item has its allergen field filled in." Even when every function works, the incident still happens if the field's content does not match the real ingredients (data accuracy). Quality in use is what happens to people; product quality and data quality are the two strands that support it.
