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
  
- **Child entity (Items):** `ZR_DVSO_I`

```
@AccessControl.authorizationCheck: #NOT_REQUIRED
@Metadata.allowExtensions: true

@EndUserText.label: '###GENERATED Core Data Service Entity'
define view entity ZR_DVSO_I
  as select from zdv_so_i as Items
  
  association [0..1] to ZI_Supplement_StdVH as _Product 
    on $projection.ProductID  = _Product.SupplementID

  association to parent ZR_DVSO_H as _Header 
    on $projection.SoUUID = _Header.SoUUID
{
  key item_uuid             as ItemUUID,
      so_uuid               as SoUUID,
      item_no               as ItemNo,     
      product_id            as ProductID,
      quantity              as Quantity,
      @Consumption.valueHelpDefinition: [ { entity.name: 'I_UnitOfMeasureStdVH',  entity.element: 'UnitOfMeasure',
                                            useForValidation: true } ]
      quantity_unit         as QuantityUnit,
      @Consumption.valueHelpDefinition: [ { entity.name: 'I_CurrencyStdVH',  entity.element: 'Currency',
                                            useForValidation: true } ]
      currency_code         as CurrencyCode,
      @Semantics.amount.currencyCode: 'CurrencyCode'
      net_amount            as NetAmount,
      note                  as Note,
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
      _Product,
      _Header
}
```

The service exposes projections:
- **Header projection:** `ZC_DVSO_H`

```
@Metadata.allowExtensions: true
@Metadata.ignorePropagatedAnnotations: true
@EndUserText: { label: '###GENERATED Core Data Service Entity' }

@AccessControl.authorizationCheck: #NOT_REQUIRED

@Search.searchable: true
define root view entity ZC_DVSO_H
  provider contract transactional_query
  as projection on ZR_DVSO_H

  association [1..1] to ZR_DVSO_H as _BaseEntity on $projection.SoUUID = _BaseEntity.SoUUID

{
  key SoUUID,
      @Search.defaultSearchElement: true
      @Search.fuzzinessThreshold: 0.87
      SalesOrder,
      @ObjectModel.text.element: ['SoldToPartyName']
      @Consumption.valueHelpDefinition: [{ entity: { name: 'ZI_Customer', element: 'CustomerID' }, useForValidation: true }]
      SoldToParty,
      @Semantics.text: true
      _Customer.LastName          as SoldToPartyName,
      DocumentDate,
      @Consumption: { valueHelpDefinition: [ { entity.element: 'Currency', entity.name: 'I_CurrencyStdVH', useForValidation: true } ] }
      CurrencyCode,
      @Semantics: { amount.currencyCode: 'CurrencyCode' }
      NetAmount,
      @Semantics: { amount.currencyCode: 'CurrencyCode' }
      TaxAmount,
      @Semantics: { amount.currencyCode: 'CurrencyCode' }
      GrossAmount,
      OverallStatus,
      @Semantics: { user.createdBy: true }
      LocalCreatedBy,
      @Semantics: { systemDateTime.createdAt: true }
      LocalCreatedAt,
      @Semantics: { user.localInstanceLastChangedBy: true }
      LocalLastChangedBy,
      @Semantics: { systemDateTime.localInstanceLastChangedAt: true }
      LocalLastChangedAt,
      @Semantics: { systemDateTime.lastChangedAt: true }
      LastChangedAt,

      /* Asociaciones */
      _BaseEntity,
      _Customer,
      _Items : redirected to composition child ZC_DVSO_I
}
```

- **Item projection:** `ZC_DVSO_I`

```
@Metadata.allowExtensions: true
@Metadata.ignorePropagatedAnnotations: true
@EndUserText.label: '###GENERATED Core Data Service Entity'

@AccessControl.authorizationCheck: #NOT_REQUIRED
define view entity ZC_DVSO_I
  as projection on ZR_DVSO_I
  association [1..1] to ZR_DVSO_I as _BaseEntity on $projection.ItemUUID = _BaseEntity.ItemUUID
{
  key ItemUUID,
      SoUUID,
      ItemNo,
      @ObjectModel.text.element: [ 'ProductName' ]
      @Consumption.valueHelpDefinition: [ { entity: { name: 'ZI_Supplement_StdVH', element: 'SupplementID' }, useForValidation: true } ]
      ProductID,
//      @Semantics.text: true
      _Product.SupplementText as ProductName,
      @Semantics.quantity.unitOfMeasure: 'QuantityUnit'
      Quantity,
      @Consumption.valueHelpDefinition: [ { entity: { name: 'I_UnitOfMeasureStdVH', element: 'UnitOfMeasure' }, useForValidation: true } ]
      QuantityUnit,
      @Consumption.valueHelpDefinition: [ { entity: { name: 'I_CurrencyStdVH', element: 'Currency' }, useForValidation: true } ]
      CurrencyCode,
      @Semantics.amount.currencyCode: 'CurrencyCode'
      NetAmount,
      Note,
      @Semantics: { user.createdBy: true } LocalCreatedBy,
      @Semantics: { systemDateTime.createdAt: true } LocalCreatedAt,
      @Semantics: { user.localInstanceLastChangedBy: true } LocalLastChangedBy,
      @Semantics: { systemDateTime.localInstanceLastChangedAt: true } LocalLastChangedAt,
      @Semantics: { systemDateTime.lastChangedAt: true } LastChangedAt,

      /* Asociaciones */
      _BaseEntity,   
//      _Product,
      _Header : redirected to parent ZC_DVSO_H
}
```

Redirected composition is visible in the header projection:
- `_Items redirected to composition child ZC_DVSO_I`: `ZC_DVSO_H`

```
```
