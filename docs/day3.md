# Day 3: Transforming Data

## 3.1 Query Editor in Power BI

The Query Editor is a powerful tool for data transformation:

![Query Editor Interface](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-query-overview/query-overview-view-native.png)

Key features:
- Applied Steps window
- Preview pane
- Formula bar
- Ribbon interface
- Query settings

## 3.2 Renaming Queries

Best practices for query naming:
- Use descriptive names
- Follow naming conventions
- Avoid special characters
- Consider relationships
- Document changes

Steps to rename:
1. Right-click query
2. Select "Rename"
3. Enter new name
4. Press Enter

## 3.3 Combining Queries: Append

Append combines queries vertically (adds rows):

![Append Queries](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-append-queries/append-queries_1.png)

Use cases:
- Combining monthly data
- Merging similar tables
- Consolidating reports
- Combining multiple files

Steps:
1. Select first query
2. Click Append Queries
3. Choose queries to append
4. Select append type

## 3.4 Fixing Metadata

Metadata management includes:
- Data type changes
- Column names
- Column descriptions
- Data categorization
- Sort order

Common metadata fixes:
- Converting text to numbers
- Setting date formats
- Defining categorical columns
- Setting primary keys
- Establishing relationships

## 3.5 Filtering Rows

Filtering options:
- Basic filters
- Advanced filters
- Remove duplicates
- Keep/remove errors
- Top N filtering

![Filtering Data](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-common-query-tasks/query-tasks-filter.png)

## 3.6 Eliminating Columns

Column management:
- Remove columns
- Choose columns
- Go to column
- Reorder columns
- Split columns

Best practices:
- Remove unnecessary columns early
- Keep relevant data only
- Consider performance impact
- Document removed columns
- Maintain data relationships

## 3.7 Combining Queries: Merge

Merge joins queries horizontally (adds columns):

![Merge Queries](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-merge-queries-advanced/merge-queries-advanced_1.png)

Join types:
- Left outer
- Right outer
- Full outer
- Inner
- Left anti
- Right anti

Steps:
1. Select base query
2. Click Merge Queries
3. Choose second query
4. Select join columns
5. Choose join type

## 3.8 Adding a Column

Methods to add columns:
- Custom column
- Conditional column
- Index column
- Duplicate column
- Reference column

![Add Column](https://learn.microsoft.com/en-us/power-bi/transform-model/media/desktop-add-custom-column/add-custom-column_1.png)

Formula examples:
```
// Custom column
= [Column1] + [Column2]

// Conditional column
if [Amount] > 1000 then "High" else "Low"

// Date extraction
= Date.Year([DateColumn])
```

Best practices:
1. Use meaningful names
2. Document calculations
3. Consider performance
4. Test edge cases
5. Maintain consistency 