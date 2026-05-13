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
- KPI view (header + join to SUM): [`ZI_DVSO_KPI`](../../source/ZI_DVSO_KPI-ddls.txt#L1-L44)

- Header UI annotations: [`ZC_DVSO_H.ddlx`](../../source/ZC_DVSO_H-ddlx.txt#L13-L14)
- DataPont UI annotation: [`DataPoint`](../../source/ZC_DVSO_H-ddlx.txt#L63-L80)

> [!WARNING]
> In managed draft, a CDS KPI can look misleading if it reads only active data while the user is editing draft records.
> If activation fails due to validation errors, active data does not change, so the KPI may remain “green” even when the draft is already over budget.


### Donut Chart 🍩 (Fiori elements)
Chart and UI metadata are defined through annotations (no custom UI coding):

- Item UI annotations: [`ZC_DVSO_I.ddlx`](../../source/ZC_DVSO_I.ddlx.txt#L1-L14)
