---
layout: post
title: "Managing Files and Folders Permissions in PowerShell"
date: 2025-02-04
categories:
 - PowerShell
 - File Security
 - Windows Administration
 - Automation
tags:
 - powershell
 - security
 - automation
 - access_control
image:
 path: CommonFiles/Manage_FilesFoldersPermissions.jpeg
---
# Managing File and Folder Permissions

- **Table of Content**
    - [What Are ACLs?](#what-are-acls)
    - [Viewing Permissions](#viewing-permissions-get-acl)
    - [Creating and Modifying Access Rules](#creating-and-modifying-access-rules)
        - [Creating Access Rules](#creating-access-rules-new-object-systemsecurityaccesscontrolfilesystemaccessrule)
        - [Modifying Permissions](#modifying-permissions-set-acl)
    - [Advanced Permission Management](#advanced-permission-management)
        - [Managing Inheritance](#managing-inheritance-setaccessruleprotection)
        - [Viewing Inherited Permissions](#viewing-inherited-permissions)
        - [Batch Modifying Permissions](#batch-modifying-permissions)
        - [Understanding `Export-Clixml` and `Import-Clixml`](#understanding-export-clixml-and-import-clixml)
        - [Backup & Restore ACLs](#backup--restore-acls)
    - [Tips & Best Practices](#tips--best-practices)
    
Managing file and folder permissions is crucial for securing sensitive data and maintaining proper access control in Windows environments. PowerShell provides powerful tools, such as `Get-Acl` and `Set-Acl`, to view and modify permissions efficiently.

This guide explores how to retrieve, modify, and manage ACL (Access Control List) settings using PowerShell. It covers essential commands, inheritance management, and best practices to help you take full control over file security.

# What Are ACLs?

An **Access Control List (ACL)** defines file and folder permissions, determining who can read, write, or modify them. PowerShell provides `Get-Acl` to view and `Set-Acl` to modify these permissions.

---

# Viewing Permissions (`Get-Acl`)

Retrieve ACLs for a file or folder:

```powershell
# Get ACL for a file
Get-Acl -Path "C:\Temp\example.txt"

# Get ACL for a folder
Get-Acl -Path "C:\Temp"
```

For detailed permissions:

```powershell
(Get-Acl -Path "C:\Temp\example.txt").Access
```

---

# Creating and Modifying Access Rules

## Creating Access Rules (`New-Object System.Security.AccessControl.FileSystemAccessRule`)

Before modifying file or folder permissions, you need to define **Access Control Entries (ACEs)**. These rules specify **who** (user/group) gets **what type of access** (read, write, full control).

PowerShell uses the `New-Object System.Security.AccessControl.FileSystemAccessRule` command to create these rules.

### **Syntax:**

```powershell
New-Object System.Security.AccessControl.FileSystemAccessRule(
    IdentityReference, Rights, InheritanceFlags, PropagationFlags, AccessControlType
)
```

Each parameter defines how access is granted or denied:

| **Parameter** | **Description** |
| --- | --- |
| `IdentityReference` | The **user or group** (e.g., `"DOMAIN\Username"` or `"Everyone"`). |
| `Rights` | The **permissions** (e.g., `"FullControl"`, `"Read"`, `"Write"`). |
| `InheritanceFlags` | Defines if permissions **apply to subfolders/files**. (`None`, `ContainerInherit`, `ObjectInherit`) |
| `PropagationFlags` | Determines how permissions **propagate** (used when inheritance is enabled). |
| `AccessControlType` | `Allow` or `Deny`—whether the rule grants or blocks access. |

### **Examples:**

**Grant Full Control to a User**

```powershell
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "DOMAIN\Username", "FullControl", "Allow"
)
```

**Grant Read Access to a Group**

```powershell
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "Users", "Read", "Allow"
)
```

**Apply Read Access to a Folder and Subfolders**

```powershell
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "Everyone", "Read", "ContainerInherit, ObjectInherit", "None", "Allow"
)
```

**Deny Write Access to a User**

```powershell
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "DOMAIN\\Username", "Write", "Deny"
)
```

### **Next Step: Applying the Rule with `Set-Acl`**

Once you've created an access rule, you must **apply it** to a file or folder using `Set-Acl`.

Proceed to the **Modifying Permissions (`Set-Acl`)** section to see how to apply these rules.

## Modifying Permissions (`Set-Acl`)

### Granting Permissions

```powershell
$path = "C:\Temp\example.txt"
$acl = Get-Acl -Path $path

# Apply the previously created rule
$acl.SetAccessRule($rule)
Set-Acl -Path $path -AclObject $acl
```

### Removing Permissions

```powershell
$ruleToRemove = New-Object System.Security.AccessControl.FileSystemAccessRule("DOMAIN\Username", "FullControl", "Allow")
$acl.RemoveAccessRule($ruleToRemove)
Set-Acl -Path $path -AclObject $acl
```

---

# Advanced Permission Management

## Managing Inheritance (`SetAccessRuleProtection`)

By default, folders inherit permissions from their parent directory. The `$acl.SetAccessRuleProtection()` method allows you to **enable or disable inheritance**. It takes two boolean parameters:

```powershell
$acl.SetAccessRuleProtection(<DisableInheritance>, <PreserveExistingPermissions>)
```

- `DisableInheritance`:
    - `$true` → Stops inheriting permissions from the parent folder.
    - `$false` → Enables inheritance (inherits parent folder permissions).
- `PreserveExistingPermissions`:
    - `$true` → Keeps the existing permissions when disabling inheritance.
    - `$false` → Removes all inherited permissions when disabling inheritance.

### **Disable Inheritance but Keep Existing Permissions**

```powershell
$acl.SetAccessRuleProtection($true, $true)
Set-Acl -Path $path -AclObject $acl
```

### **Disable Inheritance and Remove Inherited Permissions**

```powershell
$acl.SetAccessRuleProtection($true, $false)
Set-Acl -Path $path -AclObject $acl
```

### Enable inheritance

```powershell
$acl.SetAccessRuleProtection($false, $false)
Set-Acl -Path $path -AclObject $acl
```

## Viewing Inherited Permissions

```powershell
(Get-Acl -Path "C:\Temp").Access | ForEach-Object {
	$_.IsInherited
}
```

## Batch Modifying Permissions

Grant **read-only** access to all files in a folder:

```powershell
Get-ChildItem -Path "C:\Temp" -Recurse | ForEach-Object {
		$acl = Get-Acl -Path $_.FullName
		$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("DOMAIN\Username", "Read", "Allow")
		$acl.SetAccessRule($rule)
		Set-Acl -Path $_.FullName -AclObject $acl
}
```

## Understanding `Export-Clixml` and `Import-Clixml`

The  `Export-Clixml` and `Import-Clixml` cmdlets are used to save and restore complex objects, including ACLs. Unlike `Export-Csv` or `Out-File`, which convert data info text, Clixml preserves object properties and structure, making it ideal for use for permissions.

### Why Use These Cmdlets?

- `Export-Clixml` → Saves ACLs as XML files, ensuring we can restore them later.
- `Import-Clixml` → Reads the saved XML file, and converts it back into a PowerShell object.

### Example Usage:

```powershell
# Export ACL to a backup file
$acl = Get-Acl -Path "C:\Temp\Example.txt"
$acl | Export-Clixml -Path "C:\Backup\example_acl.xml"

# Import ACL from the backup and restore it
$restoreAcl = Import-Clixml -Path "C:\Backup\example_acl.xml"
Set-Acl -Path "C:\Temp\example.txt" -AclObject $restoreAcl
```

This ensures that if permissions get accidentally changed, we can restore them quickly without  manually configuring everything.

## Backup & Restore ACLs

### Backup ACLs

```powershell
$acl = Get-Acl -Path "C:\Temp\example.txt"
$acl | Export-Clixml -Path "C:\Backup\example_acl.xml"
```

### Restore ACLs

```powershell
$restoredAcl = Import-Clixml -Path "C:\Backup\example_acl.xml"
Set-Acl -Path "C:\Temp\example.txt" -AclObject $restoredAcl
```

---

# Tips & Best Practices

✅ Always run PowerShell as **Administrator**.

✅ Use `Get-Acl` to verify permissions before changes.

✅ Backup ACLs before modifying critical files.