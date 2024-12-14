# Get-ChangedProperties: Track Active Directory Attribute Changes

Monitoring changes in Active Directory (AD) attributes is crucial for maintaining security, ensuring compliance, and troubleshooting issues. To facilitate this, I've developed a PowerShell script—**Get-ChangedProperties**—that tracks attribute modifications for user and computer accounts across all domain controllers.

---

## Why Track Active Directory Changes?

Active Directory serves as the backbone for authentication and authorization in many organizations. Unmonitored changes can lead to security vulnerabilities, compliance violations, and operational disruptions. By tracking attribute changes, administrators can:

- **Enhance Security**: Detect unauthorized modifications to critical attributes.
- **Ensure Compliance**: Maintain accurate records for audits.
- **Facilitate Troubleshooting**: Quickly identify recent changes that may cause issues.

---

## Script Overview

The **Get-ChangedProperties** script examines specified AD accounts to identify attribute changes within a defined timeframe. It queries all domain controllers to ensure comprehensive tracking.

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
