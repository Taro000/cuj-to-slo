# Product quality model

## Contents
- Overview: eight characteristics, 31 sub-characteristics
- Examples by sub-characteristic
  - Functional suitability
  - Performance efficiency
  - Compatibility
  - Usability
  - Reliability
  - Security
  - Maintainability
  - Portability
- Notes for B2B SaaS and data platforms

The subject of product quality is the product: the software and the computer system. Statements take the form "the system does …" or "the component is …". You can check them without asking users: by testing, inspection or production records.

External quality is how the system behaves when it runs; this is where SLIs and SLOs live. Internal quality is a property of the code and design; it is kept by CI gates and the Definition of Done. Write external quality first, and derive internal quality from it.

## Overview

| Characteristic | Sub-characteristic | Meaning |
|---|---|---|
| Functional suitability. Signals: calculation errors, wrong bills | Functional completeness | The set of functions covers every specified task and user objective. |
| | Functional correctness | Gives correct results with the precision needed. |
| | Functional appropriateness | The functions help users complete their tasks and objectives, with no unnecessary steps. |
| Performance efficiency. Signals: p99 latency, CPU usage, concurrent-connection limits | Time behaviour | Response time, processing time and throughput meet the requirements. |
| | Resource utilization | The amounts and kinds of resources used (CPU, memory, bandwidth and so on) meet the requirements. |
| | Capacity | The maximum limits (concurrent users, data volume, transactions and so on) meet the requirements. |
| Compatibility. Signals: failure rate of calls to external APIs | Co-existence | Works while sharing an environment and resources with other products, without harming them or being harmed. |
| | Interoperability | Exchanges information with other systems and uses the information exchanged. |
| Usability: properties of the product that make it easy to use. Signals: input error rate, accessibility violations | Appropriateness recognizability | Users can recognize whether the product suits their needs. |
| | Learnability | Users can learn to use the product to reach their goals. |
| | Operability | Easy to operate and control. |
| | User error protection | Protects users against making errors. |
| | User interface aesthetics | The interaction with the screens is pleasing and satisfying. |
| | Accessibility | Usable by people with the widest range of characteristics and capabilities. |
| Reliability: keeps running, and recovers when it stops. Signals: success rate, downtime, recovery time | Maturity | Rarely fails in normal operation. |
| | Availability | Running and usable when it is needed. |
| | Fault tolerance | Works as intended despite hardware or software faults. |
| | Recoverability | After an interruption or failure, recovers the affected data and returns to the desired state. |
| Security: protects information and data according to authorization. Signals: unauthorized access, missing audit logs | Confidentiality | Data is available only to those authorized to access it. |
| | Integrity | Prevents unauthorized access to, or modification of, programs and data. |
| | Non-repudiation | Actions and events can be proven to have happened, so that they cannot be denied later. |
| | Accountability | The actions of an entity can be traced uniquely to that entity. |
| | Authenticity | The identity of a subject or resource can be proven to be the one claimed. |
| Maintainability: easy to change and to verify. Signals: time per change, change failure rate | Modularity | Built of components such that changing one has minimal impact on the others. |
| | Reusability | Components and assets can be used in more than one system, or in building other assets. |
| | Analysability | The impact of a change can be assessed, and the causes of defects or failures and the parts to change can be identified. |
| | Modifiability | Can be changed without introducing defects or degrading quality. |
| | Testability | Test criteria can be set, and tests against them can be run efficiently. |
| Portability: can be moved to another environment. Signals: failures caused by differences between environments | Adaptability | Can be adapted to different or changing hardware, software and usage environments. |
| | Installability | Can be installed and uninstalled successfully in a specified environment. |
| | Replaceability | Can replace another product with the same purpose, in the same environment. |

## Examples by sub-characteristic

Three columns, the same three businesses throughout: an online store, a food delivery app and a hospital. Compare a row with the same row in the quality-in-use examples to see which property of the product supports which result for people.

