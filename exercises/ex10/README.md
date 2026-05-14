# HANDS-ON EXERCISE 10

## Introduction
In this hands-on exercise, you will handle a Actions.

### Instance Actions (Approve / Reject / CalculateAmounts) ⚡
Actions are defined on Header:
- **Approve** → sets `OverallStatus = 'A'`
- **Reject** → sets `OverallStatus = 'R'`
- **CalculateAmounts** → sets `GrossAmount = NetAmount + TaxAmount`

Defined here:
- [`Header actions`](../../source/ZR_DVSO_H-bdef.txt#L39-L41)
```
  action Approve result [1] $self;
  action Reject result [1] $self;
  action CalculateAmounts result [1] $self;
```

Exposed in projection behavior (required for proper UI behavior):
- [`use action Approve/Reject/CalculateAmounts`](../../source/ZC_DVSO_H-bdef.txt#L16-L19)
```
  use action Approve;
  use action Reject;
  use action CalculateAmounts;
```

Implemented here:
- [`Approve`](../../source/ZBP_R_DVSO_H-clas.txt#L334-L374)
- [`Reject`](../../source/ZBP_R_DVSO_H-clas.txt#L419-L460)
- [`CalculateAmounts`](../../source/ZBP_R_DVSO_H-clas.txt#L376-L417)

> [!IMPORTANT]
> For actions to appear as buttons in Fiori elements, you must also place them using UI annotations
> (e.g. `@UI.identification` / `@UI.lineItem` with `type: #FOR_ACTION`), typically in:
> - [`ZC_DVSO_H.ddlx`](../../source/ZC_DVSO_H-ddlx.txt#L76-L79)
