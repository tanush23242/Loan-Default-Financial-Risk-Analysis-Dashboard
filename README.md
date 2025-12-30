
# 📊 Loan Default & Financial Risk Analysis Dashboard
(Power BI | SQL Server | Dataflow | Advanced DAX)

## Dashboard Link: https://app.powerbi.com/links/YcdcmFKhlt?ctid=bc5b2879-3fac-469a-b8c4-994705bc09d7&pbi_source=linkShare

## Project Overview

This project focuses on analyzing loan performance, customer demographics, and default risk using Power BI.
The dashboard is designed to help banks and financial institutions understand:
- How loans are distributed across different customer segments
- Which groups are more likely to default
- How risk changes over time (YOY & YTD trends)
- How customer attributes like age, income, employment, education, and credit score affect loan behavior
The project follows a real-world BI workflow, starting from SQL Server, moving through Power BI Dataflows, and finally creating an interactive, multi-page Power BI dashboard.

## Problem Statement

Financial institutions deal with a large volume of loan applications every year.
Without proper analysis, it becomes difficult to:
- Identify high-risk customer segments
- Monitor default trends over time
- Understand the impact of credit score, income, and employment
- Make informed lending and risk-control decisions

This dashboard aims to solve these problems by converting raw loan data into clear, visual, and actionable insights.

## Data Source & Architecture

- **Dataset Name:** Loan Default Dataset  
- **Data Source:** SQL Server  
- **Integration Method:** Power BI Dataflow (Standard Mode Gateway)  
- **Model Type:** Single fact table with calculated columns and measures  
- **Refresh Type:** Scheduled Refresh & Incremental Refresh  

**Why Dataflow?**
- Centralized data transformation  
- Reusability across reports  
- Better performance  
- Enterprise-level BI practice  

## Steps Followed
    1. Installed and configured Standard Mode Gateway
    2. Installed Microsoft SQL Server
    3. Imported loan dataset into SQL Server
    4. Created Power BI Dataflow to connect SQL Server data
    5. Performed data cleaning and transformation in Dataflow
    6. Enabled Column Quality, Column Distribution, and Column Profile
    7. Loaded data into Power BI Desktop from Dataflow
    8. Created calculated columns and DAX measures
    9. Designed multi-page interactive dashboard
    10. Published report to Power BI Service
    11. Configured Scheduled and Incremental Refresh

## Calculated Columns & Measures (DAX)

Below are all the calculated columns and measures used in this project, written together for easy reference and understanding.

### COLUMN TABLE
```DAX
Age Groups = 
 IF('Loan_default'[Age]<=19,"Teen",
    IF('Loan_default'[Age]<=39,"Adults",
    IF('Loan_default'[Age]<=59, "Middle Age Adults",
    "Senior Citizens")))

Income Bracket = 
    switch(
        TRUE(),
        'Loan_default'[Income]<30000,"Low Income",
        'Loan_default'[Income]>=30000 && 'Loan_default'[Income]<60000,
        "Medium Income",
        'Loan_default'[Income]>=60000,"High Income")

Credit score Bins = 
 IF(Loan_default[CreditScore]<=400,"Very Low",
    IF('Loan_default'[CreditScore]<=450,"Low",
        IF('Loan_default'[CreditScore]<=650,"Medium",
        "High")))

Year = YEAR('Loan_default'[Loan_Date_DD_MM_YYYY].[Date])
```
### MEASURES TABLE1
```DAX

Loan Amount by Purpose = SUMX(FILTER('Loan_default',NOT(ISBLANK('Loan_default'
[LoanAmount]))),'Loan_default'[LoanAmount])

Average Income by Employment type = CALCULATE(AVERAGE('Loan_default'[Income]),ALLEXCEPT ('Loan_default','Loan_default'   [EmploymentType]))

Default Rate by Employment type = VAR totalrecords = COUNTROWS(ALL('Loan_default'))
 VAR DefaultCases = COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE()))

 RETURN

 CALCULATE(DIVIDE(DefaultCases,totalrecords),ALLEXCEPT('Loan_default','Loan_default'[EmploymentType])) *100

Average Loan by Age Group = AVERAGEX(VALUES('Loan_default'[Age Groups]),AVERAGE('Loan_default'[LoanAmount]))

Default Rate by Year = 
 VAR totalloans = 
        CALCULATE(COUNTROWS('Loan_default'),ALLEXCEPT('Loan_default','Loan_default'[Year]))

VAR default = CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),ALLEXCEPT('Loan_default',Loan_default[Year]))

RETURN

DIVIDE(default,totalloans)*100
```