The numbers in these examples only make them concrete. A stage 3 quality requirement carries a number only when the inputs state it (`stated_numbers`); stage 4 sets measures and targets.

### Functional suitability

| Sub-characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Functional completeness | Every function the buyer's task needs is there: creating, changing, cancelling and returning orders. | Dishes show their allergens and can be filtered by them, so customers can avoid allergens. | Appointments can be booked, changed and cancelled on the web (not "changes by phone only"). |
| Functional correctness | With shipping, coupons and points combined, the total is still correct and nobody is charged twice. | Settlement amounts to restaurants are correct to the smallest currency unit after fees. | Children's doses are calculated from body weight with the correct number of digits. |
| Functional appropriateness | Guests can buy without registering: no step the purchase does not need. | Accepting an order and starting to cook takes one tap. | The previous prescription can be copied in one operation. |

### Performance efficiency

| Sub-characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Time behaviour | 99% of payments respond within 1.5 s. | Even at lunch peak, order confirmation responds within 2 s. | Five years of test results appear within 3 s of opening a chart. |
| Resource utilization | At sale peak, server CPU stays at or below 70%. | Sending its location continuously, the courier app uses at most 10% of the battery per hour. | The chart screen uses at most 1 GB of memory and runs on the old ward PCs. |
| Capacity | Accepts 300 payments per second at sale peak. | Accepts 1,200 order confirmations per second at lunch peak. | Stores 20 years of charts for one million patients. |

### Compatibility

| Sub-characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Co-existence | Analytics tools on the same servers do not slow down purchase processing. | Running beside map and music apps on the courier's phone, neither app stops the other. | The medical record and billing systems open on the same terminal without disturbing each other. |
| Interoperability | Exchanges payment results with the payment provider and confirms orders from them. | Exchanges orders with the restaurant's POS register and books them as register sales. | Receives results from lab instruments and shows them as test values in the chart. |

### Usability

| Sub-characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Appropriateness recognizability | Product pages show the delivery areas and payment methods, so buyers can tell whether the store works for them. | On opening the app, customers see whether their home is in the delivery area. | The top page of the booking site shows whether first-time patients can book on the web. |
| Learnability | With a guide and input examples, new catalogue staff register their first product within 30 minutes. | A practice mode lets new couriers deliver alone from their first day. | The screens follow the same order as the paper records; new nurses can record medication after one hour of training. |
| Operability | Quantities in the cart can be changed in place, and the buyer can always go back a screen. | When the kitchen is overloaded, the restaurant stops accepting orders with a single switch. | The main chart operations can be done with the keyboard alone. |
| User error protection | An address that does not exist, or a card number with missing digits, is flagged before confirmation. | A menu item cannot be saved while its allergen field is empty. | When the patient's wristband and the medicine's barcode do not match, recording stops with a warning. |
| User interface aesthetics | Large photos and consistent colour and spacing make the pages pleasant to look at. | Delivery progress appears on an animated map that is enjoyable to watch while waiting. | The patient app's calm colours and tidy layout look reassuring. |
| Accessibility | Choosing a product and buying it both work with a screen reader. | At 200% text size, the order button stays on the screen. | People with colour vision differences can tell open slots by a symbol, not by colour alone. |

### Reliability

| Sub-characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Maturity | In normal operation, purchase processing stops because of a failure at most once a quarter. | In normal operation, the courier app crashes at most once per 10,000 launches. | During normal outpatient hours, the chart system stops because of a failure at most once a year. |
| Availability | Apart from planned downtime, buying is possible 99.9% of the time in a month. | Orders are accepted throughout business hours (10:00 to 24:00). | The emergency department can open charts 24 hours a day, every day. |
| Fault tolerance | When one payment server fails, purchases continue on the others. | When the map service does not respond, couriers keep delivering with the address shown as text. | When the network drops, the latest charts kept on the terminal can still be viewed. |
| Recoverability | When the database fails, purchases resume within 30 minutes. | After the app crashes, restarting it restores the order in delivery and its progress. | After a server failure, the system returns to the state just before it and care resumes within an hour. |

