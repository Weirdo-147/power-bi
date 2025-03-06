# DAX Functions with Real-World Scenarios

## Aggregation Functions

### SUM
**Real-World Scenario**: 
A retail store needs to track total sales across different departments and time periods.

```dax
// Daily department sales
Daily Department Sales = 
SUM(Sales[Amount])

// Monthly sales target achievement
Sales Target Achievement = 
DIVIDE(
    SUM(Sales[Amount]),
    SUM(Targets[Target Amount])
)

// Running total of sales for the year
YTD Running Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(
        ALL(Calendar),
        Calendar[Date] <= MAX(Calendar[Date])
        && YEAR(Calendar[Date]) = YEAR(MAX(Calendar[Date]))
    )
)
```

### AVERAGE
**Real-World Scenario**: 
A restaurant chain wants to analyze average order values and customer spending patterns.

```dax
// Average order value
Avg Order Value = 
AVERAGE(Orders[Total Amount])

// 30-day moving average for daily sales
30 Day Moving Avg = 
CALCULATE(
    AVERAGE(Sales[Daily Total]),
    DATESINPERIOD(
        Calendar[Date],
        LASTDATE(Calendar[Date]),
        -30,
        DAY
    )
)

// Weighted average menu item rating
Menu Item Rating = 
DIVIDE(
    SUMX(
        Reviews,
        Reviews[Rating] * Reviews[Weight]
    ),
    SUM(Reviews[Weight])
)
```

## Time Intelligence Functions

### TOTALYTD
**Real-World Scenario**: 
A financial analyst needs to compare year-to-date performance against targets and previous years.

```dax
// YTD Sales vs Previous Year
YTD Performance = 
VAR CurrentYTD = 
    TOTALYTD(
        SUM(Sales[Amount]),
        Calendar[Date]
    )
VAR PreviousYTD = 
    CALCULATE(
        TOTALYTD(
            SUM(Sales[Amount]),
            Calendar[Date]
        ),
        SAMEPERIODLASTYEAR(Calendar[Date])
    )
VAR YoYGrowth = 
    DIVIDE(
        CurrentYTD - PreviousYTD,
        PreviousYTD
    )
RETURN
    IF(
        YoYGrowth >= 0.1,
        "Strong Growth",
        IF(
            YoYGrowth >= 0,
            "Moderate Growth",
            "Declining"
        )
    )
```

### DATEADD
**Real-World Scenario**: 
A subscription service needs to analyze customer retention and predict future renewals.

```dax
// Month-over-Month Subscriber Growth
Subscriber Growth = 
VAR CurrentSubscribers = 
    COUNTROWS(Subscribers)
VAR LastMonthSubscribers = 
    CALCULATE(
        COUNTROWS(Subscribers),
        DATEADD(Calendar[Date], -1, MONTH)
    )
RETURN
    DIVIDE(
        CurrentSubscribers - LastMonthSubscribers,
        LastMonthSubscribers
    )

// Predicted Churn Next Quarter
Predicted Churn = 
CALCULATE(
    [Churn Rate],
    DATEADD(Calendar[Date], 1, QUARTER)
)
```

## Filter Functions

### CALCULATE
**Real-World Scenario**: 
An e-commerce company wants to analyze sales performance across different customer segments and product categories.

```dax
// Premium customer sales in high-season
Premium Holiday Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    Customer[Segment] = "Premium",
    Calendar[IsHolidaySeason] = TRUE
)

// New customer acquisition cost
New Customer CAC = 
CALCULATE(
    DIVIDE(
        SUM(Marketing[Spend]),
        COUNTROWS(Customer)
    ),
    Customer[IsNew] = TRUE
)
```

### FILTER
**Real-World Scenario**: 
A manufacturing company needs to identify and analyze production line inefficiencies.

```dax
// Production lines with high defect rates
Problem Production Lines = 
CALCULATE(
    COUNTROWS(ProductionLine),
    FILTER(
        ProductionLine,
        ProductionLine[DefectRate] > 0.05 &&
        ProductionLine[OutputVolume] > 1000
    )
)

// High-value orders with delayed shipping
Delayed Premium Orders = 
CALCULATE(
    COUNT(Orders[OrderID]),
    FILTER(
        Orders,
        Orders[Amount] > 1000 &&
        Orders[ShipDate] > Orders[PromisedDate]
    )
)
```

## Text Functions

### CONCATENATE
**Real-World Scenario**: 
A healthcare provider needs to generate patient reports and appointment summaries.