### MEASURES TABLE2
```Dax 

Average Loan Amt (High Credit) = AVERAGEX(FILTER('Loan_default','Loan_default'[Credit score Bins]="High"),'loan_default'[LoanAmount])

Loan by Education type = COUNTROWS(FILTER('Loan_default',NOT(ISBLANK('Loan_default'[LoanID]))))

Median by Credit Score bins = MEDIANX('Loan_default','Loan_default'[LoanAmount])

Total Loan (Credit Bins) = CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Age Groups]="Adults",ALLEXCEPT('Loan_default','Loan_default'[Age Groups],'Loan_default'[CreditScore],'Loan_default'[Credit score Bins]))

Total Loan (Middle Age Adults) = 
CALCULATE(
    SUM('Loan_default'[LoanAmount]),
    'Loan_default'[Age Groups] = "Middle Age Adults"
)
```
### MEASURES Table3
```DAX
YOY Default Loans Change = DIVIDE(
    CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))) - 
    CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1)

    , CALCULATE(COUNTROWS(FILTER('Loan_default','Loan_default'[Default]=TRUE())),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),0) * 100

YOY Loan Amount Change = 
DIVIDE(
    CALCULATE(SUM('Loan_default'[LoanAmount]),
    'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))) - 
    CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1)

    ,CALCULATE(SUM('Loan_default'[LoanAmount]),'Loan_default'[Year]=YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))-1),0) * 100

YTD Loan Amount = 
CALCULATE(SUM('Loan_default'[LoanAmount]),DATESYTD('Loan_default'[Loan_Date_DD_MM_YYYY]),ALLEXCEPT('Loan_default',Loan_default[Credit score Bins],Loan_default[MaritalStatus]))

```

## Dashboard Pages, Insights, Deployment & Conclusion

### Dashboard Pages Explanation

#### 🔹 Page 1: Loan Default & Overview
- Loan amount by purpose  
- Average income by employment type  
- Default rate by employment type  
- Average loan by age group  
- Default rate by year  

**Focus:** Overall loan performance and default trends.
<img width="1633" height="885" alt="Image" src="https://github.com/user-attachments/assets/7a4a5d3b-080a-4855-948d-868c8f9adfd2" />

#### 🔹 Page 2: Applicant Demographics & Financial Profile
- Median loan amount by credit score  
- Loan distribution by age group and marital status  
- Loan amount by education type  
- Impact of mortgage and dependents on loan amount  

**Focus:** Customer characteristics and financial behavior.

#### 🔹 Page 3: Financial Risk Metrics
- Year-on-Year (YOY) loan amount change  
- Year-on-Year (YOY) default loan change  
- Year-to-Date (YTD) loan amount  
- Decomposition tree (Income → Employment Type)  

**Focus:** Risk monitoring and trend analysis.

### Key Insights Summary
- Home and Business loans dominate the total loan amount  
- Unemployed and low-credit-score customers show higher default risk  
- High credit score customers receive larger loans with lower defaults  
- Middle-age adults form the largest borrower segment  
- Default rates fluctuate year-over-year, highlighting economic impact  

### Deployment
- Report published to **Power BI Service**  
- Dataflow configured with:
  - Scheduled Refresh  
  - Incremental Refresh  

### Skills Demonstrated
- Power BI Desktop & Service  
- SQL Server integration  
- Power BI Dataflows & Gateway  
- Advanced DAX functions  
- Financial & Risk Analytics  
- End-to-end BI project implementation  

---

### Conclusion
This project demonstrates a **complete business intelligence solution** for loan default and financial risk analysis.  
It showcases strong skills in **data modeling, DAX, visualization, and analytical thinking**, making it suitable for **banking, finance, and data analyst roles**.
