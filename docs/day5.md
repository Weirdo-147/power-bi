# Day 5: Enhancing the Data Model

## 5.1 Defining Hierarchies

Hierarchies allow users to drill down through levels of data:

![Hierarchies](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-create-and-manage-hierarchies/create-hierarchies_2.png)

### Common Hierarchy Examples
1. Date Hierarchy
   - Year
   - Quarter
   - Month
   - Day

2. Geographic Hierarchy
   - Country
   - Region
   - State
   - City

3. Product Hierarchy
   - Category
   - Subcategory
   - Product

### Creating Hierarchies
1. Select the base field
2. Right-click and choose "Create Hierarchy"
3. Add additional levels
4. Arrange levels in proper order
5. Name the hierarchy

## 5.2 Configuring Properties

Properties control how data is displayed and behaves:

### Field Properties
- Data Type
- Format
- Description
- Display Folder
- Hidden Status
- Sort By Column

![Field Properties](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-field-properties/field-properties-1.png)

### Table Properties
- Name
- Description
- Hidden Status
- Storage Mode

### Relationship Properties
- Cardinality
- Cross Filter Direction
- Security Filtering
- Make this relationship active

## 5.3 Defining New Measures

Measures are DAX calculations that aggregate data:

![Measures](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-measures/measures_1.png)

### Basic Measures
```dax
Total Sales = SUM(Sales[Amount])

Average Price = AVERAGE(Products[UnitPrice])

Customer Count = DISTINCTCOUNT(Sales[CustomerID])
```

### Time Intelligence Measures
```dax
YTD Sales = 
TOTALYTD(
    SUM(Sales[Amount]),
    'Date'[Date]
)

Previous Year Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    SAMEPERIODLASTYEAR('Date'[Date])
)
```

### Advanced Measures
```dax
// Running Total
Running Total = 
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(
        ALLSELECTED(Date),
        Date[Date] <= MAX(Date[Date])
    )
)

// Market Share
Market Share = 
DIVIDE(
    SUM(Sales[Amount]),
    CALCULATE(
        SUM(Sales[Amount]),
        ALL(Products)
    )
)
```

## 6.3 Filtering

### Filter Types

1. Basic Filters
   - Equals
   - Contains
   - Starts with
   - Is blank/not blank

2. Advanced Filters
   - Top N
   - Relative Date
   - Between
   - Custom filters

![Filtering](https://learn.microsoft.com/en-us/power-bi/create-reports/media/power-bi-report-filter/power-bi-filter-reading.png)

### Filter Contexts

1. Row Context
```dax
Previous Row Amount = 
    CALCULATE(
        SUM(Sales[Amount]),
        PREVIOUSROW(Sales)
    )
```

2. Filter Context
```dax
Sales in USA = 
    CALCULATE(
        SUM(Sales[Amount]),
        Customer[Country] = "USA"
    )
```

### Filter Functions
```dax
// ALL - Removes all filters
Total Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    ALL(Sales)
)

// ALLEXCEPT - Removes all filters except specified columns
Sales by Product = 
CALCULATE(
    SUM(Sales[Amount]),
    ALLEXCEPT(Sales, Sales[ProductID])
)

// FILTER - Creates custom filter
High Value Sales = 
CALCULATE(
    SUM(Sales[Amount]),
    FILTER(Sales, Sales[Amount] > 1000)
)
```

### Best Practices for Filtering
1. Use appropriate filter types
2. Consider performance impact
3. Document complex filters
4. Test filter combinations
5. Use bookmarks for filter states 