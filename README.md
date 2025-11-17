# Automotive-Revenue-and-Insights-Dashboard
This project presents an Automotive Revenue & Customer Insights Dashboard developed using Power BI. The analysis focuses on vehicle purchases, customer demographics, and revenue trends across multiple automotive brands. Power BI was used not only for visualization but also for all data preparation and transformation.

The data cleaning process was performed in Power Query, where inconsistent formats, invalid values, and missing fields were corrected to ensure a reliable dataset. New timeNbased fields such as Month and Year were extracted from the main date column to enable deeper trend analysis and interactive filtering. Additionally, a new calculated column for Revenue was created by multiplying the unit price by the number of items purchased, providing a clear view of financial performance across categories.

Using these prepared fields, the dashboard highlights total revenue, customer age groups, monthly purchase behavior, gender distribution, and model-level performance patterns. Interactive filters allow users to explore insights across different customer segments, years, and car brands.


#POWER QUERY M CODE(Data cleaning + Month, Year + Revenue column)

let
    // Load source file
    Source = Excel.Workbook(File.Contents("CarSalesData.xlsx"), null, true),
    Sales_Sheet = Source{[Item="Sales", Kind="Sheet"]}[Data],

   // Promote first row to headers
    PromotedHeaders = Table.PromoteHeaders(Sales_Sheet, [PromoteAllScalars=true]),

// Clean text fields: trim spaces and standardize
    CleanText = Table.TransformColumns(
        PromotedHeaders,
        {
            {"CustomerName", Text.Trim, type text},
            {"Gender", Text.Proper, type text},
            {"Brand", Text.Proper, type text},
            {"Model", Text.Proper, type text}
        }
    ),
    // Change data types
    ChangeTypes = Table.TransformColumnTypes(
        CleanText,
        {
            {"CustomerID", Int64.Type},
            {"Age", Int64.Type},
            {"Purchase Date", type date},
            {"Price", type number},
            {"Purchased", Int64.Type}
        }
    ),

   // Remove rows with missing key values
    RemovedNulls = Table.SelectRows(
        ChangeTypes,
        each [Purchase Date] <> null and [Price] <> null and [Purchased] <> null
    ),
    // Create Revenue Column (Price × Purchased)
    AddRevenue = Table.AddColumn(
        RemovedNulls,
        "Revenue",
        each [Price] * [Purchased],
        type number
    ),

   // Extract Year
    AddYear = Table.AddColumn(
        AddRevenue,
        "Year",
        each Date.Year([Purchase Date]),
        Int64.Type
    ),

// Extract Month Name
    AddMonth = Table.AddColumn(
        AddYear,
        "Month",
        each Date.MonthName([Purchase Date]),
        type text
    ),

  // Final sorted data
    SortedRows = Table.Sort(AddMonth, {{"Purchase Date", Order.Ascending}})
in
    SortedRows
# Key challenges included:
•	No visibility into which car models or brands generate the highest revenue
	•	Difficulty identifying seasonal trends because dates were not structured
	•	No breakdown of purchases by age groups or gender
	•	Absence of a unified metric for total revenue, since it wasn’t provided in the dataset
	•	Inability to analyze performance across different periods (monthly, yearly)
	•	Manual reporting was slow, inconsistent, and lacked interactive insights

As a result, decision-makers could not determine:
	•	Where revenue was coming from
	•	Which customer segment to target
	•	Which models to promote or stock more of
	•	How performance changed over time


#  Business Goals

The primary business objectives of this project were:
	1.	Improve sales visibility by creating an automated analytics dashboard
	2.	Understand customer demographics (age & gender buying patterns)
	3.	Track total revenue and identify high-performing models
	4.	Analyze monthly and yearly sales trends for forecasting
	5.	Enable interactive filtering to support data-driven decisions
	6.	Reduce manual reporting time and improve accuracy


# Business Solution

To address the business problems and meet the goals:

 1. Data Cleaning & Preparation in Power BI
	•	Standardized inconsistent fields (brand, model, gender)
	•	Removed invalid or erroneous rows
	•	Ensured correct data types

 2. Feature Engineering

Created new analytical columns to improve insights:
	•	Year (extracted from full date)
	•	Month (extracted for trend analysis)
	•	Revenue = Price × Quantity Purchased

 3. Interactive Dashboard Development

Built a fully interactive Power BI dashboard featuring:
	•	Total revenue
	•	Sales by year
	•	Sales by month
	•	Purchases by gender and age group
	•	Top-performing car models
	•	Filters for deeper exploration (brand, gender, year, model)

 4. Business Insights

The dashboard now helps the business:
	•	Identify which models to promote
	•	Understand customer buying patterns
	•	Allocate marketing budgets effectively
	•	Forecast demand based on monthly/yearly trends
	•	Improve inventory planning

   Overall, this dashboard offers a clean, well-structured, and insight-driven view of sales performance, built end-to-end within Power BI—from data cleaning to modeling and final reporting.
