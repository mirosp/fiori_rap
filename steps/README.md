Step-by-Step: Create a RAP Service in SAP
<img width="1042" height="696" alt="image" src="https://github.com/user-attachments/assets/49fd5a00-a964-428a-96da-def69812e49c" />

1. Create Database Table
Use ABAP Dictionary (SE11 or Eclipse ADT)
Define fields, keys, and data types

👉 Example: ADT

2. Create CDS Data Model (Root View Entity)
This represents your business object

3. Create Behavior Definition (BDEF)
Defines CRUD operations
managed implementation in class zbp_i_employee unique;

4. Create Behavior Implementation Class
Class gets generated automatically
Add logic if needed (validations, determinations)

5. Create Projection View
Used to expose data externally

6. Create Metadata (UI Annotations) ← NEW STEP
👉 Best practice: use Metadata Extension, not inline annotations.

Option A: Metadata Extension (Recommended)
@Metadata.layer: #CORE
annotate view Z_C_Employee with {

  @UI.headerInfo: {
    typeName: 'Employee',
    typeNamePlural: 'Employees'
  }
  @UI.lineItem: [{ position: 10 }]
  id;
  @UI.lineItem: [{ position: 20 }]
  name;
  @UI.lineItem: [{ position: 30 }]
  dept;
}
Option B: Inline Metadata (Simpler, not ideal for large apps)
define root view entity Z_C_Employee
  as projection on Z_I_Employee
{
  @UI.lineItem: [{ position: 10 }]
  key id,
  @UI.lineItem: [{ position: 20 }]
  name,
  @UI.lineItem: [{ position: 30 }]
  dept
}

7. Create Projection Behavior Definition

8. Create Service Definition
Expose your projection view

@EndUserText.label: 'Employee Service'
define service ZUI_EMPLOYEE {
  expose Z_C_Employee;
}

9. Create Service Binding
In ADT (Eclipse):
Right-click → New → Service Binding
Choose:
OData V4 (recommended)
or OData V2
Activate and Publish

10. Test the Service
Use:
Preview in ADT
Browser
SAP Gateway Client (/IWFND/V4_ADMIN)
Postman
