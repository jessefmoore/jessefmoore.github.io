---
title : "Active Directory CheatSheet notes"
author: Jesse Moore
date: 2022-03-29 15:06:00 +0800
categories: [Blue-Teaming]
tags: [active-directory]
---


## PowerView Enumeration

We can gather additional information about our target using PowerView
1. Get current domain
```powershell
PS C:\Tools> Get-NetUser
user-dc.it.starlight.local
user-mssql.it.starlight.local
user-adminsrv.it.starlight.local
```

2. Enumerate Domain Admins
```powershell
Get-NetDomain
# See Attributes of the Domain Admins Group
Get-NetGroup -GroupName "Domain Admins" -FullData
# Get Members of the Domain Admins group
Get-NetGroupMember -GroupName "Domain Admins"
```


  1. Contains the `NTDS.dit` - a database that contains all of the information of an Active Directory domain controller as well as password hashes for domain users
  2. Stored by default in `%SystemRoot%\NTDS`
  3. Accessible only by the domain controller


# Group Policy Object

We can gather additional information about our target using PowerView

1. Get list of GPO in current domain.
```powershell
Get-NetGPO
Get-NetGPO -ComputerName <computer-name>
Get-GPO -All (GroupPolicy module)
Get-GPResultantSetOfPolicy -ReportType Html -Path C:\Users\Administrator\report.html (Provides RSoP)
gpresult /R /V (GroupPolicy Results of current machine)
```

2. Get GPO(s) which use Restricted Groups or groups.xml for interesting users
```powershell
Get-NetGPOGroup 
```

3. Get users which are in a local group of a machine using GPO
```powershell
Find-GPOComputerAdmin -ComputerName <computer-name>
```

4. Get machines where the given user is member of a specific group
```powershell
Find-GPOLocation -Username student1 -Verbose
```

5. Get OUs in a domain
```powershell
Get-NetOU -FullData
```

6. Get GPO applied on an OU. Read GPOname from gplink attribute from Get-NetOU
```powershell
Get-NetGPO -GPOname "{AB306569-220D-43FF-BO3B-83E8F4EF8081}"
Get-GPO -Guid AB306569-220D-43FF-B03B-83E8F4EF8081 (GroupPolicy module) 
```


We can gather additional information about our target using PowerView
1. Get the ACLs associated with the specified object
```powershell
Get-ObjectAcl -SamAccountName student1 -ResolveGUIDs
```

2. Get the ACLs associated with the specified prefix to be used for search
```powershell
Get-ObjectAcl -ADSprefix 'CN=Administrator,CN=Users' -Verbose
```

3. We can also enumerate ACLs using ActiveDirectory module but without resolving GUIDs
```powershell
(Get-Acl "AD:\CN=Administrator, CN=<name>, DC=<name>, DC=<name>,DC=local").Access
```

4. Get the ACLs associated with the specified LDAP path to be used for search
```powershell
Get-ObjectAcl -ADSpath "LDAP://CN=Domain Admins,CN=Users,DC=<name>,DC=<name>,DC=local" -ResolveGUIDs -Verbose
```

5. Search for interesting ACEs
```powershell
Invoke-ACLScanner -ResolveGUIDs
```

6. Get the ACLs associated with the specified path
```powershell
Get-PathAcl -Path "\\<computer-name>\sysvol"
````

1. Get a list of all domain trusts for the current domain 
```powershell
Get-NetDomainTrust
Get-NetDomainTrust -Domain <domain-name>
```

2. Get details about the current forest
```powershell
Get-NetForest
Get-NetForest -Forest <forest-name>
```

3.  Get all domains in the current forest
```powershell
Get-NetForestDomain
Get-NetForestDomain -Forest <forest-name>
```

4.  Get all global catalogs for the current forest
```powershell
Get-NetForestCatalog
Get-NetForestCatalog -Forest <forest-name>
```
 
5.  Map trusts of a forest
```powershell
Get-NetForestTrust
Get-NetForestTrust -Forest <forest-name>
```

### Hunting for users who have Local Admin access using Powerview

1.  Find all machines on the current domain where the current user has local admin access
```powershell
Find-LocalAdminAccess -Verbose
```
**This is very noise**
This function queries the DC of the current or provided domain for a list of computers `(Get-NetComputer)` and then use multi-threaded `Invoke-CheckLocalAdminAccess` on each machine.
This can also be done with the help of remote administration tools like WMI and PowerShell remoting. Pretty useful in cases ports (RPC and SMB) used by Find-LocalAdminAccess are blocked.
See `Find-WMILocalAdminAccess.ps1`
This leaves a 4624 (log-on event) and 4634 (log-off event) on each and every object in the domain. Same for Blood-Hound.


2.  Find computers where a domain admin (or specified user/group) has sessions
```powershell
Invoke-UserHunter
Invoke-UserHunter -GroupName "RDPUsers"
```
This function queries the DC of the current or provided domain for members of the given group (Domain Admins by default) using `Get-NetGroupMember`, gets a list of computers `(Get-NetComputer)` and list sessions and logged on users `(Get-NetSession/Get-NetLoggedon)` from each machine.


3. To confirm admin access
```powershell
Invoke-UserHunter -CheckAccess
```

4.  Find computers where a domain admin is logged-in
```powershell
Invoke-UserHunter -Stealth
```
This option queries the DC of the current or provided domain for members of the given group (Domain Admins by default) using `Get-NetGroupMember`, gets a _list only_ of high traffic servers (DC, File Servers and Distributed File servers) for less traffic generation and list sessions and logged on users `(Get-NetSession/Get-NetLoggedon)` from each machine.




# References
1. [https://zer1t0.gitlab.io/posts/attacking_ad/](https://zer1t0.gitlab.io/posts/attacking_ad/)
2. [https://tryhackme.com/room/activedirectorybasics](https://tryhackme.com/room/activedirectorybasics)
3. 0xStarlight github for these commands and more, thank you 0xStarlight

If you find the cheatsheet interesting, you can buy me a coffee 

<a href="https://www.buymeacoffee.com/jessefmoore"><img src="https://img.buymeacoffee.com/button-api/?text=Buy me Coffee?&emoji=&slug=0xStarlight&button_colour=b86e19&font_colour=ffffff&font_family=Poppins&outline_colour=ffffff&coffee_colour=FFDD00" /></a>
