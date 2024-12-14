---
layout: post
title: Tracking Active Directory Attribute Changes
category: Active Directory
tags: PowerShell ActiveDirectory
image:
 path: CommonFiles/PS-Get-ChangedProperties.jpg
 alt: Get Changed AD Properties
---
Monitoring changes in Active Directory (AD) attributes is crucial for maintaining security, ensuring compliance, and troubleshooting issues. To facilitate this, I wrote a PowerShell script—**[Get-ChangedProperties.ps1](https://github.com/nativw/Get-ChangedProperties/blob/4d274ea334806ee0c8c79ee1faf3a7ead294b44d/Get-ChangedProperties.ps1)**—that tracks attribute modifications for user and computer accounts across all domain controllers.

---

## Why Track Active Directory Changes?

Active Directory serves as the backbone for authentication and authorization in many organizations. Unmonitored changes can lead to security vulnerabilities, compliance violations, and operational disruptions. By tracking attribute changes, administrators can:

- **Enhance Security**: Detect unauthorized modifications to critical attributes.
- **Ensure Compliance**: Maintain accurate records for audits.
- **Facilitate Troubleshooting**: Quickly identify recent changes that may cause issues.

---

## Script Overview

The **`Get-ChangedProperties.ps1`** script examines specified AD accounts to identify attribute changes within a defined timeframe. It queries all domain controllers to ensure comprehensive tracking.

### Key Features

- **Versatile Account Support**: Handles both user and computer accounts.
- **Comprehensive Domain Controller Querying**: Checks all domain controllers for changes.
- **Consolidated Reporting**: Provides a unified list of detected changes.
- **Detailed Documentation**: Includes thorough instructions for ease of use.

---

## Prerequisites

- **Windows PowerShell**
- **Active Directory Module for Windows PowerShell**

---

## Parameters

- **`DaysToCheck`** *(int)*: Number of days in the past to check for changes.
- **`AccountName`** *(string)*: Name of the user or computer account to examine.
- **`ObjectType`** *(string)*: Type of account; valid values are `"User"` and `"Computer"`.

---

## Usage

```powershell
& 'C:\Scripts\Get-ChangedProperties.ps1' -DaysToCheck <Days> -AccountName <AccountName> -ObjectType <User|Computer>
```

## Examples

### Checking a User Account

To check for changes in the past 7 days for the user account `jdoe`, use the following command:
```powershell
& "C:\Scripts\Get-ChangedProperties.ps1" -DaysToCheck 7 -AccountName "jdoe" -ObjectType "User"
```

#### Sample Output

    AttributeName  LastChangeTime       DomainController 
    -------------- -------------------  ----------------- 
    mail            10-Dec-24 14:32:00  DC01             
    department      11-Dec-24 09:15:00  DC02             
    title           12-Dec-24 10:10:00  DC03             

### Checking a Computer Account

To check for changes in the past 7 days for the computer account `COMP01`, use the following command:
```powershell
& "C:\Scripts\Get-ChangedProperties.ps1" -DaysToCheck 7 -AccountName "COMP01" -ObjectType "Computer"
```

#### Sample Output

    AttributeName  LastChangeTime       DomainController 
    -------------- -------------------  ----------------- 
    IPv4Address     10-Dec-24 14:32:00  DC01             
    MemberOf        11-Dec-24 09:15:00  DC02             
    Enabled         12-Dec-24 10:10:00  DC03             

## Error Handling

The **`Get-ChangedProperties.ps1`** script includes basic error handling to ensure smooth execution. However, it's important to consider the following potential issues:

- **Incorrect Account Name**: If the specified account does not exist, the script will return an error indicating that the account could not be found.
- **Invalid Object Type**: If an invalid `ObjectType` (e.g., other than "User" or "Computer") is provided, the script will throw an error.
- **Insufficient Permissions**: Running the script requires appropriate Active Directory permissions. If the script cannot access the required domain controllers or attributes, an error will be returned.
  
For troubleshooting, ensure that your account has sufficient privileges and that the specified parameters are correct.

---

## Conclusion

The **`Get-ChangedProperties.ps1`** script is a powerful tool for monitoring Active Directory attribute changes, which is crucial for maintaining security and operational integrity. By tracking changes in real-time, administrators can quickly identify unauthorized modifications, ensure compliance, and streamline troubleshooting efforts. 

Feel free to try out the script and provide feedback or improvements. You can find the **`Get-ChangedProperties.ps1`** script [here](https://github.com/nativw/Get-ChangedProperties/blob/4d274ea334806ee0c8c79ee1faf3a7ead294b44d/Get-ChangedProperties.ps1).