```dax
// Patient full record
Patient Record = 
CONCATENATE(
    CONCATENATE(
        Patients[ID],
        " - "
    ),
    CONCATENATE(
        Patients[FullName],
        CONCATENATE(
            " (DOB: ",
            CONCATENATE(
                FORMAT(Patients[DateOfBirth], "mm/dd/yyyy"),
                ")"
            )
        )
    )
)

// Appointment summary
Appointment Details = 
CONCATENATE(
    FORMAT(Appointments[DateTime], "dddd, mmmm dd, yyyy hh:mm AM/PM"),
    CONCATENATE(
        " with Dr. ",
        CONCATENATE(
            Doctors[LastName],
            CONCATENATE(
                " - ",
                Appointments[Type]
            )
        )
    )
)
```

### REPLACE
**Real-World Scenario**: Text standardization
```dax
// Product code standardization
Standard Product Code = 
REPLACE(
    Products[SKU],
    1,
    2,
    "STD"
)

// Phone number formatting
Formatted Phone = 
REPLACE(
    REPLACE(
        Customer[Phone],
        1,
        0,
        "("
    ),
    5,
    0,
    ") "
)
```

### SEARCH
**Real-World Scenario**: Text pattern matching
```dax
// Product category extraction
Category Code = 
MID(
    Products[SKU],
    SEARCH("-", Products[SKU]) + 1,
    2
)

// Email domain identification
Email Domain = 
MID(
    Customer[Email],
    SEARCH("@", Customer[Email]) + 1,
    100
)
```

### LEN
**Real-World Scenario**: Data validation
```dax
// SKU length validation
Valid SKU = 
IF(
    LEN(Products[SKU]) = 10,
    "Valid",
    "Invalid Length"
)

// Password strength
Password Strength = 
SWITCH(
    TRUE(),
    LEN(Users[Password]) < 8, "Weak",
    LEN(Users[Password]) < 12, "Medium",
    "Strong"
)
```

### LEFT
**Real-World Scenario**: Code extraction
```dax
// Region code extraction
Region Code = 
LEFT(Location[PostalCode], 2)

// Product family identification
Product Family = 
LEFT(Products[SKU], 3)
```

### RIGHT
**Real-World Scenario**: Suffix extraction
```dax
// File extension identification
File Type = 
RIGHT(Documents[FileName], 3)

// Year extraction from code
Year Code = 
RIGHT(Products[BatchNumber], 4)
```

## Logical Functions

### IF
**Real-World Scenario**: 
A sales team needs to categorize customer behavior and set appropriate follow-up actions.

```dax
// Customer engagement status
Customer Status = 
IF(
    DATEDIFF(Customer[LastPurchase], TODAY(), DAY) <= 30,
    "Active",
    IF(
        DATEDIFF(Customer[LastPurchase], TODAY(), DAY) <= 90,
        "At Risk",
        IF(
            DATEDIFF(Customer[LastPurchase], TODAY(), DAY) <= 180,
            "Churning",
            "Inactive"
        )
    )
)

// Sales performance indicator
Sales Performance = 
IF(
    [Sales Achievement] >= 1.2,
    "Exceeding",
    IF(
        [Sales Achievement] >= 1,
        "Meeting Target",
        IF(
            [Sales Achievement] >= 0.8,
            "Near Target",
            "Needs Improvement"
        )
    )
)
```

## Math Functions

### ROUND
**Real-World Scenario**: 
A financial services company needs to present monetary values in reports with appropriate decimal places and handle currency conversions.

```dax
// Invoice amount rounding
Rounded Invoice Amount = 
ROUND(
    Sales[Amount] * Exchange[Rate],
    2
)

// Percentage metrics with proper display
Performance Score = 
VAR Score = 
    DIVIDE(
        Sales[Achieved],
        Sales[Target]
    )
RETURN
    SWITCH(
        TRUE(),
        Score >= 1, "100%",
        Score >= 0.1, FORMAT(ROUND(Score * 100, 1), "0.0%"),
        "< 10%"
    )
```

### RANKX
**Real-World Scenario**: 
A sales organization needs to identify top performers and analyze product performance across different categories.

```dax
// Sales representative ranking
Sales Rep Ranking = 
VAR CurrentRank = 
    RANKX(
        ALL(Employee),
        [Total Sales],
        ,
        DESC
    )
RETURN
    SWITCH(
        TRUE(),
        CurrentRank <= 3, "Top Performer",
        CurrentRank <= 10, "High Performer",
        CurrentRank <= 20, "Average Performer",
        "Needs Improvement"
    )

// Product performance by category
Product Category Rank = 
RANKX(
    FILTER(
        ALL(Products),
        Products[Category] = EARLIER(Products[Category])
    ),
    [Sales Amount],
    ,
    DESC,
    DENSE
)
```

## Statistical Functions

### PERCENTILE.EXC
**Real-World Scenario**: 
A human resources department needs to analyze salary distributions and identify compensation brackets.

