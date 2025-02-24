# Day 2: Getting Data

## 2.1 Databases

Power BI supports connections to various database types:

### SQL Server
- Direct connection or import
- Windows authentication or SQL authentication
- Support for both on-premises and Azure SQL

![SQL Connection](https://learn.microsoft.com/en-us/power-bi/connect-data/media/desktop-connect-sql-database/connect-sql-database_1.png)

### Other Supported Databases
- Oracle
- MySQL
- PostgreSQL
- Amazon Redshift
- Google BigQuery
- And many more

## 2.2 Files

Power BI can connect to various file formats:

### Excel Files
- Support for .xlsx, .xlsm, and .xls
- Can import tables, named ranges, and Power Query queries
- Handles multiple worksheets

### Text/CSV Files
- Delimiter detection
- Encoding options
- Column type inference

### Other File Types
- XML
- JSON
- PDF
- Folder import for multiple files

![File Import](https://learn.microsoft.com/en-us/power-bi/connect-data/media/desktop-data-sources/data-sources-02.png)

## 2.3 Web and Other Sources

### Web Sources
- Direct URL import
- Web API connections
- HTML table scraping
- Authentication support

### Other Online Sources
- SharePoint
- OneDrive
- Google Analytics
- Facebook
- GitHub

## 2.4 Azure and Software as a Service

### Azure Services
- Azure SQL Database
- Azure Synapse Analytics
- Azure Blob Storage
- Azure Data Lake Storage
- Azure Cosmos DB

![Azure Services](https://learn.microsoft.com/en-us/power-bi/connect-data/media/service-azure-and-power-bi/azure-power-bi.png)

### Software as a Service (SaaS)
- Salesforce
- Dynamics 365
- Google Analytics
- Adobe Analytics
- ServiceNow

## 2.5 Manual Entry

Power BI allows manual data entry through:

### Enter Data Feature
- Create tables directly in Power BI
- Copy/paste from clipboard
- Quick prototyping and testing

![Enter Data](https://learn.microsoft.com/en-us/power-bi/connect-data/media/desktop-enter-data-directly-into-desktop/enter-data-directly_1.png)

### Best Practices for Manual Entry
- Use for small datasets
- Temporary data testing
- Lookup tables
- Parameter tables

### Data Entry Tips
1. Plan your table structure
2. Use appropriate data types
3. Consider future maintenance
4. Document your manual entries
5. Consider automation for larger datasets 