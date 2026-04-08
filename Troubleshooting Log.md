(Note: Original documentation was created in Polish; screenshots reflect the Polish language version of Power BI/Power Query).
## [2026-01-22] Solving Decimal Separator Issue

**Problem:** A column containing decimal values was recognized as text because of the period (US format), which prevented calculations in Power BI (due to Polish regional settings expecting a comma).

**Solution:** Instead of a manual "find and replace" operation, I used the built-in Power Query feature: 
`Change Type` -> `Using Locale...` -> `English (United States)`.
<img width="1711" height="833" alt="image" src="https://github.com/user-attachments/assets/31521e2d-5bf5-4478-a97e-26f42296791c" />



**M Code (Power Query):**
```powerquery
#"Changed Type with Locale" = Table.TransformColumnTypes(#"Changed Type", {{"Physical_Activity", type number}}, "en-US")
```
## [2026-01-30] Data Profiling in Power Query

**Problem:** I needed to verify data quality (nulls, errors, duplicates, data distribution, and outliers).

**Solution**: Enabled Data Profiling tools in Power Query (**View** tab) to quickly assess the dataset's state before further processing.

**Features used**:

   * **Column Quality**: Provides a quick check of the percentage of Valid, Error, and Empty data.

   * **Column Distribution**: Displays a bar chart showing the number of Distinct and Unique values. (If Unique = Distinct, there are no duplicates).

   * **Column Profile**: Shows detailed statistics (Min, Max, Average, Standard Deviation) and a histogram. Useful for identifying nonsensical outliers.


### Key Concepts (Data Profiling):
In the "Column Distribution" section, Power BI provides two values that are often confused:

| Concept | Definition | Example `[1, 1, 2, 3]` |
| :--- | :--- | :--- |
| **Distinct** | All different values (each counted once). | **3** (values: 1, 2, 3) |
| **Unique** | Values that appear exactly once. | **2** (values: 2, 3) |

<img width="1491" height="987" alt="image" src="https://github.com/user-attachments/assets/9d41d2ba-27f3-4443-94b8-c10decf65f7b" />

## [2026-02-10] Loading Data from Google BigQuery

**Problem** Encountered an error while trying to fetch data from Google BigQuery (`Get Data` -> `More...` -> `Google BigQuery` -> `Connect`).
<img width="975" height="609" alt="image" src="https://github.com/user-attachments/assets/91f7e60d-cd13-40e8-b74b-2d4f9cc4b0b2" />


**Attempt 1**: Installed the 64-bit ODBC driver for BigQuery.

   **Result**: The error persisted.

**Attempt 2**: Disabled the Google BigQuery Storage API (suggested by AI troubleshooting).

   Path: `File` -> `Options and settings` -> `Options`

<img width="1001" height="800" alt="image" src="https://github.com/user-attachments/assets/405c11a4-a1eb-42db-846d-02e76faed85e" />
<img width="998" height="793" alt="image" src="https://github.com/user-attachments/assets/45644cd1-fd58-40ef-a590-9df7fd121658" />

**Result**: The issue was resolved, and the data loaded successfully.

## [2026-03-16] Increase in Distinct Values After Removing Duplicates
**Problem**: The number of distinct values unexpectedly increased (from 579 to 1000) after performing a "Remove Duplicates" operation in Power Query.

**Cause**: This is due to the default behavior of the Power Query Editor, which profiles data based only on the first 1000 rows. After removing duplicates, the sample structure changed, affecting the displayed statistics.

**Solution**: To get accurate information for the entire dataset, change the profiling mode:

   1.Look at the status bar in the bottom-left corner of the Power Query window.

   2.Click the message: `Column profiling based on top 1000 rows`.

   3.Select: `Column profiling based on entire data set`.

## [2026-03-16] "Flat Line" Issue in Charts (Relationship & Measure Errors)
**Problem**: After creating DAX measures (e.g., `Total Profit`), category charts displayed the same value for every data point (a flat line), even though the source data was varied.

**Attempt 1**: Verifying Table Relationships I checked the Model View for existing relationships between dimension tables (Category, Brand) and the fact table, ensuring the filtering direction was correct.

   **Solution**: Recreated the relationships, setting the cross-filter direction to "Single" (from the "1" dimension side to the "*" fact side).

   **Result**: The problem persisted – charts were still not responding to filtering.

**Attempt 2**: Verifying DAX Measure Context Formula analysis revealed an error in the data source reference.

   **Error**: The measure `Total Profit = SUM('Original_Table'[total_profit])` was referencing the technical (staging) table, which had no relationships with the new dimension tables.

   **Solution**: Updated the DAX code to sum columns directly from the new fact table.

   Corrected formula: `Total Profit = SUM('Fact_table'[total_profit])`

**Key Takeaways**:

   * **Filter Direction**: Always check the arrows in Model View – in a Star Schema, they should point towards the Fact Table.

   * **Table Management**: After finishing transformations in Power Query (creating duplicates/references), disable "Enable Load" for staging tables or hide them. This prevents creating measures in the wrong context and improves model clarity.
   
   <img width="399" height="460" alt="image" src="https://github.com/user-attachments/assets/ef86575c-a211-4d4e-932b-d8b427cfab06" />


## [2026-03-30] Inactive Relationships

**Problem**: Brand-specific charts displayed flat lines, while charts using other related tables worked correctly.

**Attempt 1**: Recreating Relationships Deleted and recreated the links, ensuring "Single" or "Both" filtering directions.

   **Result**: No change – the issue remained.

**Attempt 2**: Activating the Relationship Solution: I noticed the "Make this relationship active" option was unchecked. After enabling it, the model synchronized.

<img width="529" height="854" alt="image" src="https://github.com/user-attachments/assets/a8665702-c513-4ee0-b1ec-f4ac9591590e" />

**Result**: Charts now display correct, filtered values.