```dax
// Salary band calculation
Salary Band = 
VAR MedianSalary = PERCENTILE.EXC(Employee[Salary], 0.5)
VAR Q1 = PERCENTILE.EXC(Employee[Salary], 0.25)
VAR Q3 = PERCENTILE.EXC(Employee[Salary], 0.75)
RETURN
    SWITCH(
        TRUE(),
        Employee[Salary] >= Q3, "Upper Band",
        Employee[Salary] >= MedianSalary, "Middle Band",
        Employee[Salary] >= Q1, "Lower Band",
        "Entry Level"
    )

// Performance distribution
Performance Percentile = 
VAR CurrentScore = Employee[PerformanceScore]
VAR Percentile = 
    PERCENTILE.EXC(
        ALLSELECTED(Employee[PerformanceScore]),
        0.75
    )
RETURN
    IF(
        CurrentScore >= Percentile,
        "Top Quartile",
        "Standard Performance"
    )
```

### STDEVX.P
**Real-World Scenario**: 
A quality control department needs to monitor production variations and identify anomalies.

```dax
// Quality control monitoring
Quality Variance = 
VAR Mean = AVERAGE(Production[MeasuredValue])
VAR StdDev = STDEVX.P(Production, Production[MeasuredValue])
VAR CurrentValue = Production[MeasuredValue]
VAR ZScore = DIVIDE(CurrentValue - Mean, StdDev)
RETURN
    SWITCH(
        TRUE(),
        ABS(ZScore) > 3, "Critical Variance",
        ABS(ZScore) > 2, "Significant Variance",
        ABS(ZScore) > 1, "Minor Variance",
        "Within Specs"
    )

// Sales anomaly detection
Sales Anomaly = 
VAR AvgSales = AVERAGE(Sales[DailyAmount])
VAR StdDev = STDEVX.P(Sales, Sales[DailyAmount])
RETURN
    IF(
        ABS(
            DIVIDE(
                Sales[DailyAmount] - AvgSales,
                StdDev
            )
        ) > 2,
        "Investigate",
        "Normal"
    )
```

## Date and Time Functions

### DATEDIFF
**Real-World Scenario**: 
A project management office needs to track project timelines and resource allocation.

```dax
// Project duration analysis
Project Timeline Status = 
VAR DaysElapsed = 
    DATEDIFF(
        Projects[StartDate],
        TODAY(),
        DAY
    )
VAR TotalDuration = 
    DATEDIFF(
        Projects[StartDate],
        Projects[PlannedEndDate],
        DAY
    )
VAR ProgressPercentage = 
    DIVIDE(
        DaysElapsed,
        TotalDuration
    )
RETURN
    SWITCH(
        TRUE(),
        ProgressPercentage > 1, "Overdue",
        ProgressPercentage > 0.9, "Critical",
        ProgressPercentage > 0.7, "Watch",
        "On Track"
    )

// Resource allocation
Resource Availability = 
VAR DaysUntilNext = 
    DATEDIFF(
        TODAY(),
        Resources[NextAvailableDate],
        DAY
    )
RETURN
    SWITCH(
        TRUE(),
        DaysUntilNext <= 0, "Available Now",
        DaysUntilNext <= 7, "Available This Week",
        DaysUntilNext <= 30, "Available This Month",
        "Long-term Committed"
    )
```

## Information Functions

### ISBLANK
**Real-World Scenario**: 
A customer service department needs to identify incomplete customer records and follow up on missing information.

```dax
// Contact information completeness
Contact Info Status = 
VAR HasEmail = NOT(ISBLANK(Customer[Email]))
VAR HasPhone = NOT(ISBLANK(Customer[Phone]))
VAR HasAddress = NOT(ISBLANK(Customer[Address]))
RETURN
    SWITCH(
        TRUE(),
        HasEmail && HasPhone && HasAddress, "Complete",
        HasEmail && HasPhone, "Basic Complete",
        HasEmail || HasPhone, "Minimal",
        "Incomplete"
    )

// Order validation
Order Validation = 
VAR HasProduct = NOT(ISBLANK(Orders[ProductID]))
VAR HasQuantity = NOT(ISBLANK(Orders[Quantity]))
VAR HasPrice = NOT(ISBLANK(Orders[UnitPrice]))
VAR HasShipping = NOT(ISBLANK(Orders[ShippingAddress]))
RETURN
    IF(
        HasProduct && HasQuantity && HasPrice && HasShipping,
        "Ready to Process",
        "Missing Information"
    )
```

## Advanced Pattern Scenarios

### Dynamic Segmentation
**Real-World Scenario**: 
A marketing team needs to create flexible customer segments based on multiple criteria.

