# HANDS-ON EXERCISE 7

## Introduction
In this hands-on exercise, you will handle a KPI & Chart.

### KPI: Budget Consumption (Items Sum vs Header NetAmount) + Criticality 🚦
The Object Page displays:
- `ItemsTotalNetAmount` (SUM of item net amounts)
- `RemainingAmount` (header net amount minus items sum)
- `ConsumptionCriticality` (0 neutral, 1 red, 2 amber, 3 green)

CDS implementation:
- Items SUM view: [`ZI_DVSO_SUM`](../../source/ZI_DVSO_SUM-ddls.txt#L1-L18)
```ABAP
@EndUserText.label: 'Suma NetAmount Items por Sales Order'
@AccessControl.authorizationCheck: #NOT_REQUIRED

// Suma de items
define view entity ZI_DVSO_SUM      
    as select from ZR_DVSO_I
    
{

  key SoUUID,
  key CurrencyCode,

  @Semantics.amount.currencyCode: 'CurrencyCode'
  cast( sum( NetAmount ) as abap.curr( 23, 2 ) ) as ItemsTotalNetAmount
  
}

group by SoUUID, CurrencyCode
```
---
- KPI view (header + join to SUM): [`ZI_DVSO_KPI`](../../source/ZI_DVSO_KPI-ddls.txt#L1-L44)
```ABAP
@EndUserText.label: 'KPI Header'
@AccessControl.authorizationCheck: #NOT_REQUIRED

// Header + Join sum of items
define view entity ZI_DVSO_KPI

  as select from ZR_DVSO_H as H
  
    left outer join ZI_DVSO_SUM as S        // Suma total ( NetAmount ) --> Sales Order
      on  S.SoUUID       = H.SoUUID
      and S.CurrencyCode = H.CurrencyCode
      
{

  key H.SoUUID,
      H.CurrencyCode,

   // Total Net Items    
  @Semantics.amount.currencyCode: 'CurrencyCode'
  coalesce(
    S.ItemsTotalNetAmount,
    cast( 0 as abap.curr( 23, 2 ) )
  ) as ItemsTotalNetAmount,

  // Net Remaining to 100% (Header)
  @Semantics.amount.currencyCode: 'CurrencyCode'
  cast(
    H.NetAmount
    - coalesce( S.ItemsTotalNetAmount, cast( 0 as abap.curr( 23, 2 ) ) )
    as abap.curr( 23, 2 )
  ) as RemainingAmount,

// Criticality ---> 0 Neutral | 1 Critical |  2 Warning | 3 Good
  cast(     
    case
      when H.NetAmount = 0 then 0
      when coalesce( S.ItemsTotalNetAmount, cast( 0 as abap.curr( 23, 2 ) ) ) > H.NetAmount then 1
      when cast( coalesce( S.ItemsTotalNetAmount, cast( 0 as abap.curr( 23, 2 ) ) ) as abap.dec( 31, 2 ) ) * 10
         >= cast( H.NetAmount as abap.dec( 31, 2 ) ) * 8 then 2
      else 3
    end
    as abap.int1
  ) as ConsumptionCriticality
}
```
---
- Header UI annotations: [`ZC_DVSO_H.ddlx`](../../source/ZC_DVSO_H-ddlx.txt#L13-L14)
```ABAP
  @UI.facet: [
    { id: 'KPI_CONSUMO', purpose: #HEADER, type: #DATAPOINT_REFERENCE, label: 'Consumo', position: 5, targetQualifier: 'KPI_CONSUMO' }]
```
---
- DataPont UI annotation: [`DataPoint`](../../source/ZC_DVSO_H-ddlx.txt#L63-L71)
```ABAP
  @UI.dataPoint: {
      qualifier: 'KPI_CONSUMO',
      title: 'Importe de ítems / Total del pedido',
      description: 'Suma de items vs total',
      visualization: #PROGRESS,
      targetValueElement: 'NetAmount',
      criticality: 'ConsumptionCriticality'
  }
  itemstotalnetamount;
```
---

> [!WARNING]
> In managed draft, a CDS KPI can look misleading if it reads only active data while the user is editing draft records.
> If activation fails due to validation errors, active data does not change, so the KPI may remain “green” even when the draft is already over budget.


### Donut Chart 🍩 (Fiori elements)
Chart and UI metadata are defined through annotations (no custom UI coding):

- Item UI annotations: [`ZC_DVSO_I.ddlx`](../../source/ZC_DVSO_I-ddlx.txt#L1-L14)
```
todo
```
---
