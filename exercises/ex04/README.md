# HANDS-ON EXERCISE 4

## Introduction
In this hands-on exercise, you will create a Behavior definition.

### Managed Draft Transactional Behavior (Create/Update/Delete + Draft lifecycle)
This app uses **managed RAP with draft**, so users can:
- Create/change data in draft
- Validate on save (draft prepare)
- Activate/discard/resume drafts
- Get consistent locking + ETags

Root behavior definition (draft + etag/locks + validations + actions + mappings):
- [`ZR_DVSO_H.bdef`](../../source/ZR_DVSO_H-bdef.txt)

Highlights:
- Draft + strict mode: [`managed implementation / strict / with draft`](../../source/ZR_DVSO_H-bdef.txt##L1-L12)
```ABAP
managed implementation in class ZBP_R_DVSO_H unique; 
strict ( 2 ); 
with draft; 
extensible;

define behavior for ZR_DVSO_H alias Header
persistent table ZDV_SO_H
extensible
draft table ZDVSO_H_D
```
---

- ETag + locks: [`etag master` + `lock master`](../../source/ZR_DVSO_H-bdef.txt##L10-L11)
```ABAP
etag master LocalLastChangedAt 
lock master total etag LastChangedAt 
```
---
- Composition to items (draft-enabled): [`association _Items { create; with draft; }`](../../source/ZR_DVSO_H-bdef.txt##L36-L37)
```ABAP
association _Items {create; with draft;}
```
---
- Draft actions: [`Activate/Discard/Edit/Resume`](../../source/ZR_DVSO_H-bdef.txt##L55-L57)
```ABAP
draft action Activate optimized;
draft action Discard;
draft action Edit;
draft action Resume;
```
---
- Draft Prepare orchestrating validations: [`Prepare { validation Item~validateItemsSum; etc }`](../../source/ZR_DVSO_H-bdef.txt##L58-L64)
```ABAP
draft determine action Prepare {
  validation Item~validateItemsSum;
  validation validateCustomer;
  validation validateAmounts;
  validation validateDocumentDate;
  validation validateCurrency;
}
```
---

Projection behavior (exposes actions to the UI):
- [`ZC_DVSO_H.bdef`](../../source/ZC_DVSO_H-bdef.txt##L1-L50)
```ABAP
projection implementation in class ZBP_C_DVSO_H unique;
strict ( 2 );
extensible;
use draft;
use side effects;

define behavior for ZC_DVSO_H alias Header
extensible
use etag

{
  use create;
  use update;
  use delete;

  // Acciones Custom
  use action Approve;
  use action Reject;
  use action CalculateAmounts;

  // Exponer la composición en la proyección
  use association _Items { create; with draft; }

  use action Edit;
  use action Activate;
  use action Discard;
  use action Resume;
  use action Prepare;

    // Instance features
  side effects {
    field NetAmount affects field GrossAmount;
    field TaxAmount affects field GrossAmount;
  }

}

define behavior for ZC_DVSO_I alias Item
{
  use update;
  use delete;

  use association _Header {with draft;}

  // Refrescar el header cuando se cambian los items
  side effects {
    field NetAmount affects entity _Header;
    $self affects entity _Header;
  }

}
```


> [!IMPORTANT]
> In Fiori elements, an action can exist in the behavior but behave incorrectly (or not appear) unless it is properly exposed in the **projection behavior** using `use action ...`.