```dax
// Dynamic customer segmentation
Customer Segment = 
VAR AvgOrderValue = [Customer Average Order]
VAR PurchaseFrequency = [Purchase Frequency]
VAR RecencyDays = [Days Since Last Purchase]
RETURN
    SWITCH(
        TRUE(),
        // High-value loyal customers
        AvgOrderValue >= 1000 && PurchaseFrequency >= 12 && RecencyDays <= 30,
            "Premium Loyal",
        // Regular high-value customers
        AvgOrderValue >= 1000 && PurchaseFrequency >= 6,
            "Premium Regular",
        // Active medium-value customers
        AvgOrderValue >= 500 && RecencyDays <= 60,
            "Active Medium",
        // At-risk customers
        RecencyDays > 90 && PurchaseFrequency >= 6,
            "At Risk Loyal",
        // Others
        "Standard"
    )
```

### Comparative Analysis
**Real-World Scenario**: 
A business analyst needs to compare performance across different dimensions and time periods.

```dax
// Multi-dimensional performance analysis
Performance Matrix = 
VAR CurrentMetric = [Selected Metric]
VAR PreviousPeriod = [Previous Period Metric]
VAR BenchmarkValue = [Benchmark]
VAR YoYGrowth = DIVIDE(CurrentMetric - PreviousPeriod, PreviousPeriod)
VAR BenchmarkDiff = DIVIDE(CurrentMetric - BenchmarkValue, BenchmarkValue)
RETURN
    SWITCH(
        TRUE(),
        YoYGrowth > 0.1 && BenchmarkDiff > 0,
            "Strong Performance",
        YoYGrowth > 0 && BenchmarkDiff > -0.1,
            "Stable Performance",
        YoYGrowth < -0.1 || BenchmarkDiff < -0.2,
            "Needs Attention",
        "Monitor"
    )
```

## Best Practices with Scenarios

### Performance Optimization Scenario
**Real-World Scenario**: 
A large retail chain needs to analyze millions of transactions across thousands of stores.

```dax
// Poor performance - multiple calculations of the same value
Poor Sales Analysis = 
DIVIDE(
    CALCULATE(
        SUM(Sales[Amount]),
        FILTER(
            ALL(Calendar),
            Calendar[Year] = YEAR(TODAY())
        )
    ),
    CALCULATE(
        SUM(Sales[Amount]),
        FILTER(
            ALL(Calendar),
            Calendar[Year] = YEAR(TODAY()) - 1
        )
    )
)

// Optimized version using variables
Optimized Sales Analysis = 
VAR CurrentYear = YEAR(TODAY())
VAR CurrentYearSales = 
    CALCULATE(
        SUM(Sales[Amount]),
        FILTER(
            ALL(Calendar),
            Calendar[Year] = CurrentYear
        )
    )
VAR PreviousYearSales = 
    CALCULATE(
        SUM(Sales[Amount]),
        FILTER(
            ALL(Calendar),
            Calendar[Year] = CurrentYear - 1
        )
    )
RETURN
    DIVIDE(CurrentYearSales, PreviousYearSales)
```

### Error Handling Scenario
**Real-World Scenario**: 
A financial system needs to handle division operations with potential zero denominators.

```dax
// Safe division with error handling
Safe Profit Margin = 
VAR Revenue = SUM(Sales[Amount])
VAR Cost = SUM(Sales[Cost])
VAR ProfitMargin = 
    IFERROR(
        DIVIDE(
            Revenue - Cost,
            Revenue
        ),
        BLANK()
    )
RETURN
    IF(
        ISBLANK(ProfitMargin),
        "No Sales Data",
        FORMAT(ProfitMargin, "0.00%")
    )
```

## Advanced Patterns with Scenarios

### Dynamic Time Comparison Scenario
**Real-World Scenario**: 
A business intelligence dashboard needs to allow users to switch between different time period comparisons.

```dax
// Dynamic time comparison
Period Over Period = 
VAR SelectedPeriod = SELECTEDVALUE(TimePeriod[Type], "MTD")
VAR SelectedMetric = SELECTEDVALUE(Metrics[Name], "Sales")
VAR CurrentValue = [Selected Metric Value]
VAR PriorValue = 
    SWITCH(
        SelectedPeriod,
        "MTD", CALCULATE([Selected Metric Value], DATEADD(Calendar[Date], -1, MONTH)),
        "QTD", CALCULATE([Selected Metric Value], DATEADD(Calendar[Date], -1, QUARTER)),
        "YTD", CALCULATE([Selected Metric Value], DATEADD(Calendar[Date], -1, YEAR))
    )
RETURN
    IF(
        ISBLANK(PriorValue),
        BLANK(),
        DIVIDE(CurrentValue - PriorValue, PriorValue)
    )
```

# Additional DAX Functions by Category

## Additional Aggregation Functions

### 1. COUNTROWS
**Real-World Scenario**: Retail transaction analysis
```dax
// Count daily transactions
Daily Transaction Count = 
COUNTROWS(Sales)

// Stores with high transaction volume
High Volume Stores = 
CALCULATE(
    COUNTROWS(Sales),
    Sales[TransactionCount] > 1000
)
```

