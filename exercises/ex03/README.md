
### 1) Parent–Child Model (Header → Items) with Composition
The BO is modeled around:
- **Root entity (Header):** [`ZR_DVSO_H`](source/ZR_DVSO_H-ddls.txt)
- **Child entity (Items):** [`ZR_DVSO_I`](source/ZR_DVSO_I-ddls.txt)

The service exposes projections:
- **Header projection:** [`ZC_DVSO_H`](source/ZC_DVSO_H-ddls.txt)
- **Item projection:** [`ZC_DVSO_I`](source/ZC_DVSO_H-ddls.txt)

Redirected composition is visible in the header projection:
- `_Items redirected to composition child ZC_DVSO_I`: [`ZC_DVSO_H`](source/ZC_DVSO_H-ddls.txt)