### Security

| Sub-characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Confidentiality | Buyers see only their own order history, never other people's addresses. | Couriers see only the address of the order they are delivering, and lose sight of it after delivery. | Staff who are not treating a patient cannot open that patient's chart. |
| Integrity | Even if the price sent from the screen is tampered with, the total is calculated from server-side prices. | Couriers cannot change their own delivery counts or pay data. | Finalized chart entries cannot be changed by anyone without the right. |
| Non-repudiation | If a buyer says "I never ordered this", the record of the authenticated order proves otherwise. | A photo and timestamp of an unattended delivery prove that it was delivered. | A doctor's electronic signature makes it impossible to deny later having prescribed. |
| Accountability | Each staff member works under their own account, so the person who changed a price can be identified. | Even on the restaurant's shared terminal, staff log in individually, so whoever cancelled an order can be traced. | Which staff member opened which patient's chart, and when, can be traced person by person. |
| Authenticity | Logins are verified with a password and a confirmation code. | Payment result notifications are verified by signature as coming from the real payment company. | Terminal users are verified with their staff card and PIN. |

### Maintainability

| Sub-characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Modularity | Changing the shipping calculation does not require touching the payment or stock components. | Switching to another map provider changes only the map component. | Updating the drug information component leaves booking and billing untouched. |
| Reusability | The web store and the app use the same price calculation component. | The login component built for the customer app is reused in the restaurant and courier apps. | One booking component serves outpatient, check-up and rehabilitation bookings. |
| Analysability | Before the shipping calculation changes, every affected screen and process can be listed. | When order failures rise, trace IDs show at once which restaurant and payment method are involved. | From a prescription defect report, the code to fix is found within a day. |
| Modifiability | A tax rate change is applied by changing a setting, without defects. | A new payment method is added within a week without breaking the existing ones. | Changes for a fee revision go in by the deadline without breaking existing calculations. |
| Testability | A stub of the payment provider lets purchases be tested automatically without real payments. | Simulated courier locations let the arrival-time calculation be tested. | Every dosing rule has a test, and each change is verified automatically within minutes. |

### Portability

| Sub-characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Adaptability | New browsers and phone OS versions work as they are, or with small fixes. | The restaurant app keeps its layout on tablets of different sizes. | Moving from on-site servers to the cloud needs only configuration changes. |
| Installability | A new version reaches production in 10 minutes through an automated procedure. | Couriers install the app from the store in a minute, and uninstalling leaves no data behind. | The chart app can be installed on 50 ward PCs at once. |
| Replaceability | A new search engine replaces the old one behind the same call interface. | After an app update, saved addresses and favourites still work. | Staff switching from another vendor's record system keep the same workflow. |

## Notes for B2B SaaS and data platforms

| Characteristic | B2B SaaS | Data platform and batch |
|---|---|---|
| Functional suitability | Tenant-specific rules (approval chains, tax rules) are applied correctly. | Transformations produce correct aggregates; reruns give the same result. |
| Performance efficiency | Large tenants' bulk operations finish in time; per-tenant limits protect the others. | Jobs finish inside their batch window; compute cost per run; growth in data volume. |
| Compatibility | Tenants sharing infrastructure do not slow each other down; integration with customers' identity providers and ERPs. | Schema contracts with upstream and downstream systems. |
| Usability | Tenant administrators can configure the service without support; role-specific screens. | People can find the right dataset and tell whether it suits their question. |
| Reliability | Availability per tenant (a global average hides one tenant's outage); success of asynchronous jobs. | Pipeline run success; retries and backfills after a failure. |
| Security | Isolation between tenants; per-user audit trails; single sign-on. | Column-level access control and masking of personal data. |
| Maintainability | Tenant configuration changes without code changes. | Lineage that shows what a change affects; tests for pipelines. |
| Portability | Deploying to another region or on premises; exporting a tenant's data. | Moving between warehouses or file formats. |
