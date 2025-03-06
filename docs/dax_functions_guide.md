# Comprehensive DAX Functions Guide

## Table of Contents
1. [Aggregation Functions](#aggregation-functions)
2. [Time Intelligence Functions](#time-intelligence-functions)
3. [Filter Functions](#filter-functions)
4. [Text Functions](#text-functions)
5. [Logical Functions](#logical-functions)
6. [Math Functions](#math-functions)
7. [Date and Time Functions](#date-and-time-functions)
8. [Context Transition Functions](#context-transition-functions)
9. [Table Manipulation Functions](#table-manipulation-functions)
10. [Information Functions](#information-functions)

## Aggregation Functions

### SUM
**Description**: Adds all numbers in a column.

**Syntax**:
```dax
SUM(<column>)
```

**Examples**:
```dax
// Basic sum of sales
Total Sales = SUM(Sales[Amount])

// Conditional sum
High Value Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    Sales[Amount] > 1000
)

// Running total
Running Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(
        ALL(Calendar),
        Calendar[Date] <= MAX(Calendar[Date])
    )
)
```

**Use Cases**:
1. Calculate total revenue
2. Sum up quantities
3. Compute running totals
4. Calculate year-to-date totals

### AVERAGE
**Description**: Calculates the arithmetic mean of a column.

**Syntax**:
```dax
AVERAGE(<column>)
```

**Examples**:
```dax
// Basic average
Average Price = AVERAGE(Products[UnitPrice])

// Moving average
Moving Avg Sales = 
CALCULATE(
    AVERAGE(Sales[Amount]),
    DATESINPERIOD(
        Calendar[Date],
        LASTDATE(Calendar[Date]),
        -7,
        DAY
    )
)

// Weighted average
Weighted Avg Price = 
DIVIDE(
    SUMX(
        Sales,
        Sales[Quantity] * Sales[UnitPrice]
    ),
    SUM(Sales[Quantity])
)
```

**Use Cases**:
1. Calculate average sales
2. Compute moving averages
3. Determine mean prices
4. Analyze performance metrics

## Time Intelligence Functions

### TOTALYTD
**Description**: Calculates year-to-date total.

**Syntax**:
```dax
TOTALYTD(<expression>, <dates>[, <filter>][, <year_end_date>])
```

**Examples**:
```dax
// Basic YTD Sales
YTD Sales = 
TOTALYTD(
    SUM(Sales[Amount]),
    Calendar[Date]
)

// YTD with custom fiscal year
Fiscal YTD Sales = 
TOTALYTD(
    SUM(Sales[Amount]),
    Calendar[Date],
    "6/30"
)

// YTD Growth
YTD Growth = 
VAR CurrentYTD = TOTALYTD(SUM(Sales[Amount]), Calendar[Date])
VAR PreviousYTD = 
    CALCULATE(
        TOTALYTD(SUM(Sales[Amount]), Calendar[Date]),
        SAMEPERIODLASTYEAR(Calendar[Date])
    )
RETURN
    DIVIDE(CurrentYTD - PreviousYTD, PreviousYTD)
```

**Use Cases**:
1. Year-to-date totals
2. Fiscal year calculations
3. Performance comparisons
4. Growth analysis

### DATEADD
**Description**: Returns a table with dates shifted by a specified interval.

**Syntax**:
```dax
DATEADD(<dates>, <number_of_intervals>, <interval>)
```

**Examples**:
```dax
// Previous month sales
Previous Month Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    DATEADD(Calendar[Date], -1, MONTH)
)

// Next quarter forecast
Next Quarter Forecast = 
CALCULATE(
    SUM(Sales[Amount]) * 1.1,
    DATEADD(Calendar[Date], 1, QUARTER)
)

// Year-over-year comparison
YoY Growth = 
VAR CurrentSales = SUM(Sales[Amount])
VAR PreviousYearSales = 
    CALCULATE(
        SUM(Sales[Amount]),
        DATEADD(Calendar[Date], -1, YEAR)
    )
RETURN
    DIVIDE(CurrentSales - PreviousYearSales, PreviousYearSales)
```

## Filter Functions

### CALCULATE
**Description**: Evaluates an expression in a modified filter context.

**Syntax**:
```dax
CALCULATE(<expression>[, <filter1>][, <filter2>]...)
```

**Examples**:
```dax
// Basic filter
US Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    Customer[Country] = "USA"
)

// Multiple filters
High Value US Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    Customer[Country] = "USA",
    Sales[Amount] > 1000
)

// Complex filtering
Premium Customer Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(
        Customer,
        Customer[TotalPurchases] > 10000 &&
        Customer[MembershipLevel] = "Premium"
    )
)
```

### FILTER
**Description**: Returns a table filtered based on specified conditions.

**Syntax**:
```dax
FILTER(<table>, <condition>)
```

**Examples**:
```dax
// Basic filtering
High Value Orders = 
CALCULATE(
    COUNT(Sales[OrderID]),
    FILTER(
        Sales,
        Sales[Amount] > 1000
    )
)

// Multiple conditions
Premium Products = 
CALCULATE(
    DISTINCTCOUNT(Products[ProductID]),
    FILTER(
        Products,
        Products[Category] = "Premium" &&
        Products[UnitPrice] > 100
    )
)

// Complex filtering with relationships
Top Customer Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(
        Customer,
        CALCULATE(
            SUM(Sales[Amount])
        ) > 50000
    )
)
```

## Text Functions

### CONCATENATE
**Description**: Joins two text strings into one text string.

**Syntax**:
```dax
CONCATENATE(<text1>, <text2>)
```

**Examples**:
```dax
// Basic concatenation
Full Name = 
CONCATENATE(
    Customer[FirstName],
    CONCATENATE(" ", Customer[LastName])
)

// Multiple concatenation with formatting
Full Address = 
CONCATENATE(
    Customer[StreetAddress],
    CONCATENATE(
        ", ",
        CONCATENATE(
            Customer[City],
            CONCATENATE(
                ", ",
                Customer[State]
            )
        )
    )
)

// Dynamic text generation
Product Description = 
CONCATENATE(
    Products[ProductName],
    CONCATENATE(
        " (",
        CONCATENATE(
            Products[Category],
            CONCATENATE(
                " - $",
                TEXT(Products[UnitPrice], "#,##0.00")
            )
        )
    )
)
```

## Logical Functions

### IF
**Description**: Returns one value if the condition is true and another value if it's false.

**Syntax**:
```dax
IF(<logical_test>, <value_if_true>[, <value_if_false>])
```

**Examples**:
```dax
// Basic IF
Status = IF(Sales[Amount] > 1000, "High", "Low")

// Nested IF
Performance Level = 
IF(
    Sales[Amount] > 5000,
    "Excellent",
    IF(
        Sales[Amount] > 3000,
        "Good",
        IF(
            Sales[Amount] > 1000,
            "Fair",
            "Needs Improvement"
        )
    )
)

// IF with calculations
Shipping Cost = 
IF(
    Sales[Weight] > 10,
    Sales[Weight] * 2.5,
    Sales[Weight] * 1.5
)
```

### SWITCH
**Description**: Evaluates an expression against multiple values and returns one of multiple possible result expressions.

**Syntax**:
```dax
SWITCH(<expression>, <value1>, <result1>[, <value2>, <result2>]...[, <else>])
```

**Examples**:
```dax
// Basic SWITCH
Region Category = 
SWITCH(
    Customer[Country],
    "USA", "North America",
    "Canada", "North America",
    "Mexico", "North America",
    "UK", "Europe",
    "France", "Europe",
    "Other"
)

// SWITCH with TRUE
Price Category = 
SWITCH(
    TRUE(),
    Products[Price] >= 1000, "Premium",
    Products[Price] >= 500, "High-End",
    Products[Price] >= 100, "Mid-Range",
    "Budget"
)

// Complex SWITCH
Shipping Method = 
SWITCH(
    TRUE(),
    AND(Sales[Weight] > 20, Sales[Priority] = "High"), "Express Heavy",
    AND(Sales[Weight] > 20, Sales[Priority] = "Normal"), "Standard Heavy",
    Sales[Priority] = "High", "Express",
    "Standard"
)
```

## Math Functions

### ROUND
**Description**: Rounds a number to a specified number of decimal places.

**Syntax**:
```dax
ROUND(<number>, <decimals>)
```

**Examples**:
```dax
// Basic rounding
Rounded Price = ROUND(Products[Price], 2)

// Rounding with calculation
Rounded Total = 
ROUND(
    Sales[Quantity] * Sales[UnitPrice] * (1 - Sales[Discount]),
    2
)

// Custom rounding for display
Display Amount = 
VAR Amount = Sales[Quantity] * Sales[UnitPrice]
RETURN
    SWITCH(
        TRUE(),
        Amount >= 1000000, ROUND(Amount / 1000000, 1) & "M",
        Amount >= 1000, ROUND(Amount / 1000, 1) & "K",
        ROUND(Amount, 2)
    )
```

### RANKX
**Description**: Returns the ranking of a number in a list of numbers.

**Syntax**:
```dax
RANKX(<table>, <expression>[, <value>[, <order>[, <ties>]]])
```

**Examples**:
```dax
// Basic ranking
Sales Rank = 
RANKX(
    ALL(Products),
    [Total Sales]
)

// Percentage ranking
Sales Percentile = 
DIVIDE(
    RANKX(
        ALL(Products),
        [Total Sales],
        ,
        ASC
    ),
    COUNTROWS(ALL(Products))
)

// Complex ranking with filters
Category Rank = 
RANKX(
    FILTER(
        ALL(Products),
        Products[Category] = EARLIER(Products[Category])
    ),
    [Total Sales]
)
```

## Date and Time Functions

### DATEDIFF
**Description**: Returns the count of interval boundaries crossed between two dates.

**Syntax**:
```dax
DATEDIFF(<start_date>, <end_date>, <interval>)
```

**Examples**:
```dax
// Basic date difference
Days Between = 
DATEDIFF(
    Sales[OrderDate],
    Sales[ShipDate],
    DAY
)

// Age calculation
Customer Age = 
DATEDIFF(
    Customer[BirthDate],
    TODAY(),
    YEAR
)

// Months in service
Service Duration = 
DATEDIFF(
    Employee[HireDate],
    IF(
        Employee[TerminationDate] = BLANK(),
        TODAY(),
        Employee[TerminationDate]
    ),
    MONTH
)
```

### CALENDAR
**Description**: Returns a table with a single column of dates.

**Syntax**:
```dax
CALENDAR(<start_date>, <end_date>)
```

**Examples**:
```dax
// Basic date table
Date Table = 
ADDCOLUMNS(
    CALENDAR(DATE(2020,1,1), DATE(2025,12,31)),
    "Year", YEAR([Date]),
    "Month", FORMAT([Date], "mmmm"),
    "Quarter", "Q" & ROUNDUP(MONTH([Date])/3, 0),
    "YearMonth", FORMAT([Date], "yyyy-mm")
)

// Fiscal year calendar
Fiscal Calendar = 
VAR FiscalYearStart = 7 // July
RETURN
ADDCOLUMNS(
    CALENDAR(DATE(2020,1,1), DATE(2025,12,31)),
    "Fiscal Year", 
        IF(
            MONTH([Date]) >= FiscalYearStart,
            YEAR([Date]) + 1,
            YEAR([Date])
        ),
    "Fiscal Month",
        MOD(MONTH([Date]) - FiscalYearStart + 12, 12) + 1
)
```

## Statistical Functions

### PERCENTILE.EXC
**Description**: Returns the k-th percentile of values in a column, where k is in the range 0 to 1 exclusive.

**Syntax**:
```dax
PERCENTILE.EXC(<column>, <k>)
```

**Examples**:
```dax
// 75th percentile of sales
Top Quarter Threshold = 
PERCENTILE.EXC(Sales[Amount], 0.75)

// Multiple percentiles
Sales Distribution = 
VAR Median = PERCENTILE.EXC(Sales[Amount], 0.5)
VAR Q1 = PERCENTILE.EXC(Sales[Amount], 0.25)
VAR Q3 = PERCENTILE.EXC(Sales[Amount], 0.75)
RETURN
    SWITCH(
        TRUE(),
        Sales[Amount] > Q3, "High",
        Sales[Amount] > Median, "Above Average",
        Sales[Amount] > Q1, "Below Average",
        "Low"
    )
```

### STDEVX.P
**Description**: Returns the standard deviation of a column.

**Syntax**:
```dax
STDEVX.P(<table>, <expression>)
```

**Examples**:
```dax
// Basic standard deviation
Sales StDev = 
STDEVX.P(
    Sales,
    Sales[Amount]
)

// Z-Score calculation
Sales Z-Score = 
VAR AvgSales = AVERAGE(Sales[Amount])
VAR StDev = STDEVX.P(Sales, Sales[Amount])
RETURN
    DIVIDE(
        Sales[Amount] - AvgSales,
        StDev
    )

// Anomaly detection
Is Anomaly = 
VAR ZScore = [Sales Z-Score]
RETURN
    IF(
        ABS(ZScore) > 2,
        "Anomaly",
        "Normal"
    )
```

## Advanced DAX Patterns

### Dynamic Period Comparisons
```dax
// Dynamic period comparison
Period Comparison = 
VAR SelectedPeriod = SELECTEDVALUE(Period[Type], "MTD")
VAR CurrentValue = [Total Sales]
VAR PreviousValue = 
    SWITCH(
        SelectedPeriod,
        "MTD", CALCULATE([Total Sales], DATEADD(Calendar[Date], -1, MONTH)),
        "QTD", CALCULATE([Total Sales], DATEADD(Calendar[Date], -1, QUARTER)),
        "YTD", CALCULATE([Total Sales], DATEADD(Calendar[Date], -1, YEAR))
    )
RETURN
    DIVIDE(
        CurrentValue - PreviousValue,
        PreviousValue
    )
```

### Cumulative Totals
```dax
// Cumulative total within groups
Cumulative Total = 
CALCULATE(
    [Total Sales],
    FILTER(
        ALL(Calendar[Date]),
        Calendar[Date] <= MAX(Calendar[Date])
    ),
    ALL(Calendar[Date])
)
```

### Parent-Child Hierarchies
```dax
// Parent-child hierarchy navigation
Path to Root = 
PATH(
    Employee[EmployeeID],
    Employee[ManagerID]
)

// Level calculation
Organization Level = 
PATHLENGTH(
    PATH(
        Employee[EmployeeID],
        Employee[ManagerID]
    )
)
```

## Error Handling and Debugging

### IFERROR
**Description**: Returns an alternative value when an error occurs.

**Syntax**:
```dax
IFERROR(<value>, <value_if_error>)
```

**Examples**:
```dax
// Basic error handling
Safe Division = 
IFERROR(
    DIVIDE(Sales[Amount], Sales[Quantity]),
    0
)

// Complex error handling
Safe Calculation = 
VAR Result = 
    TRY(
        DIVIDE(
            Sales[Amount],
            Sales[Quantity]
        )
    )
VAR ErrorMessage = "Invalid calculation"
RETURN
    IF(
        ISBLANK(Result),
        ErrorMessage,
        Result
    )
```

## Best Practices for DAX Functions

1. **Performance Optimization**
   - Use variables to store intermediate calculations
   - Avoid unnecessary context transitions
   - Minimize the use of CALCULATE when not needed
   - Use appropriate filter functions

2. **Code Readability**
   - Use clear and consistent naming conventions
   - Break complex calculations into smaller steps
   - Comment your code
   - Format your DAX code properly

3. **Error Handling**
   - Use IFERROR for error handling
   - Check for division by zero
   - Handle null values appropriately
   - Validate date ranges

4. **Testing and Debugging**
   - Test with sample data
   - Verify results with different filter contexts
   - Use DAX Studio for debugging
   - Document edge cases and assumptions

## Common DAX Patterns

### Rolling Calculations
```dax
// Rolling 3-month average
Rolling 3M Average = 
AVERAGEX(
    DATESINPERIOD(
        Calendar[Date],
        LASTDATE(Calendar[Date]),
        -3,
        MONTH
    ),
    [Daily Sales]
)
```

### Growth Calculations
```dax
// Year-over-year growth
YoY Growth = 
VAR CurrentValue = [Total Sales]
VAR PreviousValue = 
    CALCULATE(
        [Total Sales],
        SAMEPERIODLASTYEAR(Calendar[Date])
    )
RETURN
    DIVIDE(
        CurrentValue - PreviousValue,
        PreviousValue
    )
```

### Market Share
```dax
// Market share calculation
Market Share = 
DIVIDE(
    [Total Sales],
    CALCULATE(
        [Total Sales],
        ALL(Products)
    )
)
```

## Table Manipulation Functions

### SUMMARIZE
**Description**: Groups rows and calculates aggregations.

**Syntax**:
```dax
SUMMARIZE(<table>, <groupBy_columnName>[, <groupBy_columnName>]...[, <name>, <expression>]...)
```

**Examples**:
```dax
// Basic summarization
Sales by Category = 
SUMMARIZE(
    Sales,
    Products[Category],
    "Total Sales", SUM(Sales[Amount]),
    "Average Price", AVERAGE(Sales[UnitPrice])
)

// Multiple grouping levels
Sales by Region and Category = 
SUMMARIZE(
    Sales,
    Customer[Region],
    Products[Category],
    "Total Sales", SUM(Sales[Amount]),
    "Order Count", COUNTROWS(Sales)
)

// Complex summarization
Product Performance = 
SUMMARIZE(
    Sales,
    Products[Category],
    "Total Sales", SUM(Sales[Amount]),
    "YoY Growth", 
        VAR CurrentSales = SUM(Sales[Amount])
        VAR PrevSales = CALCULATE(
            SUM(Sales[Amount]),
            SAMEPERIODLASTYEAR(Calendar[Date])
        )
        RETURN DIVIDE(CurrentSales - PrevSales, PrevSales)
)
```

### ADDCOLUMNS
**Description**: Adds calculated columns to a table expression.

**Syntax**:
```dax
ADDCOLUMNS(<table>, <columnName>, <expression>[, <columnName>, <expression>]...)
```

**Examples**:
```dax
// Adding multiple calculated columns
Enhanced Products = 
ADDCOLUMNS(
    Products,
    "Profit Margin", Products[Price] - Products[Cost],
    "Margin Percentage", DIVIDE(Products[Price] - Products[Cost], Products[Price]),
    "Price Category", 
        SWITCH(
            TRUE(),
            Products[Price] >= 1000, "Premium",
            Products[Price] >= 500, "High-End",
            "Standard"
        )
)

// Date table enhancement
Enhanced Calendar = 
ADDCOLUMNS(
    CALENDAR(DATE(2020,1,1), DATE(2025,12,31)),
    "Year", YEAR([Date]),
    "Month", FORMAT([Date], "mmmm"),
    "Quarter", "Q" & ROUNDUP(MONTH([Date])/3, 0),
    "WeekDay", FORMAT([Date], "dddd"),
    "IsWeekend", IF(WEEKDAY([Date], 2) > 5, TRUE, FALSE),
    "WeekNumber", WEEKNUM([Date])
)
```

## Information Functions

### ISBLANK
**Description**: Checks if a value is blank.

**Syntax**:
```dax
ISBLANK(<value>)
```

**Examples**:
```dax
// Basic blank check
Has Address = NOT(ISBLANK(Customer[Address]))

// Complex blank handling
Contact Status = 
IF(
    ISBLANK(Customer[Email]) && ISBLANK(Customer[Phone]),
    "No Contact Info",
    IF(
        ISBLANK(Customer[Email]),
        "Phone Only",
        IF(
            ISBLANK(Customer[Phone]),
            "Email Only",
            "Complete"
        )
    )
)
```

### HASONEVALUE
**Description**: Returns TRUE when the context for a column has been filtered down to one distinct value.

**Syntax**:
```dax
HASONEVALUE(<columnName>)
```

**Examples**:
```dax
// Single selection check
Selected Category = 
IF(
    HASONEVALUE(Products[Category]),
    VALUES(Products[Category]),
    "Multiple Categories"
)

// Dynamic calculation
Dynamic Total = 
IF(
    HASONEVALUE(Calendar[Year]),
    [Year Total],
    IF(
        HASONEVALUE(Calendar[Quarter]),
        [Quarter Total],
        [Grand Total]
    )
)
```

## Performance Optimization

### Using Variables
Variables in DAX help improve performance and readability:

```dax
// Without variables
Poor Performance Measure = 
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

// With variables
Better Performance Measure = 
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

### Context Transition
Understanding context transition is crucial for performance:

```dax
// Avoid unnecessary context transition
Poor Performance = 
CALCULATE(
    AVERAGE(Sales[Amount]),
    FILTER(
        ALL(Sales),
        Sales[Amount] > 1000
    )
)

// Better performance
Better Performance = 
AVERAGEX(
    FILTER(
        Sales,
        Sales[Amount] > 1000
    ),
    Sales[Amount]
)
```

## Debugging Tips

1. **Step-by-Step Evaluation**
```dax
// Break down complex calculations
Complex Measure = 
VAR Step1 = [Base Calculation]
VAR Step2 = [Filtered Calculation]
VAR Step3 = DIVIDE(Step2, Step1)
RETURN
    IF(
        ISBLANK(Step3),
        0,
        Step3
    )
```

2. **Error Checking**
```dax
// Comprehensive error handling
Safe Complex Calculation = 
VAR BaseValue = [Base Calculation]
VAR FilteredValue = [Filtered Calculation]
VAR Division = 
    IFERROR(
        DIVIDE(FilteredValue, BaseValue),
        0
    )
VAR Result = 
    IF(
        Division > 1,
        1,
        Division
    )
RETURN
    IF(
        ISBLANK(Result),
        0,
        Result
    )
```

## Additional Resources

1. **Official Documentation**
   - [Microsoft DAX Reference](https://docs.microsoft.com/en-us/dax/)
   - [Power BI Documentation](https://docs.microsoft.com/en-us/power-bi/)

2. **Tools**
   - DAX Studio
   - Power BI Performance Analyzer
   - Tabular Editor

3. **Best Practices**
   - Use clear naming conventions
   - Document complex calculations
   - Test with various data scenarios
   - Consider performance implications
   - Regular code reviews
   - Version control for measures 