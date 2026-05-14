# HANDS-ON EXERCISE 5

## Introduction
In this hands-on exercise, you will create a Access control.

### Authorizations (DCL)
Access control artifacts included:
- Root DCL: `ZR_DVSO_H.dcls`
```ABAP
@EndUserText.label: 'ZC_DVSO_H'
@MappingRole: true
define role ZC_DVSO_H {
  grant select on ZC_DVSO_H
  where INHERITING CONDITIONS FROM ENTITY ZR_DVSO_H REPLACING { ROOT WITH _BaseEntity };
}
```
---
- Projection DCL: `ZC_DVSO_H.dcls`
```
todo
```
---

> The root entity uses `authorization master (global)` and the handler provides a demo-friendly `GET_GLOBAL_AUTHORIZATIONS`.
