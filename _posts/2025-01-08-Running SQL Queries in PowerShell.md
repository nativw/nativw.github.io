---
layout: post
title: "Running SQL Queries in PowerShell"
date: 2025-01-08
categories: [PowerShell, SQL, Automation]
tags: [powershell, sql, automation]
image:
 path: CommonFiles/PowerShell-SQL.jpg
---

# Running SQL Queries in PowerShell

PowerShell is a versatile scripting language often used by system administrators and developers for automation. Its ability to integrate with SQL Server allows users to execute database queries directly from scripts, streamlining tasks like data extraction, monitoring, and reporting. Its ability to integrate with various technologies, such as SQL Server, enables users to run database queries directly from scripts, simplifying tasks like monitoring, data extraction, and automated reporting.  This guide explains how to run SQL queries in PowerShell using .NET classes.

## Step-by-Step Guide

### 1. Define the SQL Server and Database

First, specify the SQL server and database you want to connect to:

```powershell
# Define the server and the database details
$Server = "MySQLServer\MySQL,45215"
$Database = "SQLDB"
```

Where:

- `MySQLServer`: The server name.
- `MySQL`: The instance name.
- `45215`: The port number on which the SQL Server instance is listening. By default, SQL Server instances use port 1433, but in this example, the instance is configured to use port `45215`.

### 2. Create the Connection String

Create a connection string with the necessary credentials:

```
# Define the connection string
$ConnectionString = "Server=$Server;Database=$Database;User ID=MyUser;Password=MyPassword"
```

**Note:** For SQL Authentication, include `User ID` and `Password`. For Windows Authentication, use `Integrated Security=True`.

### 3. Create and Open the Connection

Initialize and open the SQL connection:

```powershell
# Create and open the connection
$Connection = New-Object System.Data.SqlClient.SqlConnection
$Connection.ConnectionString = $ConnectionString
$Connection.Open()
```

Here, the `SqlConnection` object manages the connection to the SQL Server database. Assign the connection string and open the connection.

### 4. Define the SQL Query

```powershell
# Define the SQL query
$Query = "SELECT TOP 10 EmployeeID, EmployeeName FROM Employees"
```

For example, replace `EmployeeID` and `EmployeeName` with `CustomerID` and `CustomerName`, and `Employees` with `Customers` to query customer data. Suppose you are managing a customer database; this query can quickly retrieve a list of customer IDs and names for reporting or further processing. Customize these fields based on the table and columns in your database.

### 5. Create the SQL Command

Create a command object with the query:

```powershell
$Command = $Connection.CreateCommand()
$Command.CommandText = $Query
```

### 6. Execute the Query Using `SqlDataAdapter`

Use `SqlDataAdapter` to execute the query and fill a `DataSet` with the results:

```powershell
$Adapter = New-Object System.Data.SqlClient.SqlDataAdapter $Command
$DataSet = New-Object System.Data.DataSet
$Adapter.Fill($DataSet)
```

The `SqlDataAdapter` executes SQL commands and fills a `DataSet` with the results. This method is preferred for scenarios where you need a disconnected architecture, as the `DataSet` allows working on the data offline without requiring an active database connection. The `DataSet` temporarily holds the data, allowing manipulation like filtering or sorting without an active database connection.

### 7. Close the Connection

Always close the connection after executing the query:

```powershell
$Connection.Close()
```

### 8. Display the Results

Format and display the results:

```powershell
$DataSet.Tables[0] | Format-Table -AutoSize
```

**Note:** Use an index (e.g., `[0]`) to specify which table to display. This is essential if the `DataSet` contains multiple tables.

## Security Considerations

Avoid hardcoding sensitive credentials like usernames and passwords directly in scripts, as this can expose sensitive information. Use secure methods such as a secrets manager (e.g., Azure Key Vault, AWS Secrets Manager) or environment variables to retrieve credentials dynamically. Instead, use secure methods such as a secrets manager (e.g., Azure Key Vault, AWS Secrets Manager) or environment variables to dynamically retrieve credentials at runtime. Instead, consider these alternatives:

- Use environment variables to store credentials.
- Encrypt credentials using secure mechanisms such as `ConvertTo-SecureString` in PowerShell.

## Putting It All Together

Here’s an example of a complete script:

```powershell
# Define the server and the database details
$Server = "MySQLServer\MySQL,45215"
$Database = "SQLDB"

# Connection String
$ConnectionString = "Server=$Server;Database=$Database;User ID=MyUser;Password=MyPassword"

try {
    # Create and open the connection
    $Connection = New-Object System.Data.SqlClient.SqlConnection
    $Connection.ConnectionString = $ConnectionString
    $Connection.Open()

    # Query
    $Query = "SELECT TOP 10 EmployeeID, EmployeeName FROM Employees"

    # Create the SQL Command
    $Command = $Connection.CreateCommand()
    $Command.CommandText = $Query

    # Execute the query
    $Adapter = New-Object System.Data.SqlClient.SqlDataAdapter $Command
    $DataSet = New-Object System.Data.DataSet
    $Adapter.Fill($DataSet)

    # Display Result
    $DataSet.Tables[0] | Format-Table -AutoSize
}
catch {
    Write-Error "An error occurred: $_"
}
finally {
    # Ensure the connection is closed
    if ($Connection.State -eq [System.Data.ConnectionState]::Open) {
        $Connection.Close()
    }
}
```

With this guide, you can seamlessly integrate SQL queries into your PowerShell scripts, enabling tasks like data extraction, automated reporting, and database monitoring. Try applying this approach to a specific use case, such as generating weekly reports or automating data backups, to see the benefits in action. This integration streamlines workflows and empowers administrators to handle complex database operations efficiently.