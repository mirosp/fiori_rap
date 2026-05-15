# HANDS-ON EXERCISE 12

## Introduction
In this hands-on exercise, you will create a Fiori Elements app in BAS.

### Generate Fiori Elements App (Optional)

Use:
SAP Business Application Studio
Visual Studio Code

1. Open SAP Business Application Studio

Launch your BAS dev space.

Recommended dev space type:
SAP Fiori

If you don’t have one yet:

 - Create Dev Space
 - Choose:
   - SAP Fiori
 - Start the dev space

2. Open the Command Palette

Use:
```
Ctrl + Shift + P
```
or:
```
F1
```

3. Start the generator

Search for:
```
Fiori: Open Application Generator
```
Select it.

or:

   <!-- ![BAS](images/bas_1.png)  -->
   <img src="images/bas_1.png" alt="BAS" width="60%">

4. Choose application type

Select:
 - SAP Fiori elements

   <!-- ![BAS](images/bas_2.png)  -->
   <img src="images/bas_2.png" alt="BAS" width="60%">

5. Choose floorplan

Common options:
 - List Report Object Page
 - Overview Page
 - Worklist
 - Analytical List Page

Most common:
 - List Report Object Page
   <!-- ![BAS](images/bas_3.png)  -->
   <img src="images/bas_3.png" alt="BAS" width="60%">
   
6. Select data source

Usually:
 - Connect to an OData Service

Options may include:
 - ABAP System
 - CAP Project
 - Local Metadata File

7. Select system/destination

Choose:
 - Existing destination
 - SAP system connection

Example:
 - S/4HANA
 - Gateway
 - Local CAP service

If needed, log in with SAP credentials.

8. Select OData service

Choose:
- OData V2 or V4 service

   <!-- ![BAS](images/bas_4.png)  -->
   <img src="images/bas_4.png" alt="BAS" width="60%">

9. Select main entity set

Example:

   <!-- ![BAS](images/bas_5.png)  -->
   <img src="images/bas_5.png" alt="BAS" width="60%">

This becomes the main page entity.

10. Configure app details

   <!-- ![BAS](images/bas_6.png)  -->
   <img src="images/bas_6.png" alt="BAS" width="60%">
   
11. Configure deployment target (optional)

You can:
 - Configure later
 - Or specify ABAP deployment now

   <!-- ![BAS](images/bas_7.png)  -->
   <img src="images/bas_7.png" alt="BAS" width="60%">

Usually easiest:
 - Skip for now

12. Finish generation

Click:
```
Finish
```
BAS will:
 - Generate the project
 - Install npm dependencies
 - Create UI5 configuration
 - Generate annotations setup

13. Wait for project generation

Generated structure typically:
```
webapp/
  manifest.json
  annotations/
  localService/
package.json
ui5.yaml
```

### Run Fiori Elements App (Optional)
1. Right click webapp/
   <!-- ![BAS](images/bas_prew_1.png)  -->
   <img src="images/bas_prew_1.png" alt="BAS" width="60%">

   <!-- ![BAS](images/bas_prew_2.png)  -->
   <img src="images/bas_prew_2.png" alt="BAS" width="60%">

2. Start the local UI5 server

Most Fiori elements apps use one of these commands.

Option A — Preferred

   <!-- ![BAS](images/bas_npm_1.png)  -->
   <img src="images/bas_npm_1.png" alt="BAS" width="40%">

```
npm run start
```

This usually runs:
UI5 server
Fiori tools preview middleware
OData proxy

3. Wait for startup

Typical successful output:

Server started
```
URL: http://localhost:8080
```

In BAS:

Click the popup(allow popup):
Open in New Tab

4. Stop the preview server

Press:
```
CTRL + C
```
in the terminal.
  
### Deploy Fiori Elements App (Optional)

   <!-- ![BAS](images/bas_appinfo_2.png)  -->
   <img src="images/bas_appinfo_1.png" alt="BAS" width="60%">

   <!-- ![BAS](images/bas_appinfo_2.png)  -->
   <img src="images/bas_appinfo_2.png" alt="BAS" width="60%">


1. Open the terminal in BAS

In BAS:
```
Terminal → New Terminal
```
Go to your app folder if needed:
```
cd myprojectname
```
2. Install dependencies

Run:
```
npm install
```
This installs the deployment tooling from your package.json.

3. Verify deployment configuration

Check that your project contains:

 - ui5-deploy.yaml
 - package.json
 - webapp/

In package.json, you should typically see a deploy script like:
```
"scripts": {
  "build": "ui5 build --clean-dest",
  "deploy": "fiori deploy --config ui5-deploy.yaml"
}
```

4. Configure ui5-deploy.yaml

Example:
```
specVersion: "4.0"
metadata:
  name: customer.mynamespace.myprojectname
type: application
builder:
  resources:
    excludes:
      - /test/**
      - /localService/**
  customTasks:
    - name: deploy-to-abap
      afterTask: generateCachebusterInfo
      configuration:
        target:
          destination: FIORI-DEV-RF1
          url: http://dev.rits.rf1.mol.sys.corp:1001
          client: '100'
        app:
          name: ZMYAPPNAME
          description: My app deplyment description
          package: ZDV_RAP
          transport: RF1K999999
        exclude:
          - /test/
```
Key fields:

url → ABAP system URL
client → SAP client
name → BSP application name
package → ABAP package
transport → transport request

5. Add deployment tooling (if missing)

If fiori deploy is not available:
```
npm install --save-dev @sap/ux-ui5-tooling
npm install --save-dev @sap/ui5-builder-webide-extension
npm install --save-dev @ui5/cli
```
Sometimes also:
```
npm install --save-dev nwabap-ui5uploader
```

6. Build the app

Run:
```
npm run build
```
This creates the dist/ folder.

7. Deploy from terminal

Run:
   <!-- ![BAS](images/bas_npm_2.png)  -->
   <img src="images/bas_npm_2.png" alt="BAS" width="40%">

```
npm run deploy
```
