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
- [`ZR_DVSO_H.bdef`](source/zr_dvso_h.bdef.asbdef#L1-L144)

Highlights:
- Draft + strict mode: [`managed implementation / strict / with draft`](source/zr_dvso_h.bdef.asbdef#L1-L12)
- ETag + locks: [`etag master` + `lock master`](source/zr_dvso_h.bdef.asbdef#L10-L11)
- Composition to items (draft-enabled): [`association _Items { create; with draft; }`](source/zr_dvso_h.bdef.asbdef#L36-L37)
- Draft actions: [`Activate/Discard/Edit/Resume`](source/zr_dvso_h.bdef.asbdef#L55-L57)
- Draft Prepare orchestrating validations: [`Prepare { validation Item~validateItemsSum; etc }`](source/zr_dvso_h.bdef.asbdef#L58-L64)

Projection behavior (exposes actions to the UI):
- [`ZC_DVSO_H.bdef`](source/zc_dvso_h.bdef.asbdef#L1-L51)

> [!IMPORTANT]
> In Fiori elements, an action can exist in the behavior but behave incorrectly (or not appear) unless it is properly exposed in the **projection behavior** using `use action ...`.
