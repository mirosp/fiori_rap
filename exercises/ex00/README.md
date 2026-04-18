# Exercise 0: Getting Started

## Introduction

ADT in eclipse
https://tools.hana.ondemand.com/#abap

# Eclipse ABAP Development Tools Installation Guide

## Steps

1. Get an installation of **Eclipse 2026-03** (e.g., *Eclipse IDE for Java Developers*).
2. Open Eclipse and choose in the menu bar:
```
Help > Install New Software...
```
3. Enter the URL:
```
https://tools.hana.ondemand.com/latest
```
4. Press **Enter** to display the available features.
5. Select **ABAP Development Tools** and choose **Next**.
6. On the next wizard page, review the overview of features to be installed, then choose **Next**.
7. Confirm the license agreements and choose **Finish** to start the installation.

## Tutorial ADT
You can also check out this interactive tutorial: [Install ABAP Development Tools (ADT)](https://developers.sap.com/tutorials/abap-install-adt.html).


# BAS Business application studio

BAS in BTP cockpit
https://account.hana.ondemand.com/#/home/welcome

# Accessing SAP Business Application Studio in SAP BTP Cockpit

## Steps

1. Open the SAP BTP Cockpit: https://cockpit.btp.cloud.sap
2. Log in with your SAP account.
3. In the global account view, select your **Subaccount**.
4. In the left-hand navigation, go to: (If the BAS is already enabled, skip to point 8)
```
Services > Service Marketplace
```
5. In the search bar, type:
```
Business Application Studio
```
6. Click on **SAP Business Application Studio** from the results.
7. Click **Enable** (if not already enabled).
8. Once enabled, navigate to:
```
Services > Instances and Subscriptions
```
9. Find **SAP Business Application Studio** in the list.
10. Click **Go to Application** to launch BAS.

### Notes

* If you don’t see BAS, check your entitlements in:
  ```
  Entitlements > Service Assignments
  ```
* You may need admin permissions to enable the service.


# Creating a Dev Space in SAP Business Application Studio

## Steps

1. Open **SAP Business Application Studio** from the BTP cockpit.
2. In the BAS home screen, click on:
```
Create Dev Space
```
3. Enter a **name** for your dev space (e.g., `dev-space-1`).
4. Choose an **Application Type** based on your use case:
- SAP Fiori
- Full Stack Cloud Application
- SAP HANA Native Application
- Basic Dev Space

5. Select the required **additional extensions** (optional, depending on your project).
6. Click **Create Dev Space**.
7. Wait until the dev space status changes to:
```
RUNNING
```
8. Click on the dev space name to open it.

## Notes
- Initial startup may take a few minutes.
- You can stop unused dev spaces to save resources.
- Make sure your assigned quotas allow dev space creation.



