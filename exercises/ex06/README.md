# HANDS-ON EXERCISE 6

## Introduction
In this hands-on exercise, you will handle a Value Helps and Text associations.

### Value Helps (F4) + Text Associations
Value helps are wired in the projection views using `@Consumption.valueHelpDefinition` and text associations (`@ObjectModel.text.element`):

- Header value helps + text: [`ZC_DVSO_H`](../../source/ZC_DVSO_H-ddls.txt#L17-L27)
```ABAP
  @ObjectModel.text.element: ['SoldToPartyName']
  @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_Customer', element: 'CustomerID' }, useForValidation: true }]
  SoldToParty,

  @Semantics.text: true
  _Customer.LastName          as SoldToPartyName,

  DocumentDate,

  @Consumption: { valueHelpDefinition: [ { entity.element: 'Currency', entity.name: 'I_CurrencyStdVH', useForValidation: true } ] }
  CurrencyCode,
```
---
- Item value helps (product/unit/etc.): `ZC_DVSO_I`
```
todo
```
---
