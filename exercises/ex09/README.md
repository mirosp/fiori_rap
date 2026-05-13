# HANDS-ON EXERCISE 9

## Introduction
In this hands-on exercise, you will handle a Determinations.

### Determinations (Auto-fill + consistency) 🧠
#### 1 SalesOrder generation (Header)
- Determination: `createDocument on save { create; update; field SalesOrder; }`
- Defined here: [`createDocument`](../../source/ZR_DVSO_H-bdef.txt#L51)
- Implemented here: [`createDocument implementation`](../../source/ZBP_R_DVSO_H-clas.txt#L288-L331)

#### 2 Default initialization (Header)
- Determination: `setInitialValues on modify { create; }`
- Defined here: [`setInitialValues`](../../source/ZR_DVSO_H.bdef.txt#L52)
- Implemented here: [`setInitialValues implementation`](../../source/ZBP_R_DVSO_H-clas.txt#L582-L604)

#### 3 Item numbering (Items)
Assigns incremental `ItemNo` (000010, 000020, …):
- Determination: `createItem on modify { create; field ItemNo; }`
- Defined here: [`Item~createItem`](../../source/ZR_DVSO_H.bdef.asbdef#L119)
- Implemented here: [`createItem implementation`](../../source/ZBP_R_DVSO_H-clas.txt#L16-L115)

#### 4 Item currency inheritance (Items) 💶
New items inherit `CurrencyCode` from the header (draft-aware, local mode):
- Determination: `getCurrency on modify { create; }`
- Defined here: [`Item~getCurrency`](../../source/ZR_DVSO_H.bdef.asbdef#L120)
- Implemented here: [`getCurrency implementation`](../../source/ZBP_R_DVSO_H-clas.txt#L182-L238)