### 2. DISTINCTCOUNT
**Real-World Scenario**: Customer analytics
```dax
// Unique customers per month
Monthly Unique Customers = 
DISTINCTCOUNT(Sales[CustomerID])

// Product categories shopped
Customer Shopping Breadth = 
DISTINCTCOUNT(Sales[ProductCategory])
```

### 3. SUMX
**Real-World Scenario**: Invoice calculations
```dax
// Total with tax
Invoice Total = 
SUMX(
    Sales,
    Sales[Amount] * (1 + Sales[TaxRate])
)

// Weighted score calculation
Weighted Score = 
SUMX(
    Evaluations,
    Evaluations[Score] * Evaluations[Weight]
)
```

### 4. AVERAGEX
**Real-World Scenario**: Performance metrics
```dax
// Average order value per customer
Customer AOV = 
AVERAGEX(
    SUMMARIZE(Sales, Sales[CustomerID]),
    [Total Sales]
)

// Average daily sales by store
Store Daily Average = 
AVERAGEX(
    VALUES(Calendar[Date]),
    [Daily Sales]
)
```

### 5. MINX
**Real-World Scenario**: Inventory management
```dax
// Lowest stock level
Minimum Stock Level = 
MINX(
    Inventory,
    Inventory[Quantity] - Inventory[SafetyStock]
)

// Earliest reorder date
Next Reorder Date = 
MINX(
    FILTER(
        Inventory,
        Inventory[Quantity] < Inventory[ReorderPoint]
    ),
    Inventory[ProjectedStockoutDate]
)
```

### 6. MAXX
**Real-World Scenario**: Sales performance
```dax
// Best performing product
Top Product Revenue = 
MAXX(
    Products,
    [Product Sales]
)

// Peak hour identification
Peak Hour = 
MAXX(
    SUMMARIZE(Sales, Sales[Hour], "Transactions", COUNTROWS(Sales)),
    [Transactions]
)
```

### 7. COUNTX
**Real-World Scenario**: Activity tracking
```dax
// Customer interactions per month
Monthly Customer Touches = 
COUNTX(
    FILTER(
        Activities,
        Activities[Type] IN {"Call", "Email", "Meeting"}
    ),
    Activities[ActivityID]
)

// Products with stock alerts
Stock Alert Count = 
COUNTX(
    Products,
    IF(Products[Stock] < Products[MinimumLevel], 1, 0)
)
```

### 8. MEDIANX
**Real-World Scenario**: Price analysis
```dax
// Median order value by category
Category Median Order = 
MEDIANX(
    SUMMARIZE(Sales, Sales[Category]),
    [Order Amount]
)

// Median processing time
Median Processing Days = 
MEDIANX(
    Orders,
    DATEDIFF(Orders[OrderDate], Orders[ShipDate], DAY)
)
```

### 9. PRODUCTX
**Real-World Scenario**: Compound growth calculations
```dax
// Compound growth rate
Growth Factor = 
PRODUCTX(
    Growth,
    1 + Growth[Rate]
)

// Probability chain
Success Probability = 
PRODUCTX(
    Steps,
    Steps[SuccessRate]
)
```

### 10. GEOMEANX
**Real-World Scenario**: Investment performance
```dax
// Portfolio growth rate
Portfolio CAGR = 
GEOMEANX(
    Returns,
    1 + Returns[YearlyReturn]
) - 1

// Quality score average
Geometric Quality Score = 
GEOMEANX(
    QualityMetrics,
    QualityMetrics[Score]
)
```

## Additional Time Intelligence Functions

### 1. SAMEPERIODLASTYEAR
**Real-World Scenario**: Year-over-year analysis
```dax
// Last year comparison
YoY Growth = 
VAR CurrentSales = [Total Sales]
VAR LastYearSales = 
    CALCULATE(
        [Total Sales],
        SAMEPERIODLASTYEAR(Calendar[Date])
    )
RETURN
    DIVIDE(CurrentSales - LastYearSales, LastYearSales)
```

### 2. DATESBETWEEN
**Real-World Scenario**: Period performance
```dax
// Sales in date range
Period Sales = 
CALCULATE(
    [Total Sales],
    DATESBETWEEN(
        Calendar[Date],
        [Start Date],
        [End Date]
    )
)
```

### 3. PARALLELPERIOD
**Real-World Scenario**: Comparative period analysis
```dax
// Previous quarter comparison
Previous Quarter Sales = 
CALCULATE(
    [Total Sales],
    PARALLELPERIOD(Calendar[Date], -1, QUARTER)
)

// Same month last year
Last Year Same Month = 
CALCULATE(
    [Total Sales],
    PARALLELPERIOD(Calendar[Date], -12, MONTH)
)
```

