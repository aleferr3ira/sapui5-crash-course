## 🚀 SAPUI5 Homework Exercise: Northwind Product Viewer

**🎯 Goal:** Build a sleek SAPUI5 Freestyle application that displays products from the Northwind OData V4 service in a responsive table (`sap.m.Table`) and allows users to filter products by name using a search field (`sap.m.SearchField`).

---

### 🛠️ Prerequisites:

Before you start, ensure you have the following:

* ✅ Access to **SAP Business Application Studio (BAS)** or **Visual Studio Code** with:
    * **Node.js** installed.
    * **SAP Fiori tools Extension Pack** installed.
* ✅ Basic knowledge of SAPUI5 project structure (e.g., Views, Controllers, `manifest.json`).
* ✅ Familiarity with the **Application Generator**.

---

### 🌐 OData Service Details:

We’ll use the public **Northwind OData V4 service**:

**Service URL:** `https://services.odata.org/V4/Northwind/Northwind.svc/`  
**Entity Set:** `Products`  

**Fields to Display (5 Columns):**

1️⃣ `ProductID`  
2️⃣ `ProductName`  
3️⃣ `UnitPrice`  
4️⃣ `UnitsInStock`  
5️⃣ `Discontinued` (Boolean: true/false)

---

### 📝 Step-by-Step Instructions:

#### **Step 1: Generate the Project**

1. Open your development environment (**BAS** or **VS Code**).  
2. Open the **Command Palette** (`Ctrl+Shift+P` or `F1`).  
3. Run the command: `Fiori: Open Application Generator`.  
4. Fill in the wizard options as follows:  
     * **Application Type:** SAP Fiori Freestyle  
     * **Floorplan:** SAPUI5 Application  
     * **Data Source:** Connect to an OData Service  
     * **OData Service URL:** Enter `https://services.odata.org/V4/Northwind/Northwind.svc/` and click "Go".  
     * Select the `Products` entity set (no navigation properties).  
     * **View Name:** `Main` (or keep the default `View1`).  
     * **Module Name:** e.g., `NorthwindProductsApp`  
     * **Application Title:** e.g., "Northwind Products"  
     * **Application Namespace:** e.g., `com.mycompany.northwind`  
     * Leave other settings as default and click **Finish**.  

5. The generator will create the project structure and configure the OData service connection in `manifest.json`.

---

#### **Step 2: Design the View (`webapp/view/Main.view.xml`)**

Replace the `<content>` aggregation of the `<Page>` in `Main.view.xml` with the following:

```xml
<mvc:View
        controllerName="com.mycompany.northwind.controller.Main"
        xmlns:mvc="sap.ui.core.mvc"
        displayBlock="true"
        xmlns="sap.m">
        <Page id="page" title="{i18n>title}">
                <!-- 🔍 Add a SearchField in the SubHeader -->
                <subHeader>
                        <Bar id="searchBar">
                                <contentMiddle>
                                        <SearchField
                                                id="searchField"
                                                width="50%"
                                                placeholder="Search by Product Name..."
                                                search=".onSearch" />
                                </contentMiddle>
                        </Bar>
                </subHeader>
                <content>
                        <!-- 📋 Table to display products -->
                        <Table
                                id="productsTable"
                                inset="false"
                                items="{
                                        path: '/Products',
                                        sorter: { path: 'ProductName' }
                                }"
                                class="sapUiResponsiveMargin"
                                width="auto">

                                <!-- Define Table Columns -->
                                <columns>
                                        <Column id="colProdId" width="5em">
                                                <Text text="Product ID" />
                                        </Column>
                                        <Column id="colProdName" minScreenWidth="Tablet" demandPopin="true">
                                                <Text text="Product Name" />
                                        </Column>
                                        <Column id="colUnitPrice" hAlign="End" minScreenWidth="Desktop" demandPopin="true">
                                                <Text text="Unit Price" />
                                        </Column>
                                        <Column id="colUnitsInStock" hAlign="End" minScreenWidth="Desktop" demandPopin="true">
                                                <Text text="Units In Stock" />
                                        </Column>
                                        <Column id="colDiscontinued" hAlign="Center" minScreenWidth="Tablet" demandPopin="true">
                                                <Text text="Discontinued" />
                                        </Column>
                                </columns>

                                <!-- Define Row Template -->
                                <items>
                                        <ColumnListItem id="itemTemplate" vAlign="Middle">
                                                <cells>
                                                        <Text text="{ProductID}" />
                                                        <Text text="{ProductName}" />
                                                        <Text text="{
                                                                path: 'UnitPrice',
                                                                type: 'sap.ui.model.type.Currency',
                                                                formatOptions: { showMeasure: false }
                                                        }" />
                                                        <Text text="{UnitsInStock}" />
                                                        <Text text="{Discontinued}" />
                                                </cells>
                                        </ColumnListItem>
                                </items>
                        </Table>
                </content>
        </Page>
</mvc:View>
```

---

#### **Step 3: Implement the Search Logic (`webapp/controller/Main.controller.js`)**

Add the following logic to your controller:

```javascript
sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "sap/ui/model/Filter",
        "sap/ui/model/FilterOperator"
], function (Controller, Filter, FilterOperator) {
        "use strict";

        return Controller.extend("com.mycompany.northwind.controller.Main", {
                onInit: function () {
                        // Initialization logic (if needed)
                },

                /**
                 * Handles the search event from the SearchField
                 * @param {sap.ui.base.Event} oEvent The search event
                 */
                onSearch: function (oEvent) {
                        // Get the search query
                        var sQuery = oEvent.getParameter("query");

                        // Create filters
                        var aFilter = [];
                        if (sQuery) {
                                aFilter.push(new Filter("ProductName", FilterOperator.Contains, sQuery));
                        }

                        // Get the table and its binding
                        var oTable = this.byId("productsTable");
                        var oBinding = oTable.getBinding("items");

                        // Apply the filters
                        oBinding.filter(aFilter);
                }
        });
});
```

---

#### **Step 4: Run and Test Your App**

1. Right-click the `webapp` folder or `index.html` file.  
2. Select **Preview Application** or run `npm start` in the terminal.  
3. Test the following:  
     * The table loads product data from the Northwind service.  
     * Enter a product name (e.g., "Chai") in the search field and press Enter. The table should filter the results.  
     * Clear the search field and press Enter to reset the table.

---

### 🎉 Expected Output:

Your app should look like this:

```
+------------------------------------------------------+
| Northwind Products               (Page Title)        |
+------------------------------------------------------+
| [ Search by Product Name...      ][🔍] (Search Bar)  |
+------------------------------------------------------+
| | Product ID | Product Name | Unit Price | ...     | | (Table Header)
| |------------|--------------|------------|---------| |
| | 1          | Chai         | 18.00      | 39      | | (Table Row 1)
| | 2          | Chang        | 19.00      | 17      | | (Table Row 2)
| | 3          | Aniseed Syrup| 10.00      | 13      | | (Table Row 3)
| | ...        | ...          | ...        | ...     | |
+------------------------------------------------------+
```

---

This exercise introduces you to **SAPUI5 project generation**, **OData integration**, **UI design with XML views**, and **event-driven filtering**. 🚀 Happy coding!
