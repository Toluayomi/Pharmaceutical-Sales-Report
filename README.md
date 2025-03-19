# Sales Analysis 

## Table of Contents 
- [Project Overview](ProjectOverview)
- [Data Sources](DataSources)
- [Data Tools](DataTools)
- [Data Cleaning and Preparation](DataCleaningandPreparation)
- [Exploratory Data Analysis](ExploratoryDataAnalysis)
- [Data Analysis](Dataanalysis)
- [Results and Findings](DataResultsandFindings)
- [Recommendations](Recommendations)
- [Limitations](Limitations)
### Project Overview 
This repository contains a detailed pharmaceutical (Drugs) sales analysis, featuring key performance metrics, sales trends, regional distribution, and customer insights. Built using Power BI, and Excel, the report provides actionable insights to optimize sales strategies and drive business growth.

### Data Sources 
Sales Data: The primary dataset used for this analysis is the "drug_sales.csv" file, containing detailed information about drugs sales made by the pharmaceutical company 

### Data Tools 

- Excel - Data Cleaning
  - [Download here](
- Power BI - Data Analysis, reporting and Visualization
  - [Download here](https://app.powerbi.com/view?r=eyJrIjoiMGE4MDg5NzUtM2FmOS00NWM2LTgyMDQtODRiYTNlZTI3MDdmIiwidCI6ImZkYjZkMTI5LTk0NTItNDY0Mi05ZDRjLTY5YjViNmMzNjU2YSJ9)

### Data Cleaning and Preparation 

The data preparation stage involved performance of the following tasks;

1. Data loading and inspection
2. Handling of missing values
3. Data cleaning and formatting

### Exploratory Data Analysis (EDA)

EDA involved exploring the dataset to provide insights into the following business questions;

1. What are the overall sales metrics?
2. What are the Top & Under-performing drugs, and their contributions?
3. Who are the Top & Under performing customers, and their contributions?
4. What is the trend in the buyers analysis?
5. What are the trends in the customer demographics & regional sales distribution?
6. What is the yearly, quarterly and monthly revenue & transaction trends?
7. What is the weekdays drugs sales analysis?

### Data Analysis 

#### KPIs
```Power BI
Total Revenue = SUMX(
    FactTable,
    FactTable[UnitsSold] * RELATED(DrugLookup[UnitSalesPrice]))

Quantity Sold = SUM(FactTable[UnitsSold])

COGS = SUMX(
    FactTable,
    FactTable[UnitsSold] * RELATED(DrugLookup[CostOfProduction]))

Profit = [Total Revenue] - [COGS]

Pct Profit Margin = DIVIDE(
    [Profit], [Total Revenue])

Average Revenue/Customer = 
    DIVIDE(
    [Total Revenue], [#Customers]

#Transaction = COUNTROWS(FactTable)

#Customers = DISTINCTCOUNT(FactTable[CustomerID])
```

#### KPIs Title

```Power BI

Title: Revenue = IF(
    SELECTEDVALUE('Calendar Date'[Month]) <> BLANK(), "Revenue in " &
    SELECTEDVALUE('CalendarDate'[Month], "Total Revenue"))

Title: Quantity Sold = IF(
    SELECTEDVALUE('Calendar Date'[Month]) <> BLANK(), "Quantity Sold in " &
    SELECTEDVALUE('Calendar Date'[Month], "Total Quantity Sold"))
````

#### Previous Revenue

```Power BI
Previous Revenue = CALCULATE(
    [Total Revenue], DATEADD('Calendar Date'[Date], -1,MONTH))

Previous Quantity Sold = CALCULATE(
    [Quantity Sold], DATEADD('Calendar Date'[Date], -1,MONTH))

Previous Profit = CALCULATE(
    [Profit], DATEADD('Calendar Date'[Date], -1,MONTH))
```

#### KPIs Reference

```Power BI

Ref:Previous Revenue = 
  VAR _PreviousMonthView =
  CALCULATE(
    MAX('Calendar Date'[Month]), PREVIOUSMONTH('Calendar Date'[Date]))

VAR _Previous_Revenue = [Previous Revenue]

RETURN 
  IF(_PreviousMonthView <> BLANK(), _PreviousMonthView &" : "&
  FORMAT(_Previous_Revenue, "#,##"))

Ref:% Profit Margin = 
    VAR _PreviousMonthView =
    CALCULATE(
    MAX('Calendar Date'[Month]), PREVIOUSMONTH('Calendar Date'[Date]))

    VAR _PctPM = IF(
    SELECTEDVALUE('Calendar Date'[Month]) = BLANK() || SELECTEDVALUE('Calendar Date'[Month]) = "Jan",
    BLANK(), 
    CALCULATE([Pct Profit Margin], DATEADD('Calendar Date'[Date],-1,MONTH)))

    VAR _ARROW = IF(
    _PctPM >0, "▲+", "▼")

    RETURN IF(
        SELECTEDVALUE('Calendar Date'[Month]) = BLANK() ||
        SELECTEDVALUE('Calendar Date'[Month]) = "Jan", BLANK(),
        _PreviousMonthView&": "&_ARROW&FORMAT(_PctPM, "0.0%")
```
#### KPIs Variance

```Power BI

Ref:Pct Revenue = 
VAR _PctChange =
    DIVIDE([Total Revenue] - [Previous Revenue], [Previous Revenue])

VAR _ARROW = IF(
    _PctChange >0, "▲+", "▼")

RETURN
    IF(
        SELECTEDVALUE('Calendar Date'[Month]) = BLANK() ||
        SELECTEDVALUE('Calendar Date'[Month]) = "JAN",BLANK(), "Variance: "
        &_ARROW&FORMAT(_PctChange,  "0.0%"))

Ref:Pct Profit = 
VAR _PctChange =
    DIVIDE([Profit] - [Previous Profit], [Previous Profit])

VAR _ARROW = IF(
    _PctChange >0, "▲+", "▼")

RETURN
      IF(
        SELECTEDVALUE('Calendar Date'[Month]) = BLANK() ||
        SELECTEDVALUE('Calendar Date'[Month]) = "JAN",BLANK(), "Variance: "
        &_ARROW&FORMAT(_PctChange, "0.0%"))

Ref:Pct Quantity Sold = 
VAR _PctChange =
    DIVIDE([Quantity Sold] - [Previous Quantity Sold], [Previous Quantity Sold])

VAR _ARROW = IF(
    _PctChange >0, "▲+", "▼")

 RETURN
      IF(
        SELECTEDVALUE('Calendar Date'[Month]) = BLANK() ||
        SELECTEDVALUE('Calendar Date'[Month]) = "JAN",BLANK(), "Variance: "
        &_ARROW&FORMAT(_PctChange, "0.0%"))
```
  #### KPIs Variance Colour 

```Power BI
  VC Revenue = IF(
    DIVIDE([Total Revenue] - [Previous Revenue], 
    [Previous Revenue])>0,
    "#64f99b", "Red")

VC % PM = IF(
    CALCULATE([Pct Profit Margin], DATEADD('Calendar Date'[Date],-1,MONTH))
    >0, 
    "#64f99b", "Red")
```

#### Drugs Top and Bottom 

```Power BI

Drug Top = ---- calculating topn
VAR _TopDrugs = 
TOPN('Top/Bottom Drugs'[Top/Bottom Value], ALL(DrugLookup[DrugName]), [Hover-over Measure],DESC)

VAR _ActiveDrug = SELECTEDVALUE(DrugLookup[DrugName])

RETURN IF(
    _ActiveDrug IN _TopDrugs, [Hover-over Measure])


Drug Bottom = ---- calculating topn
VAR _TopDrugs = 
  TOPN('Top/Bottom Drugs'[Top/Bottom Value], ALL(DrugLookup[DrugName]), [Hover-over Measure], ASC)

VAR _ActiveDrug = SELECTEDVALUE(DrugLookup[DrugName])

RETURN IF(
    _ActiveDrug IN _TopDrugs, [Hover-over Measure])

Hover-over Measure = 
VAR _HoverMeasure = 
    SELECTEDVALUE('Active Measures'[Active Measures Order])
RETURN
    SWITCH(
    TRUE(),
    _HoverMeasure=0, [Total Revenue],
    _HoverMeasure=1,[#Transaction],
    _HoverMeasure=2, [Profit],[Quantity Sold])
```

#### Active Measures

```Power BI
Active Measures = {
    ("Revenue", NAMEOF('Key Measures'[Total Revenue]), 0),
    ("Transaction", NAMEOF('Key Measures'[#Transaction]), 1),
    ("Profit", NAMEOF('Key Measures'[Profit]), 2),
    ("Unit Sold", NAMEOF('Key Measures'[Quantity Sold]), 3)
```
#### Age Group

```Power BI
% Top2 Age group = 
     DIVIDE(
      SUMX(
     TOPN( 2, ALL(CustomerTable[AgeRange]), [Total Revenue], DESC),
    [Total Revenue]), [Total Revenue])

Top2 Age-group Name = 
    VAR _Top2Agegroup =
    TOPN( 2, ALL(CustomerTable[AgeRange]),[Total Revenue])

    VAR _Result =
                CONCATENATEX(_Top2Agegroup, CustomerTable[AgeRange], " and ",
                CustomerTable[AgeRange], DESC)

  RETURN _Result
```

#### Country

```Power BI
% Top2 Country = 
      DIVIDE(
      SUMX(
     TOPN( 2, ALL(CustomerTable[Country]), [Total Revenue], 
      DESC), 
      [Total Revenue]),
     [Total Revenue])
     
Top2 Country Name = 
    VAR _Top2Country =
        TOPN( 2, ALL(CustomerTable[Country]),[Total Revenue])

    VAR _Result =
                CONCATENATEX(_Top2Country, CustomerTable[Country], " and ",
                CustomerTable[Country], DESC)
 RETURN _Result
```

#### Dynamic Charts and Titles

``` Power BI

Value Top Drugs = 
VAR _TopDrugs = 
     TOPN('Top/Bottom Drugs'[Top/Bottom Value], ALL(DrugLookup[DrugName]),
            [Hover-over Measure],DESC)

VAR _ActiveDrug =
                SELECTEDVALUE(DrugLookup[DrugName], "ALL")

VAR _TotalDynamicTopN =
                SUMX(_TopDrugs,[Hover-over Measure])

RETURN
        IF(
            _ActiveDrug="ALL",_TotalDynamicTopN,
            IF(_ActiveDrug IN _TopDrugs, [Hover-over Measure], BLANK()
            ))

% Top Drugs = 
DIVIDE(
VAR _TopDrugs = 
            TOPN('Top/Bottom Drugs'[Top/Bottom Value], ALL(DrugLookup[DrugName]),
            [Hover-over Measure],DESC
            )

VAR _ActiveDrug =
                SELECTEDVALUE(DrugLookup[DrugName], "ALL")

VAR _TotalDynamicTopN =
                SUMX(_TopDrugs,[Hover-over Measure])

RETURN
        IF(
            _ActiveDrug="ALL",_TotalDynamicTopN,
            IF(_ActiveDrug IN _TopDrugs, [Hover-over Measure], BLANK()
            )
        ),
        [Hover-over Measure]


Caption:Drugs = 
        VAR _ActiveMeasures = SELECTEDVALUE('Active Measures'[Active Measures Order])

        VAR _MeasuresResult = 
            SWITCH(
                TRUE(),
                _ActiveMeasures=0, "(£) Revenue",
                _ActiveMeasures=1, "#Transaction",
                _ActiveMeasures=2, "(£) Profit",
                "#Unit Sold"
            )
        VAR _ActiveTopBottom = 
                    SELECTEDVALUE('Switch Drugs'[Switch Drugs Order])

        VAR _TopBottomSlide =
            SELECTEDVALUE('Top/Bottom Drugs'[Top/Bottom])

 RETURN IF(
      _ActiveTopBottom=1,FORMAT([% Bottom Drugs], "0.0%")& " of "&_MeasuresResult & "
      is from the Least Sold "&_TopBottomSlide&" Drugs",
 FORMAT([% Top Drugs], "0.0%")& " of "& _MeasuresResult & " is from the Top "&_TopBottomSlide&" Drugs")

Caption:Value Drugs = 
VAR _ActiveTopBottomDrugs =
            SELECTEDVALUE('Switch Drugs'[Switch Drugs Order])
    RETURN IF(
        _ActiveTopBottomDrugs=0,[Value Top Drugs], [Value Bottom Drugs])

Title:Drugs = 
        VAR _ActiveMeasures = SELECTEDVALUE('Active Measures'[Active Measures Order])

        VAR _MeasuresResult = 
            SWITCH(
                TRUE(),
                _ActiveMeasures=0, "(£) Revenue",
                _ActiveMeasures=1, "# Transaction",
                _ActiveMeasures=2, "(£) Profit",
                "# Quantity Sold"
        )

        VAR _TopBottom = 
                        SELECTEDVALUE('Top/Bottom Drugs'[Top/Bottom])

        VAR _ActiveTopBottom = 
                            SELECTEDVALUE('Switch Drugs'[Switch Drugs Order])

        VAR _Result =
            IF(_ActiveTopBottom=0, _TopBottom&" -Top Performing Drugs by "&_MeasuresResult,
            _TopBottom&" - Under Performing Drugs by "&_MeasuresResult
            )

        RETURN _Result
```

#### Gender 

```Power BI
Female = CALCULATE(
    [Total Revenue], CustomerTable[Gender] = "Female")

% Female = DIVIDE(
     [Female], [Total Revenue], 0)

% R:Female = 1.0 - [% Female]
```

#### Buyer Type

```Power BI

Seller Revenue = 
            CALCULATE([Total Revenue], 
            FactTable[BuyerType] = "Seller")

% User = DIVIDE(
    [User Revenue], [Total Revenue])
```
### Trend

Previous Month = 
            CALCULATE([Total Revenue],
            DATEADD('Calendar Date'[Date], -1,MONTH)
            )

MoM Change = 
        IF(
            SELECTEDVALUE('Calendar Date'[Month]) = "Jan", BLANK(),
        DIVIDE([Total Revenue] - [Previous Month], 
        [Previous Month], 0))

 CF MoM Change = 
        IF([MoM Change] >0,
        "#f0a787", "#AF1740"
        )
```

#### Tooltip Active

``` Power BI
Tooltip Active = 
            VAR _Country = SELECTEDVALUE(CustomerTable[Country])
            VAR _AgeGroup = SELECTEDVALUE(CustomerTable[AgeRange])

            RETURN
            IF(_Country=BLANK(), _AgeGroup, _Country)
```

### Results and Findings 

### Recommendations 

### Limitations 

### Reference 







