# AdventureWorks HR / Employee Analytics Dashboard

A Power BI dashboard and summary report analysing the AdventureWorks Cycles workforce: headcount, pay structure, tenure, and sales performance by employee.

## Business Question

How is the workforce spread across departments and roles, how is pay structured, and do salespeople's results match their pay?

## Project Overview

- **Perspective:** HR / Employee Analytics, with a cross-sectional link to sales performance
- **Dataset:** AdventureWorks (Excel, provided by AltSchool Africa; not included in this repository)
- **Tools:** Power BI Desktop, Power Query, DAX
- **Deliverables:** 5-page dashboard, 2-page summary report, combined PDF submission

## Dashboard Pages

| Page | Focus | Key visuals |
|---|---|---|
| 1. Title | Name, perspective, navigation | Page buttons |
| 2. Staff | Workforce overview | Headcount, Female %, Male %, Salaried %; headcount by department and job title; gender and marital status |
| 3. Pay | Compensation | Average, highest and lowest current rate; pay records; rate by department and job title; pay frequency; average rate set by year |
| 4. Tenure | Tenure and workforce health | Average age, tenure, sick and vacation hours; hires by year; tenure and age bands; leave hours by job title |
| 5. Sales | Sales vs employees | Revenue, orders, average order value; revenue by salesperson; order split; pay vs revenue; revenue by year |

## Data Preparation (Power Query)

1. Loaded 7 tables: Employee, EmployeeDepartmentHistory, Department, Shift, EmployeePayHistory, SalesPerson, SalesOrderHeader
2. Profiled each table for errors, blanks and duplicates
3. Removed unneeded columns (including sensitive IDs) and set data types; `SalesOrderID` was stored as a date and was converted to a whole number
4. Relabelled coded fields: Gender, Marital Status, Pay Frequency
5. Filtered to current employees (`CurrentFlag = 1`) and current department (`EndDate` blank)
6. Merged Employee, EmployeeDepartmentHistory, Department and Shift into one `HR Master` table (290 rows, 290 distinct IDs)
7. Built a `CurrentPay` helper (Group By employee, Max of Rate) and merged it into `HR Master` as `CurrentRate`

## Data Model

- **Dimensions:** HR Master, SalesPerson, DATE
- **Facts:** EmployeePayHistory, SalesOrderHeader
- **Relationships:** one-way filters from dimension to fact. HR Master and SalesPerson are linked 1 to 1.

## Key DAX Measures

```DAX
Headcount = DISTINCTCOUNT('HR Master'[BusinessEntityID])

Female % =
DIVIDE(CALCULATE([Headcount], 'HR Master'[Gender] = "Female"), [Headcount])

Avg Current Rate = AVERAGE('HR Master'[CurrentRate])

Pay Records = COUNTROWS('HumanResources EmployeePayHistory')

Age = INT(YEARFRAC('HR Master'[BirthDate], MAX('Sales SalesOrderHeader'[OrderDate])))

Total Revenue = SUM('Sales SalesOrderHeader'[SubTotal])
```

## Key Insights

- Production has 179 of 290 employees (62%); 71% of staff are male and 29% are female
- Production averages a pay rate of 14.15, about 22% below the company average of 18.19
- 77% of the workforce was hired in 2008 and 2009, so 75% have 5 to 7 years of tenure
- Vacation hours vary widely in one job family: Prod. Tech WC10 averages 91 hours, WC20 only 10.5
- Online orders are 88% of orders but only about 27% of the $109.85M revenue
- Salespeople on the same pay rate generate very different revenue ($0.17M to $10.37M), so pay does not track sales results

## Recommendations

1. Plan retention and career paths for the 2008-2009 hiring cohort
2. Review Production pay bands against the market
3. Improve gender balance and check pay equity by role
4. Review leave balances and policy for Production Technician work centres
5. Link sales pay (bonus and commission) to results
6. Confirm the order date range before judging yearly revenue trends

## Assumptions

- Current employees only, in their current department
- Current pay = the highest rate on record per employee (assumes pay only goes up)
- Age and tenure are measured at the latest order date in the data, not today
- Revenue uses `SubTotal`; online orders have no salesperson and are excluded from salesperson charts
- Only 17 employees are salespeople

## Repository Structure

```
adventureworks-hr-employee-analytics/
├── README.md
├── AdventureWorks_HR_Dashboard.pbix
├── AdventureWorks_HR_Summary_Report.pdf
├── AdventureWorks_Analysis.pdf
└── images/
    ├── 02_staff.png
    ├── 03_pay.png
    ├── 04_tenure.png
    └── 05_sales.png
```

## How to Open

1. Download `AdventureWorks_HR_Dashboard.pbix`
2. Open it in Power BI Desktop
3. Use the navigation pane on the left and the slicers to explore

## Author

**Danjuma Zainab**
Data Analytics student, AltSchool Africa
