# 🏬 GL Retail Corporation - Financial Reporting Automation (Power BI + Excel)

<img width="1584" height="396" alt="banner" src="/images/banner.png" />

---

## 📚 Project Navigation

- [🏬 Project Overview](#-project-overview)  
- [🎯 Objectives](#-objectives)  
  - [1️⃣ Automate & Visualize Reporting Integrity](#1-automate--visualize-reporting-integrity)  
  - [2️⃣ Improve Reporting Efficiency](#2-improve-reporting-efficiency)  
  - [3️⃣ Develop New Insights](#3-develop-new-insights)
- [🏢 Organization Context](#-organization-context)
- [⚙️ Tools & Technologies](#️-tools--technologies)
- [📊 Project Components](#-project-components)  
  - [1️⃣ Accounts Receivable (AR) & Accounts Payable (AP) Ageing](#1-accounts-receivable-ar--accounts-payable-ap-ageing)  
  - [2️⃣ Financial Statements](#2-financial-statements)  
  - [3️⃣ Sales & Inventory Analysis](#3-sales--inventory-analysis)
- [🧩 Workflow Summary](#-workflow-summary)
- [🗄️ Data Extraction — SQL View (vwGLTrans)](#️-data-extraction--sql-view-vwgltrans)
- [🧩 Data Modeling in Power BI](#-data-modeling-in-power-bi)
  - [⚙️ Steps to Build the Data Model](#️-steps-to-build-the-data-model)
- [💰 Financial Statement — Power BI Automation](#-financial-statement--power-bi-automation)
  - [🧾 Income Statement DAX Calculation](#-income-statement-dax-calculation)
  - [🧾 Balance Sheet DAX Calculation](#-balance-sheet-dax-calculation)
- [📈 Financial Ratios — Analytical Insights](#-financial-ratios--analytical-insights)
- [💸 Cash Flow Statement — Excel Integration](#-cash-flow-statement--excel-integration)
  - [🎯 Objectives](#-objectives)
  - [⚙️ Approach](#️-approach)
  - [📊 Income Statement (Excel)](#-income-statement)
  - [🧾 Balance Sheet (Excel)](#-balance-sheet-statement)
  - [🧮 Steps to Build the Cash Flow Statement](#-steps-to-build-the-cash-flow-statement)
- [✅ Result](#-result)

---

## 🧾 Project Overview

**GL Retail Corporation** — a multi-store retail company with a data-driven culture — hired me as a **Business Intelligence Developer** to automate and modernize their reporting systems.

The goal was to enhance data reliability, improve report efficiency, and uncover deeper financial insights through modern BI tools.

> ⚠️ **Note:**  
> The Power BI (`.pbix`) and Excel file (`.xlsx`) are **not published publicly** to maintain data privacy and protect the analytical model.  
> Only report screenshots and insights are shared for portfolio demonstration.

---

## 🎯 Objectives

### 1. Automate & Visualize Reporting Integrity
- Streamlined manual AR & AP Ageing reports.
- Consolidated data from multiple sources into a single data model.
- Reduced manual adjustments by automating calculations in Power Query.

### 2. Improve Reporting Efficiency
- Automated **Income Statement**, **Balance Sheet**, and **Cash Flow** reporting using Power BI.
- Built dynamic dashboards with drill-through and ratio analysis.
- Integrated **Excel** for ad-hoc financial analysis.

### 3. Develop New Insights
- Enhanced visibility into **product category performance** and **inventory efficiency**.
- Identified data misallocations across sales categories.
- Enabled leadership to make faster, insight-driven decisions.

---

## 🏢 Organization Context

| Attribute | Description |
|------------|-------------|
| **Company Name** | GL Retail Corporation |
| **Industry** | Retail |
| **Head Office** | 1 Head Office + 5 Retail Stores |
| **Approach** | Data-driven decision-making |
| **Objective** | Turn good performance into great through analytics |

---

## ⚙️ Tools & Technologies

| Tool | Purpose |
|------|----------|
| **Azure Data Studio** | Data extraction from company database |
| **Power Query** | Data transformation and modeling |
| **Power BI** | Financial statement dashboards & visualizations |
| **Microsoft Excel** | Additional financial analysis and validation |

---

## 📊 Project Components

### 1. **Accounts Receivable (AR) & Accounts Payable (AP) Ageing**
- Automated ageing calculations using Power Query.
- Replicated the existing Excel format for familiarity.
- Ensured real-time refresh from database connections.

### 2. **Financial Statements**
- Developed automated **Income Statement**, **Balance Sheet**, and **Cash Flow** dashboards.
- Visualized **key financial ratios** (Profit Margin, Current Ratio, Debt-to-Equity).
- Created a high-level summary view for management.

### 3. **Sales & Inventory Analysis**
- Identified and corrected product/category misallocations.
- Improved accuracy for cross-team reporting.
- Analyzed category performance for strategic product planning.

---

## 🧩 Workflow Summary

```text
Azure Data Studio → Power Query → Power BI Financial Dashboards → Excel Analysis
```

- Data Extraction: Connected to the company’s SQL database.
- Data Modeling: Cleaned, merged, and structured data in Power Query.
- Visualization: Built interactive dashboards for financial KPIs.
- Validation: Cross-checked results and ratios in Excel.

---

## 🗄️ Data Extraction — SQL View (vwGLTrans)

To centralize and simplify the data needed for financial statements, a SQL view was created in Azure Data Studio.
This view joins multiple dimension and fact tables to provide a unified dataset for Power BI modeling.

```sql
-- =============================================
-- Author:      Sujeet Singh
-- Project:     GL Retail Corporation - BI Automation
-- Description: Consolidated view for Financial Statements
-- =============================================

CREATE VIEW vwGLTrans
AS
    -- This view contains all the information required 
    -- to create automated financial statements in Power BI.

    SELECT 
        -- FactGLTran
        gl.FactGLTranID,
        gl.JournalID,
        gl.GLTranDescription,
        gl.GLTranAmount,
        gl.GLTranDate,

        -- dimGLAcct
        acc.AlternateKey AS GLAccNum,
        acc.GLAcctName,
        acc.[Statement],
        acc.Category,
        acc.Subcategory,

        -- dimStore
        store.AlternateKey AS StoreNum,
        store.StoreName,
        store.ManagerID,
        store.PreviousManagerID,
        store.ContactTel,
        store.AddressLine1,
        store.AddressLine2,
        store.ZipCode,

        -- dimRegion
        region.AlternateKey AS RegionNum,
        region.RegionName,
        region.SalesRegionName,

        -- Metadata: Last Refresh Timestamp
        CONVERT(
            DATETIME2, 
            GETDATE() AT TIME ZONE 'UTC' AT TIME ZONE 'Central Standard Time'
        ) AS [Last Refresh Date]

    FROM dbo.FactGLTran AS gl
    INNER JOIN dbo.dimGLAcct AS acc 
        ON gl.GLAcctID = acc.GLAcctID
    INNER JOIN dbo.dimStore AS store 
        ON gl.StoreID = store.StoreID
    INNER JOIN dbo.dimRegion AS region 
        ON store.RegionID = region.RegionID;
GO
```

---

## 🧩 Data Modeling in Power BI

After extracting data from the SQL view (`vwGLTrans`) created in **Azure Data Studio**, the next step was to build a robust, relational data model using **Power Query Editor** and **DAX**.

---

### ⚙️ Steps to Build the Data Model

#### 1️⃣ Connect to the Database
- Connected **Power BI** to the company database via **Azure SQL Database**.  
- Imported the SQL View `vwGLTrans` as the single source of truth for all financial reporting.

#### 2️⃣ Load as a Staging Query
- In **Power Query Editor**, the SQL view was loaded as a **staging query**.  
- The staging query acts as a **raw data layer**, ensuring that transformations can be referenced rather than repeated.

#### 3️⃣ Create Fact and Dimension Queries
- **Fact Table:** `FactGLTran` (transactions)  
- **Dimension Tables:** `dimGLAcct`, `dimStore`, `dimRegion`  
- Each was created by referencing the staging query and filtering relevant columns.

#### 4️⃣ Build Relationships
- Linked dimensions to the fact table using **primary and foreign keys**:
  - `FactGLTran[GLAcctID]` → `dimGLAcct[GLAcctID]`
  - `FactGLTran[StoreID]` → `dimStore[StoreID]`
  - `dimStore[RegionID]` → `dimRegion[RegionID]`
- Ensured correct **cardinality (Many-to-One)** and **referential integrity** for accurate analysis.

---

## 💰 Financial Statement — Power BI Automation


### 🧩 Steps to Build the Income Statement & Balance Sheet

<img width="1584" height="396" alt="Income Statement" src="/images/income-statement.png" />

#### 1️⃣ Create a Custom Headers Table in Excel
- Designed a **Headers Table** to control the contents and appearance of the financial statements.
- Defined **sorting order**, **hierarchy**, and **display names** for all financial line items.
- This table serves as the foundation for building a flexible and easily maintainable report.

#### 2️⃣ Import Headers Table to Power BI
- Imported the custom Headers Table into the **Power BI Data Model**.
- Used it as a **template** for dynamically organizing and formatting line items.

#### 3️⃣ Create DAX Measures for Income Statement Logic
Built key DAX measures to automate financial statement calculations and enable dynamic reporting.

---

## 🧾Income Statement DAX Calculation

🔹**Sum of all transactions**
```DAX
SumAmount = SUM(FactGLTran[GLTranAmount])
```

🔹**Income Statement transactions only**
```DAX
I/S Amount = CALCULATE(
    ABS([SumAmount]),
    DimHeaders[Statement] = "Income Statement"
)
```

🔹**Running subtotal for each section**
```DAX
I/S Subtotal = CALCULATE(
    [I/S Amount],
    FILTER(ALL(DimHeaders), DimHeaders[Sort] < MAX(DimHeaders[Sort]))
)
```

🔹** % of Revenue (staging)**
```DAX
Staging % of Revenue =
VAR Revenue =
    CALCULATE([I/S Amount], FILTER(ALL(DimHeaders), DimHeaders[Category] = "Revenue"))
RETURN
    DIVIDE([I/S Subtotal], Revenue, 0)
```

🔹**Final formatted % of Revenue**
```DAX
% of Revenue = FORMAT([Staging % of Revenue], "0.00%")
```
---

🔹**Flip sign for Waterfall Chart**
```DAX
Sign Flip SumAmount = [SumAmount] * -1
```

🔹**Gross Margin Ratio**
```DAX
Gross Margin Ratio =
VAR GrossProfit = CALCULATE([I/S Subtotal], DimHeaders[Category] = "Gross Profit")
VAR Revenue = ABS(CALCULATE([SumAmount], DimHeaders[Category] = "Revenue"))
RETURN DIVIDE(GrossProfit, Revenue, 0)
```

🔹**Operating Margin Ratio**
```DAX
Operating Margin Ratio =
VAR OperatingMargin = CALCULATE([I/S Subtotal], DimHeaders[Category] = "EBIT")
VAR Revenue = ABS(CALCULATE([SumAmount], DimHeaders[Category] = "Revenue"))
RETURN DIVIDE(OperatingMargin, Revenue, 0)
```

🔹**Gross Profit %**
```DAX
Gross Profit % = CALCULATE([Staging % of Revenue], DimHeaders[Category] = "Gross Profit %")
```

🔹**Main Income Statement measure (switch logic)**
```DAX
Income Statement =
VAR Display_Filter = NOT ISFILTERED(DimGLAccts[Subcategory])
RETURN
    SWITCH(
        TRUE(),
        SELECTEDVALUE(DimHeaders[MeasureName]) = "Subtotal" && Display_Filter, [I/S Subtotal],
        SELECTEDVALUE(DimHeaders[MeasureName]) = "Per_Of_Revenue" && Display_Filter, [% of Revenue],
        [I/S Amount]
    )
```

🔹 **Testing Measures**
```DAX
Is Subtotal = SELECTEDVALUE(DimHeaders[MeasureName]) = "Subtotal"
Is Filtered = ISFILTERED(DimGLAccts[Subcategory])
Is Not Filtered = NOT ISFILTERED(DimGLAccts[Subcategory])
Is Subtotal & Is Not Filtered = [Is Subtotal] && [Is Not Filtered]
```

---

## 🧾 Balance Sheet DAX Calculation

<img width="1584" height="396" alt="Balance Sheet" src="/images/balance-sheet.png" />

### 🧩 Steps to Build the Balance Sheet

The Balance Sheet report in Power BI automates the calculation of assets, liabilities, and equity — dynamically updating as transactions flow into the system. It ensures accurate reflection of retained earnings and equity relationships across periods.

🔹 **Balance Sheet Amount**
```DAX
B/S Amount =
CALCULATE(
    ABS([SumAmount]),
    DimHeaders[Statement] = "Balance Sheet"
)
```

🔹 **Cumulative Amount for each section**
```DAX
Cumulative Amount =
CALCULATE(
    ABS[B/S Amount]),
    FILTER(
        ALL(DimDate),
        DimDate[Date] <= MAX(DimDate[Date])
    )
)
```

🔹 **Balance Sheet Subtotal for each section**
```DAX
B/S Subtotal =
CALCULATE(
    [Cumulative Amount],
    ALL(DimHeaders),
    DimHeaders[Balance Sheet Section] IN VALUES(DimHeaders[Balance Sheet Section])
)
```

🔹 **Opening Retained Earnings**
```DAX
Opening Retained Earnings =
CALCULATE(
    ABS([SumAmount]),
    FactGLTran[GLAcctNum] = 4100,
    ALL(DimDate),
    ALL(DimHeaders)
)
```

🔹 **Retained Earnings**
```DAX
Retained Earnings =
[Opening Retained Earnings] +
CALCULATE(
    ABS([SumAmount]),
    FILTER(ALL(DimDate), DimDate[Date] <= MAX(DimDate[Date])),
    FILTER(ALL(DimHeaders), DimHeaders[Statement] = "Income Statement")
)
```

🔹 **Total Equity**
```DAX
Total Equity = [B/S Subtotal] + [Retained Earnings]
```

🔹 **Total Liabilities & Equity**
```DAX
Total Liabilities & Equity =
CALCULATE(
    [Cumulative Balance],
    ALL(DimHeaders),
    DimHeaders[Balance Sheet Section] = "Total Liabilities"
        || DimHeaders[Balance Sheet Section] = "Total Equity"
)
+ [Retained Earnings]
```

🔹 **Final Balance Sheet Measure**
```DAX
Balance Sheet =
VAR Display_Filtered = NOT ISFILTERED(DimGLAccts[Subcategory])
RETURN
SWITCH(
    TRUE(),
    SELECTEDVALUE(DimHeaders[MeasureName]) = "Section_Subtotal" && Display_Filtered, [B/S Subtotal],
    SELECTEDVALUE(DimHeaders[MeasureName]) = "Retained_Earnings" && Display_Filtered, [Retained Earnings],
    SELECTEDVALUE(DimHeaders[MeasureName]) = "Total_Equity" && Display_Filtered, [Total Equity],
    SELECTEDVALUE(DimHeaders[MeasureName]) = "Total_LE" && Display_Filtered, [Total Liabilities & Equity],
    [Cumulative Balance]
)
```

## 📈 Financial Ratios — Analytical Insights

These measures enable real-time ratio analysis directly from the Power BI dashboard, providing management with deeper insights into the company’s financial health.

#### 1️⃣ Gross Margin Ratio

```DAX
Gross Margin Ratio =
VAR GrossProfit = CALCULATE([I/S Subtotal], DimHeaders[Category] = "Gross Profit")
VAR Revenue = ABS(CALCULATE([SumAmount], DimHeaders[Category] = "Revenue"))
RETURN DIVIDE(GrossProfit, Revenue, 0)
```

#### 2️⃣ Operating Margin Ratio

```DAX
Operating Margin Ratio =
VAR OperatingMargin = CALCULATE([I/S Subtotal], DimHeaders[Category] = "EBIT")
VAR Revenue = ABS(CALCULATE([SumAmount], DimHeaders[Category] = "Revenue"))
RETURN DIVIDE(OperatingMargin, Revenue, 0)
```

#### 3️⃣ Current Ratio

```DAX
Current Ratio =
VAR CurrentAssets = CALCULATE([Cumulative Amount], DimHeaders[Category] = "Current Assets")
VAR CurrentLiabilities = CALCULATE([Cumulative Amount], DimHeaders[Category] = "Current Liabilities")
RETURN DIVIDE(CurrentAssets, CurrentLiabilities, 0)
```

#### 4️⃣ Debt Ratio

```DAX
Debt Ratio =
VAR TotalDebt = CALCULATE([Cumulative Amount], DimGLAccts[Subcategory] = "Long-term Debt")
VAR TotalAssets = CALCULATE([B/S Subtotal], DimHeaders[Category] = "Total Assets")
RETURN DIVIDE(TotalDebt, TotalAssets, 0)
```
---

## 💸 Cash Flow Statement — Excel Integration

The final phase of the project focused on **extending Power BI’s centralized data model to Excel**, enabling financial teams to perform **ad-hoc analysis** while maintaining a **single source of truth**.

---

### 🎯 Objectives
- Allow the **Finance Team** to access the same Power BI data model directly in **Excel**.  
- Develop an **automated Cash Flow Statement** that updates dynamically with the centralized model.

---

### ⚙️ Approach
- After publishing the **Power BI dataset** to the **Power BI Service**, the model was connected to **Excel** using the *“Analyze in Excel”* feature.  
- This ensured both platforms were **synchronized**, maintaining **data consistency** and **real-time refresh capability**.

**Workflow:**
```text
Power BI Service → Analyze in Excel → Cash Flow Statement Automation
```

By migrating the model to Excel, the finance team retained **live connectivity** to the Power BI dataset — ensuring everyone worked from the **same validated data source**.

---

### 📊 Income Statement

<img width="686" height="496" alt="Income Statement" src="/images/income_statement_excel.png" />

---

```text
=CUBEVALUE(
    "connection",
    "[Measures].[I/S Amount]",
    "[DimGLAccts].[GLAcctName].[All].[" & $B6 & "]",
    D$4
)
```

- $B6 → Refers to Income Statement line items such as Revenue, COGS, Direct Labour, etc.
- D$4 → Refers to the Year (e.g., 2019, 2020, 2021).

---

```text
=CUBEVALUE(
    "connection",
    "[Measures].[I/S Amount]",
    "[DimGLAccts].[Subcategory].[All].[" & $B18 & "]",
    "[DimHeaders].[Category].[All].[Operating Expenses]",
    D$4
)
```

- $B18 → Refers to Operating Expense line items such as Employee Expenses, Property Expenses, Office Supplies, Bank Charges Expenses, Depreciation Expenses, etc.
- D$4 → Refers to the Year (e.g., 2019, 2020, 2021).

---

### 🧾 Balance Sheet Statement

<img width="1080" height="1034" alt="Balance Sheet" src="/images/balance_sheet_excel.png" />

---

```text
=CUBEVALUE(
    "connection",
    "[Measures].[B/S Amount]",
    "[DimHeaders].[Category].[All].[Current Assets OR Non Current Assets]",
    "[DimGLAccts].[Subcategory].[All].[" & B40 & "]",
    "[FactGLTran].[GLTranDescription].[" & C$37 & "]"
)
```

- B40 → Refers to Balance Sheet line items such as Cash, Trade Receivables, Inventory, PPE, ROU Assets.
- C$37 → Refers to Opening Balance.

---

```text
=CUBEVALUE(
    "connection",
    "[Measures].[Cumulative Amount]",
    "[DimHeaders].[Category].[All].[Current Assets]",
    "[DimGLAccts].[Subcategory].[All].[" & $B40 & "]",
    D$37
)
```

- $B40 → Refers to Balance Sheet line items such as Cash, Trade Receivables, Inventory, PPE, ROU Assets.
- D$37 → Refers to Date/Year.

🧮 Note: The same logic applies to Liabilities — simply replace "Assets" with "Liabilities" in the formula to retrieve corresponding figures.

---

### 🧾 Steps to Build the Cash Flow Statement

<img width="730" height="550" alt="Cash Flow" src="/images/cash_flow_excel.png" />

---

#### 1️⃣ Populate Income Statement Values
- Used the **`CUBEVALUE()`** formula to pull Income Statement values directly from the published Power BI dataset.  
- Ensured accuracy and consistency with the Power BI version.

#### 2️⃣ Calculate Subtotals and Grand Totals
- Implemented **Excel’s `SUM()`** function for subtotals and consolidated totals.  
- Maintained formatting and hierarchy consistent with financial reporting standards.

#### 3️⃣ Populate Balance Sheet Values
- Applied **`CUBEVALUE()`** formulas to extract key Balance Sheet values.  
- Enabled drill-down and time-based analysis using connected slicers.

#### 4️⃣ Build the Cash Flow Statement
- Combined logic from both **Income Statement** and **Balance Sheet** to compute **cash inflows and outflows**.  
- Incorporated complex Excel formulas to handle:
  - **Operating**, **Investing**, and **Financing** activities.  
  - **Automatic linkage** between retained earnings and closing cash positions.
 
---

#### 🧩 Some CUBEVALUE Formulas Used

🔹**Purchase of PPE**
```text
=-CUBEVALUE(
    "connection",
    "[Measures].[SumAmount]",
    "[FactGLTran].[GLTranDescription].[All].[Purchase PPE]",
    "[DimGLAccts].[GLAcctName].[All].[Property, Plant & Equipment]",
    D$37
)
```

---

🔹**Principal Payment of Financing Leases**
```text
=-CUBEVALUE(
    "connection",
    "[Measures].[SumAmount]",
    "[FactGLTran].[GLTranDescription].[All].[Long Term Lease Liability - Principal Payment]",
    D$37
)
```

---

🔹 **Issuance of New Debt**
```text
=CUBEVALUE(
    "connection",
    "[Measures].[SumAmount]",
    "[DimGLAccts].[GLAcctNum].[All].[1000]",
    "[FactGLTran].[GLTranDescription].[All].[Term Loan Injection]",
    D$37
)
```

---

🔹 **Principal Repayment of Term Loan**
```text
=-CUBEVALUE(
    "connection",
    "[Measures].[SumAmount]",
    "[FactGLTran].[GLTranDescription].[All].[Term Loan - Principal Payment]",
    D$37
)
```

💡 Notes:
- D$37 → Refers to the reporting period (e.g., 2019, 2020, 2021).
- Negative signs (-CUBEVALUE) represent cash outflows (e.g., payments, purchases).
- Positive values represent cash inflows (e.g., financing or capital injections).

🔄 Now the Cash Flow Statement automatically updates as financial transactions flow into Power BI, providing a real-time and reliable view of cash movements.

---

### 📈 Result
✅ Unified financial reporting ecosystem across **Power BI and Excel**.  
✅ Real-time, automated **Cash Flow Statement** reflecting live company data.  
✅ Empowered finance teams to analyze and validate figures **without manual intervention**.  
✅ Maintained full consistency with **Power BI’s financial model and calculations**.

---

## 👨‍💻 Author

**Sujit Singh**  
Data Analyst | EXCEL | Tableau | Power BI | SQL | Python  
📧 [sujit10x12@gmail.com]  
🌐 [https://www.linkedin.com/in/sujit10x12/]  

---

## ⚠️ License

This project is covered under a **Proprietary License (All Rights Reserved)**.  
For details, see the [LICENSE.txt](./LICENSE.txt) file.

© 2025 **Sujeet Singh** — This repository is for personal portfolio demonstration only.  
No reuse, redistribution, or modification is permitted.

---
