# Pro Data Analysis Skill

Senior data analyst and SQL engineer specializing in enterprise data warehouses. Translates business questions into fully documented, optimized SQL queries through a systematic 7-stage workflow.

## Features

- ✅ **Multi-database support**: Oracle, MySQL, PostgreSQL, SQL Server
- 🔍 **Data Discovery**: Search for tables/columns via metadata and comments
- 📊 **Query Optimization**: Analyze EXPLAIN plans and optimize performance
- ✅ **Safety First**: All queries are executed with limits and timeouts
- 📝 **Full Documentation**: Every query includes comments and documentation
- 🤝 **Human-in-the-Loop**: Checkpoints for user confirmation before proceeding

## System Requirements

### Python Packages

```bash
# Core dependencies
pip install python-dotenv

# Database drivers (choose according to the database you use)
pip install oracledb               # For Oracle
pip install mysql-connector-python # For MySQL
pip install psycopg2-binary        # For PostgreSQL
pip install pyodbc                 # For SQL Server

# Optional (for Excel document search)
pip install openpyxl pandas
```

### SQL Server - ODBC Driver

SQL Server requires an ODBC driver. Install according to your operating system:

**Windows:**
- Download from [Microsoft](https://learn.microsoft.com/en-us/sql/connect/odbc/download-odbc-driver-for-sql-server)

**macOS:**
```bash
# Install unixODBC
brew install unixodbc

# Install Microsoft ODBC Driver
brew tap microsoft/mssql-release https://github.com/Microsoft/homebrew-mssql-release
brew install msodbcsql17
```

**Linux (Ubuntu/Debian):**
```bash
# Install unixODBC
sudo apt-get install unixodbc-dev

# Install Microsoft ODBC Driver 17
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
curl https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/prod.list | sudo tee /etc/apt/sources.list.d/mssql-release.list
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install -y msodbcsql17
```

## Configuration

### 1. Create a .env file

Copy `.env.example` to `.env` and fill in your connection details:

```bash
cp .env.example .env
```

### 2. Configure database connections

#### Oracle
```env
DWH_TYPE=oracle
DWH_USERNAME=your_username
DWH_PASSWORD=your_password
DWH_DSN=hostname:port/service_name
```

#### MySQL
```env
MYSQL_DEV_TYPE=mysql
MYSQL_DEV_USERNAME=your_username
MYSQL_DEV_PASSWORD=your_password
MYSQL_DEV_HOST=localhost
MYSQL_DEV_PORT=3306
MYSQL_DEV_DATABASE=your_database
```

#### PostgreSQL
```env
PG_DEV_TYPE=postgresql
PG_DEV_USERNAME=your_username
PG_DEV_PASSWORD=your_password
PG_DEV_HOST=localhost
PG_DEV_PORT=5432
PG_DEV_DATABASE=your_database
```

#### SQL Server
```env
MSSQL_DEV_TYPE=sqlserver
MSSQL_DEV_USERNAME=your_username
MSSQL_DEV_PASSWORD=your_password
MSSQL_DEV_HOST=localhost
MSSQL_DEV_PORT=1433
MSSQL_DEV_DATABASE=your_database
# Optional: MSSQL_DEV_DRIVER={ODBC Driver 18 for SQL Server}
```

## Available Scripts

### 1. Search Schema Metadata
Search for tables and columns by name or comment:

```bash
# Search in comments and names
python scripts/search_schema.py --keyword "customer" --db DWH

# Search only in comments
python scripts/search_schema.py --keyword "customer" --search-in comments --db DWH

# Search with regex
python scripts/search_schema.py --keyword "CUST_|CUSTOMER_" --regex --db DWH

# Filter by schema
python scripts/search_schema.py --keyword "revenue" --schema SALES --db DWH
```

### 2. Check Table Structure
Check table structure, indexes, partitions, and statistics:

```bash
# Oracle
python scripts/check_table.py OWNER TABLE_NAME --db DWH

# SQL Server
python scripts/check_table.py dbo Customers --db MSSQL_DEV

# Output as JSON
python scripts/check_table.py SCHEMA TABLE --db DWH --format json

# Output as Markdown
python scripts/check_table.py SCHEMA TABLE --db DWH --format markdown
```

### 3. Run Query Safely
Run SELECT queries with row limits and timeouts:

```bash
# Run query from string
python scripts/run_query_safe.py --sql "SELECT * FROM SCHEMA.TABLE" --db DWH

# Run query from file
python scripts/run_query_safe.py --file query.sql --db DWH --limit 50

# Count rows only
python scripts/run_query_safe.py --file query.sql --db DWH --count-only

# Output as JSON
python scripts/run_query_safe.py --file query.sql --db DWH --format json
```

### 4. EXPLAIN Plan Analysis
Analyze execution plans for performance optimization:

```bash
# Run EXPLAIN on a query
python scripts/explain_query.py --file query.sql --db DWH

# Oracle
python scripts/explain_query.py --sql "SELECT * FROM TABLE" --db DWH

# SQL Server (using SHOWPLAN)
python scripts/explain_query.py --file query.sql --db MSSQL_DEV

# Output as JSON
python scripts/explain_query.py --file query.sql --db DWH --format json
```

### 5. Find Relationships
Find foreign keys and join paths:

```bash
# Find relationships for one table
python scripts/find_relationships.py --schema SCHEMA --table TABLE_NAME --db DWH

# Find join paths between multiple tables
python scripts/find_relationships.py --schema SCHEMA --tables TABLE1,TABLE2,TABLE3 --db DWH
```

### 6. Sample Data
Get sample data and profiling:

```bash
# Get 10 sample rows
python scripts/sample_data.py --schema SCHEMA --table TABLE_NAME --db DWH

# Get 50 sample rows
python scripts/sample_data.py --schema SCHEMA --table TABLE_NAME --db DWH --rows 50

# Data profiling (analyze data distribution)
python scripts/sample_data.py --schema SCHEMA --table TABLE_NAME --db DWH --profile
```

### 7. Search Documents
Search in Excel documentation (if available):

```bash
# Search in folder documents/
python scripts/search_documents.py --keyword "customer" --folder documents/

# Search with regex
python scripts/search_documents.py --keyword "CUST|CUSTOMER" --folder documents/ --regex
```

## 7-Stage Workflow

When using this skill with Claude, the workflow proceeds through 7 stages:

1. **Requirement Analysis**: Analyze business requirements
2. **Data Discovery**: Find relevant tables/columns
3. **Data Mapping**: Map data and define join conditions
4. **Query Design**: Design query with CTEs and comments
5. **Query Testing**: Test with EXPLAIN and safe execution
6. **Optimization**: Optimize based on EXPLAIN plan
7. **Documentation**: Save query and documentation

### Checkpoints

The workflow has 4 checkpoints for user confirmation:
- **CP1**: After Requirement Analysis
- **CP2**: After Data Discovery (confirm tables/columns)
- **CP3**: After Data Mapping (confirm joins/filters)
- **CP4**: Before Query Testing (confirm query logic)

You can skip checkpoints by saying "skip checkpoints" or "auto mode".

## Database-Specific Notes

### Oracle
- Use `ROWNUM` for pagination
- Supports `CONNECT BY` for hierarchical queries
- Partition pruning with `WHERE partition_key >= ...`

### MySQL
- Case-insensitive string comparison (default)
- Use `LIMIT` for pagination
- `GROUP_CONCAT` for string aggregation

### PostgreSQL
- Case-sensitive string comparison (default)
- Use `LIMIT` for pagination
- Native JSON/JSONB support

### SQL Server
- Use `TOP` or `OFFSET...FETCH NEXT` for pagination
- `STRING_AGG` for string aggregation (SQL Server 2017+)
- Extended properties for table/column comments

## References

In the `references/` folder, you can find reference documents:

- `dialect-differences.md`: Differences between Oracle, MySQL, PostgreSQL, SQL Server
- `query-patterns.md`: Common SQL query patterns
- `window-functions.md`: Guide to window functions
- `optimization.md`: Query optimization techniques
- `database-design.md`: Database design
- `dwh-patterns.md`: Data warehouse patterns

## Troubleshooting

### SQL Server Connection Issues

**Error: "Can't open lib 'ODBC Driver 17 for SQL Server'"**
- Install the ODBC driver (see System Requirements)
- Or specify a different driver: `MSSQL_DEV_DRIVER={ODBC Driver 18 for SQL Server}`

**Error: "Login failed for user"**
- Check username/password
- Check SQL Server Authentication mode (Windows Auth vs SQL Auth)
- Ensure user has privilege to access the database

**Error: "SSL Security error"**
- Add `TrustServerCertificate=yes` to the connection string
- Or properly configure SSL certificate

### Oracle Connection Issues

**Error: "TNS:could not resolve the connect identifier"**
- Check DSN format: `hostname:port/service_name`
- Check tnsnames.ora if using alias

### MySQL Connection Issues

**Error: "Access denied for user"**
- Check username/password
- Check host access permissions: `GRANT ALL ON db.* TO 'user'@'host'`

## License

MIT

## Version History

- **2.2.0**: Added SQL Server support
- **2.1.0**: Improved checkpoints and workflow
- **2.0.0**: Added PostgreSQL support
- **1.0.0**: Initial release with Oracle and MySQL
