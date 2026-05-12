# HANDS-ON EXERCISE 10

## Introduction
In this hands-on exercise, you will handle a Actions.

### Instance Actions (Approve / Reject / CalculateAmounts) ⚡
Actions are defined on Header:
- **Approve** → sets `OverallStatus = 'A'`
- **Reject** → sets `OverallStatus = 'R'`
- **CalculateAmounts** → sets `GrossAmount = NetAmount + TaxAmount`

Defined here:
- [`Header actions`](source/zr_dvso_h.bdef.asbdef#L39-L42)

Exposed in projection behavior (required for proper UI behavior):
- [`use action Approve/Reject/CalculateAmounts`](source/zc_dvso_h.bdef.asbdef#L16-L19)

Implemented here:
- [`Approve`](source/zbp_r_dvso_h.clas.locals_imp.abap#L334-L374)
- [`Reject`](source/zbp_r_dvso_h.clas.locals_imp.abap#L419-L460)
- [`CalculateAmounts`](source/zbp_r_dvso_h.clas.locals_imp.abap#L376-L417)

> [!IMPORTANT]
> For actions to appear as buttons in Fiori elements, you must also place them using UI annotations
> (e.g. `@UI.identification` / `@UI.lineItem` with `type: #FOR_ACTION`), typically in:
> - [`ZC_DVSO_H.ddlx`](source/zc_dvso_h.ddlx.asddlxs#L76-L79)
