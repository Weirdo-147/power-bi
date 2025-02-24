# Day 4: Modeling Data for Analysis

## 4.1 Creating Relationships

Relationships connect tables in your data model:

![Relationship View](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-create-and-manage-relationships/create-manage-relationships_2.png)

### Types of Relationships
1. One-to-Many (Most common)
2. One-to-One
3. Many-to-Many (Using bridge tables)
4. Bi-directional

### Best Practices
- Use meaningful primary keys
- Ensure data integrity
- Avoid circular relationships
- Consider performance impact
- Document relationship logic

### Creating Relationships Steps
1. Go to Model view
2. Drag field from one table to another
3. Configure relationship properties
4. Test the relationship
5. Validate cross-filtering

## 4.2 Defining New Columns

Calculated columns are created using DAX (Data Analysis Expressions):

![Calculated Columns](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-calculated-columns/calculated-columns_1.png)

### Common Column Calculations
```dax
// Concatenate strings
Full Name = CONCATENATE(FirstName, " ", LastName)

// Date calculations
Year = YEAR([Date])

// Conditional columns
Category = IF([Sales] > 1000, "High", "Low")
```

### Best Practices
1. Use clear naming conventions
2. Document complex calculations
3. Consider performance impact
4. Test with sample data
5. Validate results

## 4.3 Performing a Look up to a Related Table

Lookup operations retrieve values from related tables:

![Related Tables](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-many-to-many-relationships/many-to-many-relationships_1.png)

### RELATED Function
```dax
// Simple lookup
Product Category = RELATED(Products[Category])

// Nested lookup
Supplier Country = RELATED(Products[SupplierID[Country]])
```

### LOOKUPVALUE Function
```dax
// Lookup with multiple conditions
Price = LOOKUPVALUE(
    PriceList[Price],
    PriceList[ProductID], Products[ProductID],
    PriceList[Date], MAX(Sales[Date])
)
```

## 4.4 Translating a Value

Value translation involves mapping values between tables or creating meaningful descriptions:

### Using SWITCH
```dax
Status Description = 
SWITCH(
    [Status],
    1, "Active",
    2, "Pending",
    3, "Completed",
    "Unknown"
)
```

### Using LOOKUPVALUE
```dax
Product Name = 
LOOKUPVALUE(
    Products[ProductName],
    Products[ProductID],
    Sales[ProductID]
)
```

### Best Practices for Value Translation
1. Create mapping tables for complex translations
2. Use clear naming conventions
3. Handle null values
4. Document translation logic
5. Consider performance impact

### Advanced Translation Techniques

#### Using Variables
```dax
Total Amount = 
VAR BaseAmount = [Quantity] * [Unit Price]
VAR DiscountAmount = BaseAmount * [Discount Percentage]
RETURN
    BaseAmount - DiscountAmount
```

#### Using SWITCH with Multiple Conditions
```dax
Performance Category = 
SWITCH(
    TRUE(),
    [Sales] > 1000000, "Excellent",
    [Sales] > 500000, "Good",
    [Sales] > 100000, "Fair",
    "Needs Improvement"
)
```

#### Using IF with Multiple Conditions
```dax
Shipping Priority = 
IF(
    [Total Weight] > 100,
    "Heavy",
    IF(
        [Distance] > 1000,
        "Long Distance",
        "Standard"
    )
)
``` 