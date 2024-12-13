---
layout: post
title: Get-ChangedProperties
category: Active Directory
tags: PowerShell ActiveDirectory
image:
 path: CommonFiles/PS-Get-ChangedProperties.jpg
 alt: Get Changed AD Properties
---
Tracking changes in Active Directory attributes can be incredibly useful for various scenarios, such as monitoring security, auditing compliance, and troubleshooting issues.
To address these needs, I've developed a PowerShell script that efficiently tracks changes in AD attributes for both user and computer accounts across all domain controllers.

Here is a small glimpse of the script:
```powershell
foreach ($dc in $domainControllers) {
    try {
        $metadata = Get-ADReplicationAttributeMetadata -Object $account.DistinguishedName -Server $dc.HostName | Where-Object {$_.LastOriginatingChangeTime -gt $date -and $_.AttributeName -ne "lastLogonTimestamp"}
        foreach ($entry in $metadata) {
            $results += [PSCustomObject]@{
                AttributeName = $entry.AttributeName
                LastChangeTime = $entry.LastOriginatingChangeTime
                DomainController = $dc.HostName
            }
        }
    } catch {
        Write-Host "Error querying $($dc.HostName): $_"
    }
}

$results
```

[!info]Link to the code: ([https://github.com/nativw/Get-ChangedProperties/blob/main/Get-ChangedProperties.ps1](https://github.com/nativw/Get-ChangedProperties/blob/main/Get-ChangedProperties.ps1))

This PowerShell script checks for changes in Active Directory attributes for user or computer accounts across all domain controllers. It helps identify which properties have changed over a specified number of days, making it easier to monitor and audit AD changes.

## Features
- Accepts both user and computer accounts
- Checks changes across all domain controllers
- Outputs a consolidated list of changes
- Includes detailed documentation for easy usage

## Prerequisites
- Windows PowerShell
- Active Directory module for Windows PowerShell

## Parameters
- `DaysToCheck` (int): The number of days in the past to check for changes. If greater than 0, it will be converted to a negative value.
- `AccountName` (string): The name of the user or computer account to check.
- `ObjectType` (string): The type of account to check. Valid values are "User" and "Computer".

## Usage
```powershell
& 'C:\Scripts\PSFile.ps1' -DaysToCheck <Days> -AccountName <AccountName> -ObjectType <User|Computer>
```

## Examples
### Check User Account
```powershell
& 'C:\Scripts\PSFile.ps1' -DaysToCheck 7 -AccountName "jdoe" -ObjectType "User"
```

This example checks for changes in the past 7 days for the user account "jdoe".


### Check Computer Account
```powershell
& 'C:\Scripts\PSFile.ps1' -DaysToCheck 7 -AccountName "COMP01" -ObjectType "Computer"
```

This example checks for changes in the past 7 days for the computer account "COMP01".
