---
layout: post
title: "Introducing Create-DriveD"
date: 2024-12-24
categories: [PowerShell, Security]
tags: [powershell, security, bitlocker, encryption]
image:
 path: CommonFiles/Create-DriveD.png
---

# Create-DriveD.ps1  

[Link to the script on GitHub](https://github.com/nativw/Create-DriveD)  

## Overview  

Creating new partitions on computers can sometimes be a tedious task, especially if you're an IT professional supporting multiple remote sites.  

Instead of connecting to remote computers one by one, I wrote an automatic script designed to simplify disk management and encryption tasks. This script can be modified to fit your specific requirements and can be deployed remotely using automated tools such as SCCM, BigFix, PDQ Deploy, Ansible, or even custom PowerShell remoting scripts.  

## Key Features  

- **Automated Partition Management:**  
  - Shrinks the C drive by a specified amount.  
  - Creates a new partition labeled as D.  

- **Encryption Integration:**  
  - Automatically encrypts the new partition D if the C drive is encrypted.  
  - Uses the MBAM script (`Invoke-MbamClientDeployment.ps1`) to securely escrow encryption keys to an MBAM server.  

- **Flexibility and Customization:**  
  - The script is designed to be easily adapted to different environments and requirements.  

## Prerequisites  

To use this script effectively, ensure the following:  

1. **Administrative Privileges:**  
   Run the script with admin rights to access disk and encryption features.  

2. **BitLocker and MBAM Configuration:**  
   BitLocker encryption and MBAM should be properly configured in your environment.  

3. **Endpoint Setup:**  
   Replace the placeholder **`<YourRecoveryServiceEndpoint>`** in the script with the actual MBAM service endpoint address for key escrow.  

## Deployment Options  

This script can be deployed remotely using tools and methods such as:  
- **Microsoft SCCM**  
- **BigFix**  
- **PDQ Deploy**  
- **Ansible**  
- **PowerShell Remoting**  

Choose the tool that best fits your organization's infrastructure for seamless automation.  

## Get Started  

Explore the repository, review the script, and automate your disk management tasks today!  
➡️ [Create-DriveD Repository on GitHub](https://github.com/nativw/Create-DriveD)  