---
title : "US-CERT AKA CISA Alert-TA18-074A-Detection"
author: Jesse Moore
date: 2019-02-05 11:17:00 +0800
categories: [detection]
#tags: [detection, incident response, powershell, CISA]
---

This is a script to find ioc from US-CERT alert.

```
<#
.SYNOPSIS
Get-US-CERT-TA18-074A.ps1 returns data about US-CERT Alert. https://www.us-cert.gov/ncas/alerts/TA18-074A
.NOTES
The next line is needed by Kansa.ps1 to determine how to handle output
from this script.
OUTPUT TSV
 
Contributed by Jesse Moore
#>
Write-Host "*****PowerShell_Version******";$PSVersionTable.PSVersion.Major
#Find OS Version
Write-Host "*****Find OS Version 6? aka 7. or 10?******"
Write-Host "*****Find OS Version 6? aka 7. or 10?******"
(Get-WmiObject -class Win32_OperatingSystem).Caption
(Get-CimInstance Win32_OperatingSystem).version
    Start-Sleep -Second 2
Write-Host "*****fDenyTSConnections******" ;Get-ItemPropertyValue 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' fDenyTSConnections #Checks for TRUE (1) deny connections, #/t REG_DWORD /d 0 /f
Write-Host "*****NotificationPackages******" ;Get-ItemPropertyValue 'HKLM:\SYSTEM\CurrentControlSet\Control\Lsa' 'Notification Packages' #checks for unsigned DLLs for passwordFilter
    Start-Sleep -Second 2
write-host "*****CachedLogonsCount******" ;Get-ItemPropertyValue 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon' -Name 'CachedLogonsCount'
    Start-Sleep -Second 2
Write-Host "*****FSingleSessionPerUser******" ;Get-ItemPropertyValue 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' fSingleSessionPerUser #/t REG_DWORD /d 0 /f
    Start-Sleep -Seconds 2
Write-Host "*****ExecutionPolicy******" ;Get-ItemPropertyValue 'HKLM:\SOFTWARE\Microsoft\PowerShell\1\ShellIds\Microsoft.PowerShell\' -Name ExecutionPolicy
    Start-Sleep -Seconds 2
#Looking for just the presence of this Key name below
Write-host "*****Looking for just the presence of this key name******"
Write-host "*****Looking for just the presence of this key name******"
Start-Sleep -Seconds 2
Write-Host "*****StandardProfile_OpenPorts?******"
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\GloballyOpenPorts\List' -erroraction 'silentlycontinue' #/v 3389:TCP /t REG_SZ /d "3389:TCP:*:Enabled:Remote Desktop" /f
    Start-Sleep -Seconds 2
Write-Host "*****DomainProfile_OpenPort?******"
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\GloballyOpenPorts\List' -erroraction 'silentlycontinue'#/v 3389:TCP /t REG_SZ /d "3389:TCP:*:Enabled:Remote Desktop" /f
    Start-Sleep -Seconds 2
Write-Host "*****EnabledConcurrentSessions******"
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Licensing Core' -Name EnableConcurrentSessions -erroraction 'silentlycontinue' #/t REG_DWORD /d 1 /f
Start-Sleep -Seconds 2
Write-Host "*****WinLogonEnabledConcurrentSessions******"
    Start-Sleep -Seconds 2
#
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon' -Name EnableConcurrentSessions -erroraction 'silentlycontinue' #/t REG_DWORD /d 1 /f
#
Write-Host "*****WinLogonAllowMultipleTSSessions******"
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon' -Name AllowMultipleTSSessions -erroraction 'silentlycontinue'# /t REG_DWORD /d 1 /f
#
    Start-Sleep -Seconds 2
#
Write-Host "*****TerminalServcsMaxInstanceCount******"
Get-ItemProperty 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name MaxInstanceCount -erroraction 'silentlycontinue'#/t REG_DWORD /d 100 /f
#
    Start-Sleep -Seconds 2
#
#Only Win10 SpecialAccounts
Write-Host "*****WinLogonSpecialAccounts Win10+******"
Write-Host "*****WinLogonSpecialAccounts Win10+******"
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\SpecialAccounts\UserList' -erroraction silentlycontinue #/v MS_BACKUP /t REG_DWORD /d 0 /f ONLYWIN10
#
    Start-Sleep -Seconds 2
#
Write-Host "*****DontDisplayLastUsername******" ;Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system' | Select-Object -Property dontdisplaylastusername -erroraction silentlycontinue # /t REG_DWORD /d 1 /f
    Start-Sleep -Seconds 2
Write-Host "*****LocalAccountTokenFilterPolicy******" ;Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system' | Select-Object -Property LocalAccountTokenFilterPolicy -erroraction silentlycontinue # /t REG_DWORD /d 1 /f
    Start-Sleep -Seconds 2
Write-Host "*****LocalAccountTokenFilterPolicy******" ;Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system' | Select-Object -Property legalnoticetext -erroraction silentlycontinue
    Start-Sleep -Seconds 2
#
#Find OS version
Write-Host "*****Find OS Version 6? aka 7. or 10?******"
(Get-CimInstance Win32_OperatingSystem).version
#Find SMBv1 enabled
#Write-Host "*****SMBv1 Enbaled******"
#Write-Host "*****SMBv1 Enbaled******"
Get-Item HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters | ForEach-Object {Get-ItemProperty $_.pspath}
#Get-WindowsOptionalFeature –Online –FeatureName SMB1Protocol | Where-Object {$_.State -contains 'Enabled'} -erroraction silentlycontinue
#
Start-Sleep -Seconds 2
Write-Host "*****SMBv1 Reg Key******"
reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters
#
Start-Sleep -Seconds 2
#Only WIN10 find DNSClient
#reg query "HKLM\Software\Policies\Microsoft\Windows NT\DNSClient" | cmd
#reg query "HKLM\Software\Policies\Microsoft\Windows NT\DNSClient"
    Start-Sleep -Seconds 2
#Find services running
Write-Host "*****Services Running******"
Get-Service | Where-Object -Property Status -eq -Value 'running'
    Start-Sleep -Seconds 5
#
#Find last write time on file
Write-Host "*****LastWriteTime******" ;Get-ItemPropertyValue -Path 'C:\Users\%UserProfile%\Desktop\putty.exe' -Name LastWriteTime,CreationTime -erroraction 'silentlycontinue'
#LocalUsers to see if added backdoor user
Write-Host "*****LocalUser******"
Get-LocalUser
    Start-Sleep -Seconds 5
Write-Host "*****LocalAdmin******"
Get-LocalUser Administrator -erroraction silentlycontinue
    Start-Sleep -Seconds 2
Write-Host "*****LocalMS_BACKUP******"
Get-LocalUser MS_BACKUP -erroraction silentlycontinue
    Start-Sleep -Seconds 2
Write-Host "*****Guest renamed******"
Get-LocalUser CISGUEST -erroraction silentlycontinue
    Start-Sleep -Seconds 2
Write-Host "*****LocalUser ADMIN Renamed******"
Get-LocalUser CISADMIN -erroraction silentlycontinue
    Start-Sleep -Seconds 2
Write-Host "*****LocalGroup Administrators******"
Get-LocalGroupMember Administrators -erroraction silentlycontinue
    Start-Sleep -Seconds 2
Write-Host "*****LocalGroup RDUsers******"
Get-LocalGroupMember "Remote Desktop Users" -erroraction silentlycontinue
    Start-Sleep -Seconds 2
Write-Host "*****LocalGroupMember Guest******"
Get-LocalGroupMember Guests -erroraction silentlycontinue
    Start-Sleep -Seconds 2
#
#Find service descriptions with CMD.EXE
Write-Host "*****show securtiy descriptions******"
Write-Host "*****show securtiy descriptions******"
    Start-Sleep -Seconds 3
"sc sdshow termservice" | cmd
Write-Host "*****SC qc termserv for StartType Auto******"
Write-Host "*****SC qc termserv for StartType Auto******"
    Start-Sleep -Seconds 2
"sc qc termservice" | cmd #looking for auto in START_TYPE
#
#Find Services running
Write-Host "*****Find Service running termservice******" ;Get-Service termservice | Where-Object {$_.Status -contains 'Running'}
#
    Start-Sleep -Seconds 5
# What service that is running has StartMode equal to auto
Write-Host "*****Services running in StartMode******" ;Get-WmiObject -Class Win32_SystemDriver | Where-Object -FilterScript {$_.State -eq "Running"} | Where-Object -FilterScript {$_.StartMode -eq "Auto"}
#
    Start-Sleep -Seconds 5
#Only Windows10 Firewall
Write-Host "*****Checking Firewall_Profile WIN10******" ;Get-NetFirewallProfile
#
#Find firewall on or off for older Windows
#Reg Query 'HKEY_LOCAL_MACHINE:\SYSTEM\CurrentControlSet\Services\SharedAccessParametersFirewallPolicyStandardProfile'
#
Write-Host "*****Find Windows Defender Exclusions path******"
Write-Host "*****Find Windows Defender Exclusions path******"
    Start-Sleep -Seconds 3
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows Defender\Exclusions\Paths' -erroraction silentlycontinue
#
    Start-Sleep -Seconds 5
#Find the Alternate data stream (desktop.ini)
#cd %UserProfile%\Desktop; Get-Item -Stream * *.txt
#cd c:\Users\moorej1; Get-Item -Stream * *.txt
#
#Find if wdigest is turned on (1) Plaintext creds in Memory
#reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest" /v UseLogonCredential
Write-Host "*****Wdigest useLogonCreds******"
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest'UseLogonCredential -ErrorAction SilentlyContinue
#
Write-Host "*****Find Files with CMD.EXE*****"
Write-Host "*****Find Files with CMD.EXE*****"
    Start-Sleep -Seconds 3
#"dir /b /s C:\IPS_*.txt 2> nul" | cmd
"dir /b /s C:\comp*.txt 2> nul" | cmd
    Start-Sleep -Second 3
"dir /b /s C:\adm*.txt 2> nul" | cmd
    Start-Sleep -Second 3 
"dir /b /s C:\con*.txt 2> nul" | cmd 
    Start-Sleep -Second 3
"dir /b /s C:\dom*.txt 2> nul" | cmd 
    Start-Sleep -Second 3
"dir /b /s C:\enum*.txt 2> nul" | cmd 
    Start-Sleep -Second 3
"dir /b /s C:\user*.txt 2> nul" | cmd 
    Start-Sleep -Second 3
#Find Desktop Shortcuts that might be mal (aka the notepad.exe.lnk, Document.lnk, and desktop.ini.lnk, and SETROUTE.lnk
#cd %UserProfile%\Desktop; Get-Item -Stream * *.lnk  <--This only works through cmd.exe, otherwise it wont change directories
Write-Host "*****Find .LNK files***** Win10+"
Write-Host "*****Find .LNK files Win10+*****"
    Start-Sleep -Seconds 3
Get-Item -Stream * *.lnk -ErrorAction SilentlyContinue # This works in the Regular PS cmd prompt in C:\Users\moorej1\Desktop
    Start-sleep -Seconds 3
#cd $HOME; Get-Item -Stream * *.lnk -ErrorAction SilentlyContinue
#
#Find Tools Hydra, SecretsDump, and CrackMapExec
#
#
    Start-Sleep -Seconds 3
Write-Host "*****See ScheduledTasks running Win10+*****"
Write-Host "*****See ScheduledTasks running Win10+*****"
    Start-Sleep -Seconds 3
schtasks /query
#
Start-Sleep -Seconds 3
#Get-ScheduledTask -ErrorAction SilentlyContinue
#Get-ScheduledTask -Recurse |  Where-Object { $_.Name -like "*Task*"}
#
#Get sysinternals info
Write-Host "*****Look for SysInternals such as PSEXEC*****"
    Start-Sleep -Seconds 2
reg query HKCU\SOFTWARE\Sysinternals 2> null
#
    Start-Sleep -Seconds 2
Write-Host "*****New-EventLog written about Kansa Win10+*****"
Write-Host "*****New-EventLog written about Kansa Win10+*****"
    Start-Sleep -Second 2
New-EventLog -LogName Application -Source "Kansa" -ErrorAction SilentlyContinue
    Start-Sleep -Seconds 2
Write-EventLog -LogName Application -Source "Kansa" -EntryType Information -EventId 7777 -Message "This is Kansa completing a Security Check."
    Start-Sleep -Second 2
#
Eventcreate.exe /L Application -SO "Security-Team" /T Information /ID 1 /D "This is Kansa completing a Security Check."
Write-Host "*****END OF PowerShell Script******"
Write-Host "*****END OF PowerShell Script******"
Write-Host "*****END OF PowerShell Script******"
Write-Host "*****END OF PowerShell Script******"
```

