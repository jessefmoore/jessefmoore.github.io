---
title : "Kansa -How to Get-Started"
author: Jesse Moore
date: 2018-11-21 10:00:00 +0800
categories: [Blue-Teaming]
tags: [active-directory, detection, incident response]
---

![image](https://user-images.githubusercontent.com/6413570/163220846-82bcfa78-5b99-4c4c-b326-a2a4d41703af.png)


# Introduction

This is a place to keep my notes on various projects so I don't forget.

If not so, you can give it a read from [here](https://jessefmoore.github.io/posts/Kansa-PowerShell-Part1/).

This guide aims to provide a starting place for Kansa PowerShell in an Active Directory network. You may refer to this as a Cheat-Sheet also.

This article will not contain everything you need to know about PowerShell. The following topics will be covered in a later article.

I will cover the following topics under this guide:
  1. Install Kansa for one host
  2. Usage example
  3. Running Modules standalone



> Throughout the article, I will use [Kansa PowerShell](https://github.com/davehull/Kansa), to show how to retrieve information from Active Directory joined machines.
> This article has been created with references from a few other articles
> All used references for completing this article will be listed below.

---

# Installation of Kansa
## What is Kansa & Powershell
A modular incident response framework in Powershell.

This also allows Powershell to execute .NET functions directly from its shell. Most Powershell commands, called _cmdlets,_ are written in .NET. Unlike other scripting languages and shell environments, the output of these _cmdlets_ are objects - making Powershell somewhat object oriented.

## PreREQS:

Run the below on all Windows machines you need to remotely access (So all Windows machines you want Kansa to grab info from).
```
Enable-PSRemoting -Force
Set-Item wsman:\localhost\client\trustedhosts *
```
https://pastebin.com/ve4pPvV3

Check the Remote PowerShell with these commands
```
New-PSSession -ComputerName WIN-AD -Credential campus\Administrator
Get-PSSESSION
```
https://pastebin.com/XJRwvNNC


## Download Install
Download latest build from github
```
https://github.com/davehull/Kansa
```
unzip it, and “unblock” the ps1 files.

The easiest way to do this if you’re using Powershell v3 or later is to cd to the directory where Kansa resides and do:

## Unblock-File
```
ls -r *.ps1 | Unblock-File
```

Ensure that you check your execution policies with PowerShell:

```
Set-ExecutionPolicy AllSigned | RemoteSigned | Unrestriced
```

![image2](https://user-images.githubusercontent.com/6413570/163231082-42b14a36-eff2-4e0e-b098-491169877ae7.png)


## Usage example
Open an elevated Powershell Prompt (Right-click Run As Administrator)

At the command prompt, enter:

```
.kansa.ps1 -Target $env:COMPUTERNAME -ModulePath .Modules -Verbose
```

The script should start collecting data or you may see an error aboutnot having Windows Remote Management enabled.

When it finishes running, you’ll have a new Output_timestamp subdirectory, with subdirectories for data collected by each module.

## Test WIN-AD 

```
PS C:\Tools\Kansa-master> .\kansa.ps1 -Pushbin -Target WIN-AD -Credential chi.local\administrator -Authentication Negotiate
 
VERBOSE: Found Modules\\Modules.conf.
 
VERBOSE: Running modules:
 
Get-PrefetchListing
 
Get-Netstat
 
Get-DNSCache
 
Get-SmbSession
 
Get-LogWinEvent
 
Get-SchedTasks
 
Get-LocalAdmins
 
Get-Hotfix
 
VERBOSE: Waiting for Get-PrefetchListing to complete.
 
 
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
 
--     ----            -------------   -----         -----------     --------             -------
 
1      Job1            RemoteJob       Completed     True            WIN-AD               <#...
 
VERBOSE: Waiting for Get-Netstat to complete.
 
3      Job3            RemoteJob       Completed     True            WIN-AD               <#...
 
VERBOSE: Waiting for Get-DNSCache to complete.
 
5      Job5            RemoteJob       Completed     True            WIN-AD               <#...
 
VERBOSE: Waiting for Get-SmbSession to complete.
 
7      Job7            RemoteJob       Completed     True            WIN-AD               <#...
 
VERBOSE: Waiting for Get-LogWinEvent Security to complete.
 
9      Job9            RemoteJob       Completed     True            WIN-AD               <# ...
 
VERBOSE: Waiting for Get-SchedTasks to complete.
 
11     Job11           RemoteJob       Completed     True            WIN-AD               <#...
 
VERBOSE: Waiting for Get-LocalAdmins to complete.
 
13     Job13           RemoteJob       Completed     True            WIN-AD               <#...
 
VERBOSE: Waiting for Get-Hotfix to complete.
 
15     Job15           RemoteJob       Completed     True            WIN-AD               <#...
 
PS C:\Tools\Kansa-master>
```


## Running Modules Standalone
Kansa modules can be run as standalone utilities outside of the Kansa framework. Why might you want to do this? Consider netstat -naob, the
output of the command line utility is ugly and doesn't easily lenditself to analysis.

```powershell
Modules\Net\Get-Netstat.ps1
```

as a standalone script will call netstat -naob, but it will return
Powershell objects in an easy to read, easy to analyze format. You can
easily convert its output to CSV, TSV or XML using normal Powershell
cmdlets. Here's an example:

```powershell
.\Get-Netstat.ps1 | ConvertTo-CSV -Delimiter "`t" -NoTypeInformation | % { $_ -replace "`"" } | Set-Content netstat.tsv
```

![Image3](https://user-images.githubusercontent.com/6413570/163231486-5e70bfc1-2b09-4e9e-8c15-5f0a2b1b85d4.png)


# References
1. Powershell Introdution from : [https://tryhackme.com/room/powershell](https://tryhackme.com/room/powershell)
2. Kansa adhdproject : [adhdproject.github.io] (https://adhdproject.github.io/#!Windows/Kansa.md#Example_1:_Usage)
3. 13Cubed video : [13cubed] (https://www.youtube.com/watch?v=OIT9oaFmXZU)
4. Jon Ketchum : [SANS DFIR YouTube] (https://www.youtube.com/watch?v=ZyTbqpc7H-M)


If you find in my notes interesting, you can buy me a coffee 

<a href="https://www.buymeacoffee.com/jessefmoore"><img src="https://img.buymeacoffee.com/button-api/?text=Buy me Coffee?&emoji=&slug=jessefmoore&button_colour=b86e19&font_colour=ffffff&font_family=Poppins&outline_colour=ffffff&coffee_colour=FFDD00" /></a>
