# fiori_rap
RAP Fiori elements app V4

# ABAP RAP Sales Order App (Managed Draft, Actions, Validations, Determinations, KPI Criticality + Donut)

App built with **SAP ABAP RAP (Managed with Draft)** and exposed via **OData V4** for **Fiori elements**.
It manages a **Sales Order** business object with **Header (root)** and **Items (composition child)**, including:

- Managed draft lifecycle ✍️
- Validations on save ✅❌
- Determinations (auto-fill & consistency) 🧠
- Instance actions (Approve / Reject / CalculateAmounts) ⚡
- Budget-consumption KPI (traffic-light criticality) + donut chart 🍩🚦

---

## What this app covers

### 1) Parent–Child Model (Header → Items) with Composition

Go to [`EXERCISE`](exercises/ex03/README.md)

The BO is modeled around:
- **Root entity (Header):** [`ZR_DVSO_H`](source/ZR_DVSO_H-ddls.txt)
- **Child entity (Items):** [`ZR_DVSO_I`](source/ZR_DVSO_I-ddls.txt)

The service exposes projections:
- **Header projection:** [`ZC_DVSO_H`](source/ZC_DVSO_H-ddls.txt)
- **Item projection:** [`ZC_DVSO_I`](source/ZC_DVSO_H-ddls.txt)

