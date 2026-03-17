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
The BO is modeled around:
- **Root entity (Header):** [`ZR_DVSO_H`](source/ZR_DVSO_H-ddls.txt)
- **Child entity (Items):** [`ZR_DVSO_I`](source/ZR_DVSO_I-ddls.txt)

The service exposes projections:
- **Header projection:** [`ZC_DVSO_H`](source/ZC_DVSO_H-ddls.txt)
- **Item projection:** [`ZC_DVSO_I`](source/ZC_DVSO_H-ddls.txt)

Redirected composition is visible in the header projection:
- `_Items redirected to composition child ZC_DVSO_I`: [`ZC_DVSO_H`](source/ZC_DVSO_H-ddls.txt)

---

### 2) Managed Draft Transactional Behavior (Create/Update/Delete + Draft lifecycle)
This app uses **managed RAP with draft**, so users can:
- Create/change data in draft
- Validate on save (draft prepare)
- Activate/discard/resume drafts
- Get consistent locking + ETags

Root behavior definition (draft + etag/locks + validations + actions + mappings):
- [`ZR_DVSO_H.bdef`](src/zr_dvso_h.bdef.asbdef#L1-L144)

Highlights:
- Draft + strict mode: [`managed implementation / strict / with draft`](src/zr_dvso_h.bdef.asbdef#L1-L12)
- ETag + locks: [`etag master` + `lock master`](src/zr_dvso_h.bdef.asbdef#L10-L11)
- Composition to items (draft-enabled): [`association _Items { create; with draft; }`](src/zr_dvso_h.bdef.asbdef#L36-L37)
- Draft actions: [`Activate/Discard/Edit/Resume`](src/zr_dvso_h.bdef.asbdef#L55-L57)
- Draft Prepare orchestrating validations: [`Prepare { validation Item~validateItemsSum; etc }`](src/zr_dvso_h.bdef.asbdef#L58-L64)

Projection behavior (exposes actions to the UI):
- [`ZC_DVSO_H.bdef`](src/zc_dvso_h.bdef.asbdef#L1-L51)

> [!IMPORTANT]
> In Fiori elements, an action can exist in the behavior but behave incorrectly (or not appear) unless it is properly exposed in the **projection behavior** using `use action ...`.

---

### 3) Authorizations (DCL)
Access control artifacts included:
- Root DCL: [`ZR_DVSO_H.dcls`](src/zr_dvso_h.dcls.asdcls#L1-L6)
- Projection DCL: [`ZC_DVSO_H.dcls`](src/zc_dvso_h.dcls.asdcls#L1-L6)

> The root entity uses `authorization master (global)` and the handler provides a demo-friendly `GET_GLOBAL_AUTHORIZATIONS`.

---

### 4) Value Helps (F4) + Text Associations
Value helps are wired in the projection views using `@Consumption.valueHelpDefinition` and text associations (`@ObjectModel.text.element`):

- Header value helps + text: [`ZC_DVSO_H`](src/zc_dvso_h.ddls.asddls#L17-L27)
- Item value helps (product/unit/etc.): [`ZC_DVSO_I`](src/zc_dvso_i.ddls.asddls#L14-L19)

---

### 5) KPI: Budget Consumption (Items Sum vs Header NetAmount) + Criticality 🚦
The Object Page displays:
- `ItemsTotalNetAmount` (SUM of item net amounts)
- `RemainingAmount` (header net amount minus items sum)
- `ConsumptionCriticality` (0 neutral, 1 red, 2 amber, 3 green)

CDS implementation:
- Items SUM view: [`ZI_DVSO_SUM`](src/zi_dvso_sum.ddls.asddls#L1-L18)
- KPI view (header + join to SUM): [`ZI_DVSO_KPI`](src/zi_dvso_kpi.ddls.asddls#L1-L44)

- Header UI annotations: [`ZC_DVSO_H.ddlx`](src/zc_dvso_h.ddlx.asddlxs#L13-L14)
- DataPont UI annotation: [`DataPoint`](src/zc_dvso_h.ddlx.asddlxs#L63-L80)

> [!WARNING]
> In managed draft, a CDS KPI can look misleading if it reads only active data while the user is editing draft records.
> If activation fails due to validation errors, active data does not change, so the KPI may remain “green” even when the draft is already over budget.

---

### 6) Donut Chart 🍩 (Fiori elements)
Chart and UI metadata are defined through annotations (no custom UI coding):

- Item UI annotations: [`ZC_DVSO_I.ddlx`](src/zc_dvso_i.ddlx.asddlxs#L1-L14)

---

### 7) Validations on Save (Business Rules) ✅❌
Header validations (root behavior):
- `validateCustomer` (SoldToParty mandatory)
- `validateAmounts` (NetAmount > 0, TaxAmount >= 0)
- `validateDocumentDate` (mandatory and not in the future)
- `validateCurrency` (CurrencyCode mandatory)

Defined here:
- [`ZR_DVSO_H validations`](src/zr_dvso_h.bdef.asbdef#L39-L48)

Item validation:
- `validateItemsSum on save { create; update; field NetAmount; }`
- Defined here: `Item~validateItemsSum`](src/zr_dvso_h.bdef.asbdef#L122-L123)
- Orchestrated via draft prepare: [`Prepare triggers Item~validateItemsSum`](src/zr_dvso_h.bdef.asbdef#L59)

Implementation (draft-aware reads in LOCAL MODE + associations):
- [`validateItemsSum implementation`](src/zbp_r_dvso_h.clas.locals_imp.abap#L120-L220)

---

### 8) Determinations (Auto-fill + consistency) 🧠
#### 8.1 SalesOrder generation (Header)
- Determination: `createDocument on save { create; update; field SalesOrder; }`
- Defined here: [`createDocument`](src/zr_dvso_h.bdef.asbdef#L51)
- Implemented here: [`createDocument implementation`](src/zbp_r_dvso_h.clas.locals_imp.abap#L288-L331)

#### 8.2 Default initialization (Header)
- Determination: `setInitialValues on modify { create; }`
- Defined here: [`setInitialValues`](src/zr_dvso_h.bdef.asbdef#L52)
- Implemented here: [`setInitialValues implementation`](src/zbp_r_dvso_h.clas.locals_imp.abap#L582-L604)

#### 8.3 Item numbering (Items)
Assigns incremental `ItemNo` (000010, 000020, …):
- Determination: `createItem on modify { create; field ItemNo; }`
- Defined here: [`Item~createItem`](src/zr_dvso_h.bdef.asbdef#L119)
- Implemented here: [`createItem implementation`](src/zbp_r_dvso_h.clas.locals_imp.abap#L16-L115)

#### 8.4 Item currency inheritance (Items) 💶
New items inherit `CurrencyCode` from the header (draft-aware, local mode):
- Determination: `getCurrency on modify { create; }`
- Defined here: [`Item~getCurrency`](src/zr_dvso_h.bdef.asbdef#L120)
- Implemented here: [`getCurrency implementation`](src/zbp_r_dvso_h.clas.locals_imp.abap#L182-L238)

---

### 9) Instance Actions (Approve / Reject / CalculateAmounts) ⚡
Actions are defined on Header:
- **Approve** → sets `OverallStatus = 'A'`
- **Reject** → sets `OverallStatus = 'R'`
- **CalculateAmounts** → sets `GrossAmount = NetAmount + TaxAmount`

Defined here:
- [`Header actions`](src/zr_dvso_h.bdef.asbdef#L39-L42)

Exposed in projection behavior (required for proper UI behavior):
- [`use action Approve/Reject/CalculateAmounts`](src/zc_dvso_h.bdef.asbdef#L16-L19)

Implemented here:
- [`Approve`](src/zbp_r_dvso_h.clas.locals_imp.abap#L334-L374)
- [`Reject`](src/zbp_r_dvso_h.clas.locals_imp.abap#L419-L460)
- [`CalculateAmounts`](src/zbp_r_dvso_h.clas.locals_imp.abap#L376-L417)

> [!IMPORTANT]
> For actions to appear as buttons in Fiori elements, you must also place them using UI annotations
> (e.g. `@UI.identification` / `@UI.lineItem` with `type: #FOR_ACTION`), typically in:
> - [`ZC_DVSO_H.ddlx`](src/zc_dvso_h.ddlx.asddlxs#L76-L79)

---

## OData V4 Exposure (Service Definition + Binding)
Service definition:
- [`ZUI_DVSO_H_O4.srvd`](src/zui_dvso_h_o4.srvd.srvdsrv#L1-L13)

Service binding (artifact):
- [`ZUI_DVSO_H_O4.srvb`](src/zui_dvso_h_o4.srvb.xml)

---

`abap` · `rap` · `abap-cloud` · `fiori-elements` · `odata-v4` · `cds` · `managed-draft`

---

