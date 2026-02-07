# Example SOP Table Output

This is an example of the Standard Operating Procedure (SOP) table that Agent 1 (Excel Analyzer) should produce when analysing an Excel file.

## Spreadsheet Overview

| Property | Value |
|---|---|
| File Name | monthly_revenue_report.xlsx |
| Sheets | 3 (Inputs, Calculations, Summary) |
| Purpose | Calculate monthly revenue by product line, apply discounts, and produce a summary report |

## SOP Process Steps

| Step | Sheet | Cell/Range | Type | Description | Dependencies | Notes |
|---|---|---|---|---|---|---|
| 1 | Inputs | A1:A12 | Static Input | Month names from January to December, listed vertically | None | Reference data — does not change |
| 2 | Inputs | B1:B12 | Static Input | Unit sales for each month — whole numbers representing units sold | None | These values are entered manually each month |
| 3 | Inputs | C1:C12 | Static Input | Unit price for each month in GBP | None | Price may vary month to month |
| 4 | Inputs | D1 | Static Input | Discount threshold — the revenue amount above which a discount is applied (e.g. £10,000) | None | Single value, applies globally |
| 5 | Inputs | D2 | Static Input | Discount rate — the percentage discount applied when the threshold is exceeded (e.g. 5%) | None | Single value, applies globally |
| 6 | Calculations | A1:A12 | Reference | Month names — copied from the Inputs sheet for readability | Step 1 | Direct reference to Inputs!A1:A12 |
| 7 | Calculations | B1:B12 | Formula | Gross revenue for each month — calculated by multiplying the unit sales by the unit price for that month | Steps 2, 3 | For each row: take the units sold and multiply by the price per unit |
| 8 | Calculations | C1:C12 | Formula | Discount amount for each month — if the gross revenue exceeds the discount threshold, apply the discount rate to the gross revenue; otherwise the discount is zero | Steps 4, 5, 7 | Conditional logic: check whether gross revenue is above the threshold. If yes, multiply gross revenue by the discount rate. If no, return zero. |
| 9 | Calculations | D1:D12 | Formula | Net revenue for each month — the gross revenue minus the discount amount | Steps 7, 8 | Simple subtraction of the discount from the gross revenue |
| 10 | Summary | A1 | Formula | Total gross revenue for the year — sum of all monthly gross revenues | Step 7 | Aggregation of the full year of gross revenues |
| 11 | Summary | A2 | Formula | Total discounts for the year — sum of all monthly discount amounts | Step 8 | Aggregation of the full year of discounts |
| 12 | Summary | A3 | Formula | Total net revenue for the year — sum of all monthly net revenues | Step 9 | Aggregation of the full year of net revenues |
| 13 | Summary | A4 | Formula | Average monthly net revenue — the total net revenue divided by 12 | Step 12 | Simple division of total net revenue by the number of months |
| 14 | Summary | A5 | Formula | Highest revenue month — identify which month had the highest net revenue | Step 9 | Look across all monthly net revenues and return the name of the month with the highest value |

## Validation Questions for User

1. Is the discount logic correct — should the discount apply to the **entire** gross revenue when the threshold is exceeded, or only to the amount **above** the threshold?
2. Are there any additional discount tiers or rules that are not captured in the spreadsheet?
3. Should the "highest revenue month" logic handle ties (multiple months with the same value)?
4. Are there any months that should be excluded from the average calculation (e.g. partial months)?
5. Is the unit price fixed for the year, or does it genuinely vary month to month as shown?
