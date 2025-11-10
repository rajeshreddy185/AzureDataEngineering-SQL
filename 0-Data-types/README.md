** SQL data types **

SQL data types define the kind of data that a column can hold (e.g., numbers, text, dates). 
Choosing the correct data type is crucial for data integrity, efficiency, and accurate queries.

1. Numeric Data Types 🔢
These are used for storing whole numbers, decimals, and floating-point numbers.

| Data Type | Description | Example Values |
|-----------|-------------|----------------|
| INT / INTEGER | A standard whole number (no fractional part). Suitable for IDs, counts, and years. | 1, 50000, -10 |
| SMALLINT | A smaller range of whole numbers. Used when space is a concern and values won't exceed the range (e.g., -32,768 to 32,767). | 5, 1000 |
| BIGINT | A very large whole number. Used for large unique identifiers (like CustomerID in large-scale systems). | 900000000000 |
| DECIMAL(p, s) | Stores exact numeric values with a fixed precision (p, total digits) and scale (s, digits after the decimal point). Best for currency and financial data. | DECIMAL(8, 2) can store 12345.67 |
| NUMERIC(p, s) | Functionally identical to DECIMAL. | 123.45 |
| FLOAT / REAL | Floating-point numbers. Used when precision is less critical. | 1.23456, 9.9e-3 |

Export to Sheets
## 2. String (Text) Data Types 📝
These are used for storing characters, text, and binary data.

| Data Type | Description | Example Values |
|-----------|-------------|----------------|
| VARCHAR(size) | Variable-length string. Stores up to the maximum size you specify. It saves space if the actual data is shorter than the max size. Most commonly used text type. | VARCHAR(255) can store 'SQL' |
| CHAR(size) | Fixed-length string. Always uses the specified size, padding with spaces if the data is shorter. | CHAR(10) storing 'A' uses 10 bytes |
| TEXT | Used for very large strings or documents (e.g., product descriptions, articles). | A long paragraph of text |
| NVARCHAR(size) | Variable-length string that supports Unicode characters (for international languages). | 'こんにちは' (Japanese) |
| NCHAR(size) | Fixed-length string that supports Unicode. | 'A' (Unicode) |

Export to Sheets
## 3. Date and Time Data Types ⏰
These are used for storing temporal values.

| Data Type | Description | Example Values |
|-----------|-------------|----------------|
| DATE | Stores only the date (year, month, and day). | '2025-11-10' |
| TIME | Stores only the time of day (hour, minute, second). | '10:03:53' |
| DATETIME / TIMESTAMP | Stores both the date and time. The specific type name varies by database (e.g., SQL Server often uses DATETIME2). | '2025-11-10 10:03:53' |
| DATETIMEOFFSET | Stores date and time, plus a time zone offset (e.g., UTC-5). Crucial for applications dealing with multiple time zones. | '2025-11-10 10:03:53 +05:30' |

Export to Sheets
## 4. Other Specialized Data Types 💡

| Data Type | Description | Example Values |
|-----------|-------------|----------------|
| BOOLEAN / BIT | Stores a true or false value. Often represented as 1 (True) or 0 (False). | TRUE, FALSE |
| BINARY / VARBINARY | Used for storing binary data like images, audio files, or compressed data. | Raw byte data |
| XML | Used for storing XML data (specific to some systems like SQL Server). | `<root><item>...</item></root>` |
| JSON | Used for storing JSON document data (supported in modern database versions). | `{"city": "Bengaluru", "temp": 25}` |

Export to Sheets