### 4. FIRSTDATE
**Real-World Scenario**: Period start metrics
```dax
// Month start inventory
Month Start Stock = 
CALCULATE(
    [Total Stock],
    FIRSTDATE(Calendar[Date])
)

// Quarter opening balance
Quarter Opening Balance = 
CALCULATE(
    [Account Balance],
    FIRSTDATE(Calendar[QuarterStart])
)
```

### 5. LASTDATE
**Real-World Scenario**: Period end analysis
```dax
// Month end balance
Month End Balance = 
CALCULATE(
    [Account Balance],
    LASTDATE(Calendar[Date])
)

// Quarter closing metrics
Quarter End Metrics = 
CALCULATE(
    [Key Metrics],
    LASTDATE(Calendar[QuarterEnd])
)
```

### 6. DATESINPERIOD
**Real-World Scenario**: Rolling period analysis
```dax
// Rolling 90-day revenue
Rolling 90 Day Revenue = 
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(
        Calendar[Date],
        LASTDATE(Calendar[Date]),
        -90,
        DAY
    )
)

// Last 6 months average
Six Month Average = 
AVERAGEX(
    DATESINPERIOD(
        Calendar[Date],
        LASTDATE(Calendar[Date]),
        -6,
        MONTH
    ),
    [Daily Sales]
)
```

### 7. NEXTDAY
**Real-World Scenario**: Forward-looking analysis
```dax
// Next day forecast
Tomorrow's Forecast = 
CALCULATE(
    [Sales Forecast],
    NEXTDAY(Calendar[Date])
)

// Next business day schedule
Next Business Day Tasks = 
CALCULATE(
    [Scheduled Tasks],
    NEXTDAY(Calendar[Date])
)
```

### 8. PREVIOUSDAY
**Real-World Scenario**: Historical comparison
```dax
// Previous day comparison
Day Over Day Change = 
VAR Today = [Daily Sales]
VAR Yesterday = 
    CALCULATE(
        [Daily Sales],
        PREVIOUSDAY(Calendar[Date])
    )
RETURN
    DIVIDE(Today - Yesterday, Yesterday)

// Prior day balance
Previous Day Balance = 
CALCULATE(
    [Account Balance],
    PREVIOUSDAY(Calendar[Date])
)
```

### 9. STARTOFMONTH
**Real-World Scenario**: Monthly tracking
```dax
// Month-to-date progress
MTD Progress = 
CALCULATE(
    [Current Value],
    DATESINPERIOD(
        Calendar[Date],
        STARTOFMONTH(Calendar[Date]),
        1,
        MONTH
    )
)

// Monthly opening inventory
Monthly Opening Stock = 
CALCULATE(
    [Stock Level],
    STARTOFMONTH(Calendar[Date])
)
```

### 10. ENDOFMONTH
**Real-World Scenario**: Month-end reporting
```dax
// Month-end projections
Projected Month End = 
CALCULATE(
    [Forecast Value],
    ENDOFMONTH(Calendar[Date])
)

// Month closing metrics
Month End KPIs = 
CALCULATE(
    [KPI Dashboard],
    ENDOFMONTH(Calendar[Date])
)
```

## Additional Filter Functions

### 1. ALLEXCEPT
**Real-World Scenario**: Market analysis
```dax
// Market share by product
Product Market Share = 
DIVIDE(
    [Sales Amount],
    CALCULATE(
        [Sales Amount],
        ALLEXCEPT(Products, Products[Category])
    )
)
```

### 2. CROSSFILTER
**Real-World Scenario**: Multi-directional analysis
```dax
// Bi-directional sales analysis
Cross Department Analysis = 
CALCULATE(
    [Total Sales],
    CROSSFILTER(
        Sales[DepartmentID],
        Department[ID],
        BOTH
    )
)

// Customer-Product relationship
Product Affinity = 
CALCULATE(
    [Purchase Frequency],
    CROSSFILTER(
        Customers[ProductID],
        Products[ID],
        BOTH
    )
)
```

### 3. USERELATIONSHIP
**Real-World Scenario**: Alternative relationship analysis
```dax
// Secondary market analysis
Alternative Market View = 
CALCULATE(
    [Market Share],
    USERELATIONSHIP(
        Products[SecondaryMarketID],
        Markets[ID]
    )
)

// Substitute supplier analysis
Backup Supplier Performance = 
CALCULATE(
    [Supply Chain Metrics],
    USERELATIONSHIP(
        Products[BackupSupplierID],
        Suppliers[ID]
    )
)
```

### 4. REMOVEFILTERS
**Real-World Scenario**: Global comparison
```dax
// Global market share
Total Market Share = 
DIVIDE(
    [Sales Amount],
    CALCULATE(
        [Sales Amount],
        REMOVEFILTERS()
    )
)

// Overall performance
Company Wide Metrics = 
CALCULATE(
    [Performance Metrics],
    REMOVEFILTERS(Department)
)
```

