# Atlas Lab — HR Analytics & Insights (Power BI Dashboard)

An interactive, five-page **Power BI** human resources and performance analytics report designed to track workforce headcount, demographic composition, performance appraisals, satisfaction metrics, and employee attrition drivers across **1,470** employees and **6,709** performance review records.

---

## 📊 Dashboard Pages & Key Insights

### 1. Overview
![Overview Page](images/overview.png)

Provides an executive snapshot of workforce scale, compensation, satisfaction, and hiring/attrition trends from **2012 to 2022**:
* **Headline KPIs:** **1,470** Total Employees | **1,233** Active Employees | **16%** Attrition Rate (**237** terminated) | **$112.96K** Avg Salary | **3.47** Avg Manager Rating | **3.98** Avg Self Rating | **3.65** Avg Overall Satisfaction
* **Active Employees by Department & Job Title:** Interactive decomposition tree breaking down the **1,233** active employees across **Technology** (**828**), **Sales** (**354**), and **Human Resources** (**51**) down to individual job roles.
* **Employees Hiring & Attrition Trend:** Stacked column trend comparing retained vs. attrited hires by hire year (`2012`–`2022`).

### 2. Demographics
![Demographics Page](images/demographics.png)

Examines workforce diversity, age distribution, education levels, and pay parity across active employees (**1,233**):
* **Age Range Cards:** Youngest Employee (**18**) and Oldest Employee (**51**).
* **Active Employees by Gender & Age Group:** Decomposition tree segmenting **Female** (**571**), **Male** (**537**), and **Non-Binary** (**105**) employees across age brackets (`20–29`, `30–39`, `40–49`, etc.).
* **Active Employees by Marital Status:** Donut breakdown across **Married** (**545** / **44.2%**), **Single** (**421** / **34.14%**), and **Divorced** (**267** / **21.65%**).
* **Compensation & Headcount Comparisons:** Combo charts comparing active headcount and average salary across **Education Level** (peaking at **$154K** for **Doctorate** holders vs. **$95K** for **No Formal Qualifications**), **Department**, and **Ethnicity**.

### 3. Performance Tracker
![Performance Tracker Page](images/performance_tracker.png)

Evaluates appraisal alignment, rating distributions, workplace satisfaction, and training completion across **6,709** performance reviews:
* **KPI Cards:** **3.47** Avg Manager Rating | **3.98** Avg Self Rating | **50.5%** Training Utilization | **3.43** Avg Job Satisfaction | **3.43** Avg Relationship Satisfaction | **3.87** Avg Environment Satisfaction | **3.41** Avg Work-Life Balance.
* **Self vs. Manager Rating by Tenure Level & Department/Role:** Highlights a consistent **+0.51** rating gap where employee self-evaluations (**3.98**) exceed manager evaluations (**3.47**).
* **Rating Distributions:** Donut charts breaking down review counts across `ManagerRating` (`2`–`5`) and `SelfRating` (`3`–`5`).

### 4. Individual Ratings & Satisfaction
![Individual Ratings & Satisfaction Page](images/individual_ratings.png)

Employee-level drill-down view (`Select Employee` slicer) displaying:
* Employee **Start Date**, **Last Review Date**, and **Review Count**.
* Year-over-year **Self vs. Manager Rating** alongside **Training Utilization %**.
* Year-over-year **Employee Satisfaction** across Job, Relationship, Work-Life Balance, and Environment dimensions.

### 5. Attrition
![Attrition Page](images/attrition.png)

Isolates turnover drivers across **237** terminated employees (**16.12%** overall attrition):
* **Attrition KPIs:** **1,233** Active Employees | **237** Terminated Employees | **$82.26K** Terminated Avg Salary vs. **$118.86K** Retained Avg Salary (**$36.6K** pay gap) | **2.43 yrs** Terminated Avg Tenure.
* **Key Attrition Drivers:**
  * **Tenure (`YearsAtCompany`):** Turnover spikes during onboarding at **34%** (`1` year) and **32%** (`0` years), dropping to **1%** by `10` years.
  * **Department & Job Role:** Highest attrition in **Sales Representative** (**40%**), **Recruiter** (**38%**), and **Data Scientist** (**24%**).
  * **Marital Status:** **Single** employees attrite at **23%**, compared to **13%** (**Married**) and **10%** (**Divorced**).
  * **Distance from Home (`Commute Band`):** Highest turnover in the **`>40 KM`** (**20%**) and **`1–5 KM`** (**19%**) commute bands.

---

## 🗂️ Data Model & Schema

The report is built on a **Star Schema** combining HR master data, performance reviews, lookup dimensions, and a dynamic DAX calendar:

| Table | Type | Description |
| :--- | :--- | :--- |
| `DimEmployee` | Dimension | **1,470** employee records (`EmployeeID`, demographics, `Department`, `JobRole`, `Salary`, `HireDate`, `OverTime`, `Attrition`, tenure metrics) |
| `FactPerformanceRating` | Fact | **6,709** review records (`PerformanceID`, `EmployeeID`, `ReviewDate`, satisfaction scores, training counts, `SelfRating`, `ManagerRating`) |
| `DimDate` | Dimension | Dynamic DAX calendar table spanning min/max employee hire years with calendar and April-start fiscal attributes |
| `DimEducationLevel` | Lookup | Maps `Education` IDs (`1`–`5`) to labels (`No Formal Qualifications` to `Doctorate`) |
| `DimRatingLevel` | Lookup | Maps rating IDs (`1`–`5`) to labels (`Unacceptable` to `Above and Beyond`) |
| `DimSatisfiedLevel` | Lookup | Maps satisfaction IDs (`1`–`5`) to labels (`Very Dissatisfied` to `Very Satisfied`) |

---

## 🧮 Key DAX Calculations

```dax
Total Employees = DISTINCTCOUNT(DimEmployee[EmployeeID])

Active Employees = CALCULATE(COUNTROWS(DimEmployee), DimEmployee[Attrition] = "No")

Terminated Employees = CALCULATE(COUNTROWS(DimEmployee), DimEmployee[Attrition] = "Yes")

Attrition Rate % = DIVIDE([Terminated Employees], [Total Employees])

Avg Manager Rating = AVERAGE(FactPerformanceRating[ManagerRating])

Avg Self Rating = AVERAGE(FactPerformanceRating[SelfRating])

Training Completion % =
DIVIDE(
    SUM(FactPerformanceRating[TrainingOpportunitiesTaken]),
    SUM(FactPerformanceRating[TrainingOpportunitiesWithinYear])
)
