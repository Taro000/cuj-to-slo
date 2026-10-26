# Data quality model

## Contents
- Two viewpoints
- Overview: fifteen characteristics
- Examples by characteristic
- Notes for data platforms and batch

The subject of data quality is the data: values, records and collections of data. Statements take the form "which data is in which state". Even when the system works correctly, this quality drops if the values in it are wrong. Data errors can also reach users directly from the way the data is produced (the business work of entering it), without passing through any software defect. That is why data is a second strand next to product quality, not a part of it.

The numbers in the examples only make them concrete. A stage 3 quality requirement carries a number only when the inputs state it (`stated_numbers`).

## Two viewpoints

- Inherent: properties of the data itself, such as value ranges and business rules, relations between values, and metadata. You find them by examining the values.
- System-dependent: how well the system keeps the data's quality and delivers it. It depends on the hardware, on mechanisms such as backups, and on the capabilities of migration tools. You find it by examining the mechanisms that keep and deliver the data.

Some characteristics belong to one viewpoint, some to both. For a characteristic that has both, choose the QR's `layer` by where the requirement is met: by the values themselves (`data-inherent`), or by how the system keeps and delivers them (`data-system-dependent`). When both matter, write two QRs.

## Overview

| Viewpoint | Characteristic | Meaning |
|---|---|---|
| Inherent only | Accuracy | Represents the true value correctly. |
| | Completeness | The expected attributes, and the related entities, have values. |
| | Consistency | Free of contradiction, and coherent with other data. |
| | Credibility | Regarded as true and believable. |
| | Currentness | Is of the right age: the latest value. |
| Both | Accessibility | Can be accessed by people who need assistive technology. |
| | Compliance | Follows the relevant standards, regulations and conventions. |
| | Confidentiality | Can be used and interpreted only by authorized users. |
| | Efficiency | Can be processed with suitable resources and gives the expected performance. |
| | Precision | Fine enough to make the distinctions that are needed. |
| | Traceability | A record of access to the data and changes to it is kept. |
| | Understandability | Readable in suitable languages, symbols and units. |
| System-dependent only | Availability | Authorized users and applications can retrieve it. |
| | Portability | Can be moved to another system with its quality preserved. |
| | Recoverability | Keeps its quality, and operations on it continue, even when failures occur. |

Data "completeness" means that no values are missing. It is not the security sense of "integrity" (no unauthorized change), which belongs to product quality.

## Examples by characteristic

### Inherent only

| Characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Accuracy | Prices in the price master match the prices actually charged. | A restaurant's coordinates point to its actual entrance. | A patient's date of birth matches their identity document. |
| Completeness | Every order has a delivery address and line items. | Every menu item has its allergen field filled in ("none" is entered when there are none). | Every inpatient has their allergy status recorded. |
| Consistency | Every order total equals the sum of its line items. | A restaurant's opening hours agree between the restaurant data and the menu data. | The discharge date is after the admission date and agrees with the ward records. |
| Credibility | Reviews come only from actual buyers and are linked to purchase records. | Restaurant addresses are registered as checked against their business licence. | Test values in a referral letter carry the sending institution's signature, so their source can be verified. |
| Currentness | Stock counts are never more than 5 minutes behind the warehouse's actual counts. | A sold-out registration reaches the menu in the app within 60 seconds. | Drug information (doses, contraindications) is updated to the latest revision. |

### Both viewpoints

| Characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Accessibility | Product images have alt-text data, so a screen reader can convey them. | Menus exist as text data, not only as images, so they can be read aloud. | Test results can also be output as data that can be read by voice or in braille. |
| Compliance | Product codes follow the industry-standard barcode scheme. | Allergens are held under the item names that food labelling rules define. | Diagnoses are recorded with the national standard codes. |
| Confidentiality | Members' phone numbers are stored encrypted, unreadable without the key. | Order data handed over for analysis has names and phone numbers masked. | Particularly sensitive items, such as diagnoses, are stored encrypted. |
| Efficiency | Product images are kept in resolutions and formats that match their display sizes. | Addresses are also held as coordinates, so distance calculations need no conversion each time. | Test values are held as numbers, not text, so trend charts can be drawn at once. |
| Precision | Weights are held in 10 g units, fine enough to separate the shipping bands. | Courier positions are held to a few metres, fine enough to tell building entrances apart. | Children's weights are recorded in 0.1 kg units, fine enough for dose calculation. |
| Traceability | The history of price changes (when, from what, to what) is kept with the product data. | The history of changes to allergen information is kept, so what was shown at any moment can be checked later. | When a chart is corrected, the content before the correction, the person who corrected it and the time all remain. |
| Understandability | Product dimensions carry units, so centimetres and inches are not confused. | Each spiciness level from 1 to 5 has a described meaning (metadata). | Test values carry units (such as mg/dL) and reference ranges, so they are not misread. |

### System-dependent only

| Characteristic | Online store | Food delivery app | Hospital |
|---|---|---|---|
| Availability | Support staff can retrieve seven-year-old order data when they need it. | Restaurants can retrieve the previous day's sales data the next morning. | A ten-year-old chart can be retrieved from the archive and viewed within minutes. |
| Portability | On a move to another e-commerce platform, product and customer data move without loss or garbled text. | A restaurant moving to another delivery service can export its menu data and take it along. | When the record system is replaced, past charts move to the new system unchanged. |
| Recoverability | From backups and update logs, every order up to just before a failure can be restored. | If a terminal crashes out of signal range, the delivery-complete records stay on it and are sent once it reconnects. | If a disaster destroys the on-site servers, charts can be restored from backups at a distant site. |

## Notes for data platforms and batch

| Characteristic | What it usually means for a pipeline or warehouse |
|---|---|
| Currentness | Freshness of tables or partitions relative to the source events; late-arriving data. |
| Completeness | Row counts against the source; null rates in required columns; missing partitions. |
| Consistency | The same metric gives the same number in every mart; references between tables hold; no duplicates. |
| Accuracy | Reconciliation against the source of truth (for example, finance); validation rules. |
| Credibility | Lineage back to trusted sources; datasets certified by an owner. |
| Traceability | Lineage plus change logs, including who changed a metric definition. |
| Understandability | A data dictionary; units and time zones; metric definitions. |
| Compliance | Rules for personal data and retention; standard codes. |
| Precision | Timestamp granularity and time zone; decimal precision for money. |
| Efficiency | Partitioning, file formats and column types suited to the queries. |
| Availability and recoverability | Backfills, snapshots and restore; the ability to recompute a past day. |
| Portability | Export formats; moving between warehouses. |

A data quality requirement can be met in four places: the data itself (resynchronizing, correcting stored values), the software (validation on intake, retries in the integration), the hardware (capacity of the integration queue) and people's business procedures (when stock gets registered). Only the first three lead to software requirements; when only the fourth can meet the need, record it under "Out of scope".