### 5. KEEPFILTERS
**Real-World Scenario**: Preserved context analysis
```dax
// Preserved segment analysis
Segment Performance = 
CALCULATE(
    [Segment Metrics],
    KEEPFILTERS(
        Customer[Segment] = "Premium"
    )
)

// Maintained filter context
Regional Comparison = 
CALCULATE(
    [Regional Performance],
    KEEPFILTERS(
        Territory[Region] IN {"North", "South"}
    )
)
```

## Additional Math Functions

### 1. ABS
**Real-World Scenario**: Variance analysis
```dax
// Absolute variance
Performance Gap = 
ABS([Target] - [Actual])

// Price difference
Price Variance = 
ABS(
    Products[ListPrice] - 
    Products[ActualPrice]
)
```

### 2. CEILING
**Real-World Scenario**: Price rounding
```dax
// Price point rounding
Marketing Price = 
CEILING(
    Products[Cost] * 1.4,
    0.99
)

// Quantity rounding
Order Quantity = 
CEILING(
    [Required Amount],
    [Package Size]
)
```

### 3. FLOOR
**Real-World Scenario**: Conservative estimates
```dax
// Minimum stock level
Safe Stock Level = 
FLOOR(
    [Average Daily Usage] * 1.2,
    5
)

// Conservative revenue
Guaranteed Revenue = 
FLOOR(
    [Projected Revenue],
    1000
)
```

### 4. MOD
**Real-World Scenario**: Cycle analysis
```dax
// Week number in month
Week In Month = 
MOD(
    WEEKNUM(Calendar[Date]),
    4
) + 1

// Rotation schedule
Shift Rotation = 
MOD(
    DATEDIFF(
        Calendar[StartDate],
        Calendar[Date],
        DAY
    ),
    3
) + 1
```

### 5. POWER
**Real-World Scenario**: Compound calculations
```dax
// Compound interest
Future Value = 
[Principal] * 
POWER(
    1 + [Interest Rate],
    [Years]
)

// Growth projection
Market Size = 
[Current Market] *
POWER(
    1 + [Growth Rate],
    [Projection Years]
)
```

## Additional Statistical Functions

### 6. RANK.EQ
**Real-World Scenario**: Performance ranking
```dax
// Sales performance ranking
Sales Rank = 
RANK.EQ(
    [Total Sales],
    VALUES(Employee[Total Sales])
)

// Product popularity ranking
Product Rank = 
RANK.EQ(
    [Units Sold],
    VALUES(Products[Units Sold])
)
```

### 7. STDDEV.P
**Real-World Scenario**: Process control
```dax
// Process stability measure
Process Stability = 
STDDEV.P(Production[Measurement])

// Sales variability
Sales Variation = 
STDDEV.P(
    SUMMARIZE(
        Sales,
        Sales[Date],
        "Daily Sales", SUM(Sales[Amount])
    )
)
```

### 8. VAR.P
**Real-World Scenario**: Risk assessment
```dax
// Investment risk measure
Portfolio Variance = 
VAR.P(Investments[Returns])

// Demand variability
Demand Volatility = 
VAR.P(
    SUMMARIZE(
        Sales,
        Sales[ProductID],
        "Daily Demand", SUM(Sales[Quantity])
    )
)
```

### 9. CORRELATION
**Real-World Scenario**: Relationship strength analysis
```dax
// Price-demand correlation
Price Impact = 
CORRELATION(
    Products[Price],
    Sales[Quantity]
)

// Marketing effectiveness
Campaign Correlation = 
CORRELATION(
    Marketing[Spend],
    Sales[Revenue]
)
```

### 10. GEOMEAN
**Real-World Scenario**: Growth rate analysis
```dax
// Average growth rate
Growth Rate = 
GEOMEAN(
    ADDCOLUMNS(
        VALUES(Calendar[Year]),
        "YearlyGrowth",
        [Year Over Year Growth]
    )
)

// Product price increase
Price Growth = 
GEOMEAN(
    ADDCOLUMNS(
        VALUES(Products[Version]),
        "PriceChange",
        [Price Change Rate]
    )
)
```

## Additional Table Manipulation Functions

### 1. GROUPBY
**Real-World Scenario**: Custom aggregations
```dax
// Sales by category and region
Category Region Sales = 
GROUPBY(
    Sales,
    Products[Category],
    Territory[Region],
    "Total Sales", SUM(Sales[Amount]),
    "Order Count", COUNTROWS(Sales)
)

// Customer segments
Customer Segments = 
GROUPBY(
    Customers,
    Customer[Type],
    Customer[Region],
    "Average Spend", AVERAGE(Sales[Amount]),
    "Customer Count", COUNTROWS(Customers)
)
```

