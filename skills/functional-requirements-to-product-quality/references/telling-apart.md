# Telling the models apart

## Contents
- The subject decides the model
- Product quality vs quality in use
- Data quality vs product quality and quality in use
- Data the system itself creates
- One incident through three models

Putting a requirement in the wrong model, or under the wrong characteristic, sends the fix to the wrong place. Check every quality requirement against this page.

## The subject decides the model

| Subject | Model | Example about the delivery address |
|---|---|---|
| A person | Quality in use (effectiveness) | Buyers can place an order with the correct delivery address, all the way to the end. |
| The product | Product quality (user error protection) | The system flags an address that does not exist before the order is confirmed. |
| The data | Data quality (completeness) | Every order has a delivery address. |

Stage 3 writes only product and data subjects. If a draft requirement has a person as its subject, it belongs to stage 1; trace to the QIU instead of rewriting it.

## Product quality vs quality in use

- Performance efficiency vs efficiency. Product: how fast the system responds and how many resources it uses. In use: the time and effort a person spends reaching the goal. Payment can respond in 1.5 s while a form with many fields still makes the purchase take five minutes.
- Reliability vs trust. Product: whether the system actually keeps running, counted from records. In use: whether users believe it behaves as intended, asked of users. It may never stop, yet one wrong bill destroys trust.
- Functional suitability vs effectiveness. Product: whether the functions exist and work correctly. In use: whether users reach their goal to the end. Correct functions do not help if users cannot find them.
- User interface aesthetics and operability vs pleasure and comfort. Product: properties of the screens, such as colours or the number of steps; inspect the screens. In use: the enjoyment and physical ease that users feel; ask the users.
- Adaptability vs context coverage. Product: whether it runs on other devices and operating systems. In use: whether people get the same result in other situations. It can run everywhere while completion still drops on a slow network.
- Usability and accessibility can be measured as product properties (inspect the screens) or as quality in use (observe people using it). Decide which before writing the requirement, or two people will look at different things under the same name.

## Data quality vs product quality and quality in use

- Completeness vs integrity. Data completeness: no values are missing. Security integrity: no unauthorized access or change.
- Accuracy vs functional correctness. Data accuracy: the stored values are right. Functional correctness: the processing gives the right result. They are fixed in different places.
- Availability. Product: the system is running and usable. Data: the data needed can be retrieved. The system can be running while seven-year-old orders cannot be brought back from the archive.
- Confidentiality. Product: only authorized people get into the screens and functions. Data: the data itself can be read only by authorized people (encryption, masking). Even if the access control is breached, an encrypted phone number stays unreadable.
- Recoverability. Product: the system comes back and resumes (how fast). Data: the data survives the failure without loss and keeps its quality (how much is lost). Resuming in 30 minutes does not help if the last hour of orders is gone.
- Portability. Product: the software can be moved to another environment. Data: the data can be moved to another system with its quality preserved.
- Accessibility. Product: the screens and operations work with assistive technology. Data: the data itself has a form that assistive technology can read, such as alt text for images.
- Efficiency. Data: the way data is held (type, format, size) suits the processing. The speed of the processing itself is product performance efficiency; people's time and effort is efficiency in use.
- Precision vs functional correctness. Product: the calculation is correct to the precision needed. Data: the values are recorded finely enough. If body weight is recorded only in whole kilograms, a child's dose is coarse even when the calculation is correct.
- Traceability vs accountability. Product: an action can be traced to one person (shared accounts break this). Data: a record of access to and changes of the data is kept (when and how a value changed).
- Credibility vs trust. In use: users believe the product. Data: the data's source is sound and believable.

## Data the system itself creates

When a step of the system creates the data (a receipt-reading step, a nightly batch that builds journal entries), one incident can be written in both strands. Score it once, in the strand where it is fixed:

- The step produces a wrong result from good input (a misread amount, a journal line built from the wrong claim): product quality, functional correctness of that step. It is fixed in the code.
- The stored values are wrong, missing or out of step with each other, whatever put them there (a person's entry, an old defect, a migration, a source system), and stay wrong until the data itself is corrected: data quality. It is fixed in the data and in the checks that guard it.

Keep a QR in both strands only when each has its own failure scenario and its own fix. Restating one incident in both strands counts it twice and crowds the CUJ's limit.

## One incident through three models

Follow allergies through the food delivery app. Quality in use: "a customer with an allergy never eats a dish containing their allergen by mistake." Product quality: "dishes can be filtered by allergen" and "a menu item cannot be saved with the allergen field empty." Data quality: "every menu item has its allergen field filled in." Even when every function works, the incident still happens if the field's content does not match the real ingredients (data accuracy). Product quality and data quality are the two strands that hold up quality in use; check both.