Redirected composition is visible in the header projection:
- `_Items redirected to composition child ZC_DVSO_I`: [`ZC_DVSO_H`](source/ZC_DVSO_H-ddls.txt#L65)

---

### 2) Managed Draft Transactional Behavior (Create/Update/Delete + Draft lifecycle)

Go to [`EXERCISE`](exercises/ex04/README.md)

This app uses **managed RAP with draft**, so users can:
- Create/change data in draft
- Validate on save (draft prepare)
- Activate/discard/resume drafts
- Get consistent locking + ETags

Root behavior definition (draft + etag/locks + validations + actions + mappings):
- [`ZR_DVSO_H.bdef`](source/ZR_DVSO_H-bdef.txt#L1-L142)

Highlights:
- Draft + strict mode: [`managed implementation / strict / with draft`](source/ZR_DVSO_H-bdef.txt##L1-L12)
- ETag + locks: [`etag master` + `lock master`](source/ZR_DVSO_H-bdef.txt##L10-L11)
- Composition to items (draft-enabled): [`association _Items { create; with draft; }`](source/ZR_DVSO_H-bdef.txt##L36-L37)
- Draft actions: [`Activate/Discard/Edit/Resume`](source/ZR_DVSO_H-bdef.txt##L55-L57)
- Draft Prepare orchestrating validations: [`Prepare { validation Item~validateItemsSum; etc }`](source/ZR_DVSO_H-bdef.txt##L58-L64)

Projection behavior (exposes actions to the UI):
- [`ZC_DVSO_H.bdef`](source/ZC_DVSO_H-bdef.txt#L1-L51)

> [!IMPORTANT]
> In Fiori elements, an action can exist in the behavior but behave incorrectly (or not appear) unless it is properly exposed in the **projection behavior** using `use action ...`.

---

### 3) Authorizations (DCL)

Go to [`EXERCISE`](exercises/ex05/README.md)

Access control artifacts included:
- Root DCL: [`ZR_DVSO_H.dcls`](source/ZR_DVSO_H-dcls.txt#L1-L6)
- Projection DCL: [`ZC_DVSO_H.dcls`](source/ZC_DVSO_H-dcls.txt#L1-L6)

> The root entity uses `authorization master (global)` and the handler provides a demo-friendly `GET_GLOBAL_AUTHORIZATIONS`.

---

### 4) Value Helps (F4) + Text Associations

Go to [`EXERCISE`](exercises/ex06/README.md)

Value helps are wired in the projection views using `@Consumption.valueHelpDefinition` and text associations (`@ObjectModel.text.element`):

- Header value helps + text: [`ZC_DVSO_H`](source/ZC_DVSO_H-ddls.txt#L20-L30)
- Item value helps (product/unit/etc.): [`ZC_DVSO_I`](source/ZC_DVSO_I-ddls.txt#L14-L19)

---

### 5) KPI: Budget Consumption (Items Sum vs Header NetAmount) + Criticality 🚦

Go to [`EXERCISE`](exercises/ex07/README.md)

The Object Page displays:
- `ItemsTotalNetAmount` (SUM of item net amounts)
- `RemainingAmount` (header net amount minus items sum)
- `ConsumptionCriticality` (0 neutral, 1 red, 2 amber, 3 green)

CDS implementation:
- Items SUM view: [`ZI_DVSO_SUM`](source/ZI_DVSO_SUM-ddls.txt#L1-L18)
- KPI view (header + join to SUM): [`ZI_DVSO_KPI`](source/ZI_DVSO_KPI-ddls.txt#L1-L44)

- Header UI annotations: [`ZC_DVSO_H.ddlx`](source/ZC_DVSO_H-ddlx.txt#L13-L14)
- DataPont UI annotation: [`DataPoint`](source/ZC_DVSO_H-ddlx.txt#L63-L80)

> [!WARNING]
> In managed draft, a CDS KPI can look misleading if it reads only active data while the user is editing draft records.
> If activation fails due to validation errors, active data does not change, so the KPI may remain “green” even when the draft is already over budget.

---

### 5.1) Donut Chart 🍩 (Fiori elements)
Chart and UI metadata are defined through annotations (no custom UI coding):

- Item UI annotations: [`ZC_DVSO_I.ddlx`](source/ZC_DVSO_I-ddlx.txt#L1-L14)

---

### 6) Validations on Save (Business Rules) ✅❌

Go to [`EXERCISE`](exercises/ex08/README.md)

Header validations (root behavior):
- `validateCustomer` (SoldToParty mandatory)
- `validateAmounts` (NetAmount > 0, TaxAmount >= 0)
- `validateDocumentDate` (mandatory and not in the future)
- `validateCurrency` (CurrencyCode mandatory)

Defined here:
- [`ZR_DVSO_H validations`](source/ZR_DVSO_H-bdef.txt##L39-L48)

Item validation:
- `validateItemsSum on save { create; update; field NetAmount; }`
- Defined here: `Item~validateItemsSum`](source/ZR_DVSO_H-bdef.txt##L122-L123)
- Orchestrated via draft prepare: [`Prepare triggers Item~validateItemsSum`](source/ZR_DVSO_H-bdef.txt##L59)

Implementation (draft-aware reads in LOCAL MODE + associations):
- [`validateItemsSum implementation`](source/ZBP_R_DVSO_H-clas.txt#L120-L220)

---

### 7) Determinations (Auto-fill + consistency) 🧠

Go to [`EXERCISE`](exercises/ex09/README.md)

#### 7.1 SalesOrder generation (Header)
- Determination: `createDocument on save { create; update; field SalesOrder; }`
- Defined here: [`createDocument`](source/ZR_DVSO_H-bdef.txt##L51)
- Implemented here: [`createDocument implementation`](source/ZBP_R_DVSO_H-clas.txt#L288-L331)

#### 7.2 Default initialization (Header)
- Determination: `setInitialValues on modify { create; }`
- Defined here: [`setInitialValues`](source/ZR_DVSO_H-bdef.txt##L52)
- Implemented here: [`setInitialValues implementation`](source/ZBP_R_DVSO_H-clas.txt#L582-L604)

#### 7.3 Item numbering (Items)
Assigns incremental `ItemNo` (000010, 000020, …):
- Determination: `createItem on modify { create; field ItemNo; }`
- Defined here: [`Item~createItem`](source/ZR_DVSO_H-bdef.txt##L119)
- Implemented here: [`createItem implementation`](source/ZBP_R_DVSO_H-clas.txt#L16-L115)

#### 7.4 Item currency inheritance (Items) 💶
New items inherit `CurrencyCode` from the header (draft-aware, local mode):
- Determination: `getCurrency on modify { create; }`
- Defined here: [`Item~getCurrency`](source/ZR_DVSO_H-bdef.txt##L120)
- Implemented here: [`getCurrency implementation`](source/ZBP_R_DVSO_H-clas.txt#L182-L238)

---

### 9) Instance Actions (Approve / Reject / CalculateAmounts) ⚡

Go to [`EXERCISE`](exercises/ex10/README.md)

Actions are defined on Header:
- **Approve** → sets `OverallStatus = 'A'`
- **Reject** → sets `OverallStatus = 'R'`
- **CalculateAmounts** → sets `GrossAmount = NetAmount + TaxAmount`

Defined here:
- [`Header actions`](source/ZR_DVSO_H-bdef.txt##L39-L42)

Exposed in projection behavior (required for proper UI behavior):
- [`use action Approve/Reject/CalculateAmounts`](source/ZC_DVSO_H-bdef.txt#L16-L19)

Implemented here:
- [`Approve`](source/ZBP_R_DVSO_H-clas.txt#L334-L374)
- [`Reject`](source/ZBP_R_DVSO_H-clas.txt#L419-L460)
- [`CalculateAmounts`](source/ZBP_R_DVSO_H-clas.txt#L376-L417)

> [!IMPORTANT]
> For actions to appear as buttons in Fiori elements, you must also place them using UI annotations
> (e.g. `@UI.identification` / `@UI.lineItem` with `type: #FOR_ACTION`), typically in:
> - [`ZC_DVSO_H.ddlx`](source/ZC_DVSO_H-ddlx.txt#L76-L79)

---

## OData V4 Exposure (Service Definition + Binding)

Go to [`EXERCISE`](exercises/ex11/README.md)

Service definition:
- [`ZUI_DVSO_H_O4.srvd`](source/ZUI_DVSO_H_O4-srvd.txt#L1-L13)

Service binding (artifact):
- `ZUI_DVSO_H_O4.srvb`

---

`abap` · `rap` · `abap-cloud` · `fiori-elements` · `odata-v4` · `cds` · `managed-draft`

---