### 2. GENERATE
**Real-World Scenario**: Cross-table analysis
```dax
// Product-customer combinations
Product Customer Matrix = 
GENERATE(
    VALUES(Products[Category]),
    CALCULATETABLE(
        SUMMARIZE(
            Sales,
            Customer[Segment],
            "Sales", SUM(Sales[Amount])
        )
    )
)

// Time-territory analysis
Territory Time Analysis = 
GENERATE(
    VALUES(Territory[Region]),
    CALCULATETABLE(
        SUMMARIZE(
            Sales,
            Calendar[Month],
            "Monthly Sales", SUM(Sales[Amount])
        )
    )
)
```

### 3. CROSSJOIN
**Real-World Scenario**: Multi-dimensional analysis
```dax
// Product-region-time matrix
Analysis Matrix = 
CROSSJOIN(
    VALUES(Products[Category]),
    CROSSJOIN(
        VALUES(Territory[Region]),
        VALUES(Calendar[Quarter])
    )
)

// Customer-product combinations
Market Coverage = 
CROSSJOIN(
    VALUES(Customer[Segment]),
    VALUES(Products[Category])
)
```

### 4. UNION
**Real-World Scenario**: Combined data analysis
```dax
// Combined territories
All Territories = 
UNION(
    SELECTCOLUMNS(
        NorthRegion,
        "Territory", Territory[Name],
        "Sales", [Sales Amount]
    ),
    SELECTCOLUMNS(
        SouthRegion,
        "Territory", Territory[Name],
        "Sales", [Sales Amount]
    )
)

// Merged product lists
Complete Product List = 
UNION(
    CurrentProducts,
    DiscontinuedProducts
)
```

### 5. EXCEPT
**Real-World Scenario**: Exclusion analysis
```dax
// Active customers
Active Customers = 
EXCEPT(
    ALL(Customers),
    FILTER(
        Customers,
        Customers[Status] = "Inactive"
    )
)

// Available products
Available Products = 
EXCEPT(
    ALL(Products),
    FILTER(
        Products,
        Products[Stock] = 0
    )
)
```

### 6. INTERSECT
**Real-World Scenario**: Common elements analysis
```dax
// Premium customers with high activity
Premium Active = 
INTERSECT(
    FILTER(
        Customers,
        Customers[Segment] = "Premium"
    ),
    FILTER(
        Customers,
        Customers[ActivityScore] > 80
    )
)

// High-margin popular products
Strategic Products = 
INTERSECT(
    FILTER(
        Products,
        Products[Margin] > 0.4
    ),
    FILTER(
        Products,
        Products[SalesRank] <= 100
    )
)
```

### 7. NATURALINNERJOIN
**Real-World Scenario**: Matching records analysis
```dax
// Matched orders and shipments
Complete Orders = 
NATURALINNERJOIN(
    Orders,
    Shipments
)

// Product sales details
Product Sales Details = 
NATURALINNERJOIN(
    Products,
    Sales
)
```

### 8. NATURALLEFTOUTERJOIN
**Real-World Scenario**: Inclusive matching
```dax
// All customers with orders
Customer Orders = 
NATURALLEFTOUTERJOIN(
    Customers,
    Orders
)

// Products with optional reviews
Product Reviews = 
NATURALLEFTOUTERJOIN(
    Products,
    Reviews
)
```

### 9. SELECTCOLUMNS
**Real-World Scenario**: Custom table creation
```dax
// Customer summary
Customer Summary = 
SELECTCOLUMNS(
    Customers,
    "Customer", Customers[Name],
    "Total Spend", [Customer Total Sales],
    "Last Purchase", [Last Purchase Date],
    "Status", [Customer Status]
)

// Product catalog
Product Catalog = 
SELECTCOLUMNS(
    Products,
    "SKU", Products[ProductCode],
    "Name", Products[ProductName],
    "Price", [Current Price],
    "Stock", [Available Stock]
)
```

### 10. SUMMARIZECOLUMNS
**Real-World Scenario**: Multi-level aggregation
```dax
// Sales dashboard
Sales Dashboard = 
SUMMARIZECOLUMNS(
    Calendar[Year],
    Calendar[Quarter],
    Products[Category],
    "Sales", SUM(Sales[Amount]),
    "Orders", COUNTROWS(Sales),
    "Avg Order", [Average Order Value]
)

// Performance metrics
Performance Summary = 
SUMMARIZECOLUMNS(
    Employee[Department],
    Territory[Region],
    "Revenue", SUM(Sales[Amount]),
    "Target", SUM(Targets[Amount]),
    "Achievement", [Achievement Percentage]
)
```

These real-world scenarios help illustrate practical applications of DAX functions in business contexts. Each scenario includes:
1. A clear business problem
2. The solution using DAX
3. Variations for different use cases
4. Best practices and considerations

Would you like me to:
1. Add more specific business scenarios?
2. Include additional industry-specific examples?
3. Add more complex scenario combinations?
4. Expand any particular scenario type? 