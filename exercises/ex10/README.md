# HANDS-ON EXERCISE 10

## Introduction
In this hands-on exercise, you will handle a Actions.

### Information: Actions
> In the RAP context, an action is a non-standard operation that change the data of a BO instance. 
> 
> Actions are specified in behavior definitions and implemented in ABAP behavior pools. 
> By default, actions are related to instances of a BO entity. The addition `static` allows you to define a static actions that are not bound to any instance but relates to the complete entity.
> 
> Two main categories of actions can be implemented in RAP:  
> - **Non-factory actions**: Defines a RAP action which offers non-standard behavior. The custom logic must be implemented in the RAP handler method `FOR MODIFY`. An action per default relates to a RAP BO entity instance and changes the state of the instance.  An action is related to an instance by default. Non-factory actions can be instance-bound (default) or static.
> - **Factory actions**: Factory actions are used to create RAP BO entity instances. Factory actions can be instance-bound (default) or static. Instance-bound factory actions can copy specific values of an instance. Static factory actions can be used to create instances with prefilled default values.
>
> ℹ **Further reading**: [Actions](https://help.sap.com/viewer/923180ddb98240829d935862025004d6/Cloud/en-US/83bad707a5a241a2ae93953d81d17a6b.html) **|** [CDS BDL - non-standard operations](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abenbdl_nonstandard.htm) **|** [ABAP EML - response_param](https://help.sap.com/doc/abapdocu_cp_index_htm/CLOUD/en-US/index.htm?file=abapeml_response.htm)   
> ℹ **Further reading**: [RAP BO Contract](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/3a402c5cf6a74bc1a1de080b2a7c6978.html) **|** [RAP BO Provider API (derived types, %cid, implicit response parameters,...)](https://help.sap.com/docs/BTP/923180ddb98240829d935862025004d6/2a3da8a5b19e4f6b953e9a11fb5cc747.html?version=Cloud) 

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
