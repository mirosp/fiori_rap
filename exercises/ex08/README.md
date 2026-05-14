# HANDS-ON EXERCISE 8

## Introduction
In this hands-on exercise, you will handle a Validations.

### Validations on Save (Business Rules) ✅❌
Header validations (root behavior):
- `validateCustomer` (SoldToParty mandatory)
- `validateAmounts` (NetAmount > 0, TaxAmount >= 0)
- `validateDocumentDate` (mandatory and not in the future)
- `validateCurrency` (CurrencyCode mandatory)

Defined here:
- [`ZR_DVSO_H validations`](../../source/ZR_DVSO_H-bdef.txt#L43-L47)

Item validation:
- `validateItemsSum on save { create; update; field NetAmount; }`
- Defined here: [`Item~validateItemsSum`](../../source/ZR_DVSO_H-bdef.txt#L122-L123)
- Orchestrated via draft prepare: [`Prepare triggers Item~validateItemsSum`](../../source/ZR_DVSO_H-bdef.txt#L59)

Implementation (draft-aware reads in LOCAL MODE + associations):
- [`validateItemsSum implementation`](../../source/ZBP_R_DVSO_H-clas.txt#L123-L177)

