# HANDS-ON EXERCISE 3

## Introduction
In this hands-on exercise, you will create a Interface CDS View.

### Parent–Child Model (Header → Items) with Composition
The BO is modeled around:
- **Root entity (Header):** `ZR_DVSO_H`

```
@AccessControl.authorizationCheck: #NOT_REQUIRED
@Metadata.allowExtensions: true

@EndUserText.label: '###GENERATED Core Data Service Entity'
define root view entity ZR_DVSO_H
  as select from zdv_so_h as Header

  association [0..1] to ZI_Customer as _Customer on  $projection.SoldToParty = _Customer.CustomerID

  composition [0..*] of ZR_DVSO_I   as _Items // Parent–Child Relationship
{
  key so_uuid               as SoUUID,
      sales_order           as SalesOrder,
      sold_to_party         as SoldToParty,
      document_date         as DocumentDate,
      @Consumption.valueHelpDefinition: [ { entity.name: 'I_CurrencyStdVH',  entity.element: 'Currency',
                                            useForValidation: true } ]
      currency_code         as CurrencyCode,
      @Semantics.amount.currencyCode: 'CurrencyCode'
      net_amount            as NetAmount,
      @Semantics.amount.currencyCode: 'CurrencyCode'
      tax_amount            as TaxAmount,
      @Semantics.amount.currencyCode: 'CurrencyCode'
      gross_amount          as GrossAmount,
      overall_status        as OverallStatus,
      @Semantics.user.createdBy: true
      local_created_by      as LocalCreatedBy,
      @Semantics.systemDateTime.createdAt: true
      local_created_at      as LocalCreatedAt,
      @Semantics.user.localInstanceLastChangedBy: true
      local_last_changed_by as LocalLastChangedBy,
      @Semantics.systemDateTime.localInstanceLastChangedAt: true
      local_last_changed_at as LocalLastChangedAt,
      @Semantics.systemDateTime.lastChangedAt: true
      last_changed_at       as LastChangedAt,

      /* Asociaciones */
      _Customer,
      _Items

}
```
  
- **Child entity (Items):** [`ZR_DVSO_I`](source/ZR_DVSO_I-ddls.txt)

The service exposes projections:
- **Header projection:** [`ZC_DVSO_H`](source/ZC_DVSO_H-ddls.txt)
- **Item projection:** [`ZC_DVSO_I`](source/ZC_DVSO_H-ddls.txt)

Redirected composition is visible in the header projection:
- `_Items redirected to composition child ZC_DVSO_I`: [`ZC_DVSO_H`](source/ZC_DVSO_H-ddls.txt)
