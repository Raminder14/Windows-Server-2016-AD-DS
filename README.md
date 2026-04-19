# 🖥️ Windows Server 2016 — SysAdmin Lab Portfolio

> **SysAdmin Portfolio Project** | Raminder Dhillon  
> 📧 raminderjitsingh59@gmail.com | 💼 [LinkedIn](https://linkedin.com/in/raminderjeet-dhillon-220445136) | 🐙 [GitHub](https://github.com/Raminder14)

---

## 📋 Project Overview

A comprehensive Windows Server 2016 home lab built from scratch on Linux Mint using VirtualBox. This portfolio covers 12 real-world SysAdmin projects including Active Directory, DHCP, DNS, File Server, Group Policy, Remote Desktop, Backup, Audit Policy, Software Deployment, Roaming Profiles, and WSUS.

---

## 🛠️ Lab Environment

| Component | Details |
|---|---|
| Host OS | Linux Mint |
| Virtualization | Oracle VirtualBox 7.2.6 |
| Server OS | Windows Server 2016 Standard Evaluation (Desktop Experience) |
| Client OS | Windows 10 Pro |
| Server RAM | 4GB |
| Client RAM | 2GB |
| Server Disk | 50GB |
| Client Disk | 25GB |
| Server Name | WS2016-LAB |
| Client Name | DESKTOP-ICOC2JI |
| Server IP | 192.168.1.10 (Static) |
| Client IP | 192.168.1.20 (DHCP Reserved) |
| Domain | raminder.local |
| Network Type | VirtualBox Internal Network |

---

## ✅ Project 1 — Active Directory Domain Services (AD DS)

### Base Server Setup
- Installed Windows Server 2016 with Desktop Experience
- Set static IP: `192.168.1.10`, renamed to `WS2016-LAB`
- Promoted to Domain Controller
- Domain: `raminder.local`, NetBIOS: `RAMINDER`
- Domain Functional Level: Windows Server 2016
- All 5 FSMO roles on WS2016-LAB

### Organisational Units
```
raminder.local
├── IT
├── HR
├── Finance
└── Management
```

### Users (12 Total)

| OU | Users |
|---|---|
| IT | John Smith (jsmith), David Lee (dlee), Emma Davis (edavis) |
| HR | Sarah Jones (sjones), Karen White (kwhite), Paul Harris (pharris) |
| Finance | Mike Wilson (mwilson), Rachel Green (rgreen), Tom Clark (tclark) |
| Management | Lisa Brown (lbrown), James Taylor (jtaylor), Nancy Martin (nmartin) |

### Security Groups

| Group | Members |
|---|---|
| IT-Team | jsmith, dlee, edavis |
| HR-Team | sjones, kwhite, pharris |
| Finance-Team | mwilson, rgreen, tclark |
| Management-Team | lbrown, jtaylor, nmartin |

### Group Policy Objects (GPOs)

| GPO | Linked To | Purpose |
|---|---|---|
| Password-Policy | raminder.local | Strong password policy (min 8 chars, 30 day expiry) |
| Desktop-Lockdown | HR, Finance OUs | Disables Task Manager and Control Panel |

---

## ✅ Project 2 — Windows 10 Client Domain Join

- Installed Windows 10 Pro in VirtualBox
- Static IP: `192.168.1.20`, DNS: `192.168.1.10`
- Joined to `raminder.local` domain
- Verified login with `RAMINDER\jsmith`
- Logon server: `\\WS2016-LAB`
- Client visible in AD Computers container

---

## ✅ Project 3 — DHCP Server

| Setting | Value |
|---|---|
| Scope Name | LAN-Scope |
| Range | 192.168.1.50 — 192.168.1.100 |
| Exclusion | 192.168.1.1 — 192.168.1.49 |
| Gateway | 192.168.1.1 |
| DNS | 192.168.1.10 |
| Lease | 8 days |
| Reservation | 192.168.1.20 → 08-00-27-df-ad-57 |

---

## ✅ Project 4 — File Server

| Share | Path | Group | Permission |
|---|---|---|---|
| IT | C:\Shares\IT | IT-Team | Full Control |
| HR | C:\Shares\HR | HR-Team | Full Control |
| Finance | C:\Shares\Finance | Finance-Team | Full Control |
| Management | C:\Shares\Management | Management-Team | Full Control |

- Verified jsmith can write to `\\WS2016-LAB\IT` share

---

## ✅ Project 5 — DNS Deep Dive

- Reverse Lookup Zone: `1.168.192.in-addr.arpa`
- PTR Records: 192.168.1.10 → WS2016-LAB, 192.168.1.20 → DESKTOP-ICOC2JI
- Custom A Record: `fileserver.raminder.local → 192.168.1.10`
- All DNS resolution tests passed ✅

---

## ✅ Project 6 — Windows Server Backup

- Backup share: `\\WS2016-LAB\BackupShare`
- Source: `C:\Shares`
- Job completed in 1 minute with no errors
- HResult: 0 (Success)

---

## ✅ Project 7 — Remote Desktop Services (RDS)

- Enabled RDP via registry and firewall
- Disabled NLA for lab compatibility
- Fixed CredSSP encryption policy
- IT-Team added to Remote Desktop Users
- RDP connection verified from Win10-Client as jsmith

---

## ✅ Project 8 — Audit Policy

| Category | Setting |
|---|---|
| Logon | Success and Failure |
| File System | Success and Failure |
| Audit Policy Change | Success and Failure |
| User Account Management | Success and Failure |

- Verified Event IDs 4624, 4634, 4672 in Security log
- Kerberos authentication confirmed
- Network logon Type 3 recorded

---

## ✅ Project 9 — Account Lockout Policy

| Setting | Value |
|---|---|
| Lockout Threshold | 5 failed attempts |
| Lockout Duration | 30 minutes |
| Observation Window | 30 minutes |

- Tested lockout on jsmith after 5 failed attempts ✅
- Verified `LockedOut: True`, `BadLogonCount: 5` ✅
- Unlocked account with `Unlock-ADAccount` ✅

---

## ✅ Project 10 — Software Deployment via GPO

- Created Software share: `\\WS2016-LAB\Software`
- Downloaded 7-Zip MSI and placed in share
- Created **Software-Deployment** GPO linked to domain
- Configured software installation package via GPMC
- 7-Zip installed on Win10-Client at `C:\Program Files\7-Zip\`

---

## ✅ Project 11 — Roaming Profiles

- Created Roaming Profiles share: `\\WS2016-LAB\RoamingProfiles`
- Set profile path for all 12 domain users
- Profile path format: `\\WS2016-LAB\RoamingProfiles\%username%`
- Verified `jsmith.V6` profile folder created after logoff
- Profile syncs back to server on every logoff ✅

---

## ✅ Project 12 — Windows Server Update Services (WSUS)

- Installed WSUS role on WS2016-LAB
- Configured sync from Microsoft Update
- Set English-only updates
- Created **WSUS-Policy** GPO pointing clients to `http://WS2016-LAB:8530`
- WS2016-LAB registered with WSUS successfully
- Win10-Client pointed to WSUS server via GPO

---

## 💻 Key PowerShell Commands Used

### AD DS:
```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
Install-ADDSForest -DomainName "raminder.local" -DomainNetbiosName "RAMINDER" -ForestMode "WinThreshold" -DomainMode "WinThreshold" -InstallDns:$true -Force:$true
New-ADOrganizationalUnit -Name "IT" -Path "DC=raminder,DC=local"
New-ADUser -Name "John Smith" -SamAccountName "jsmith" -UserPrincipalName "jsmith@raminder.local" -Path "OU=IT,DC=raminder,DC=local" -AccountPassword (ConvertTo-SecureString "Admin@2016" -AsPlainText -Force) -Enabled $true
New-ADGroup -Name "IT-Team" -GroupScope Global -GroupCategory Security -Path "OU=IT,DC=raminder,DC=local"
Add-ADGroupMember -Identity "IT-Team" -Members "jsmith","dlee","edavis"
```

### DHCP:
```powershell
Install-WindowsFeature -Name DHCP -IncludeAllSubFeature -IncludeManagementTools
Import-Module DHCPServer
Add-DhcpServerInDC -DnsName "WS2016-LAB.raminder.local" -IPAddress 192.168.1.10
Add-DhcpServerv4Scope -Name "LAN-Scope" -StartRange 192.168.1.50 -EndRange 192.168.1.100 -SubnetMask 255.255.255.0 -State Active
Add-DhcpServerv4ExclusionRange -ScopeId 192.168.1.0 -StartRange 192.168.1.1 -EndRange 192.168.1.49
Add-DhcpServerv4Reservation -ScopeId 192.168.1.0 -IPAddress 192.168.1.20 -ClientId "08-00-27-DF-AD-57" -Description "Win10-Client Reservation"
```

### File Server:
```powershell
Install-WindowsFeature -Name FS-FileServer -IncludeManagementTools
New-SmbShare -Name "IT" -Path "C:\Shares\IT" -Description "IT Department Share"
Grant-SmbShareAccess -Name "IT" -AccountName "RAMINDER\IT-Team" -AccessRight Full -Force
```

### DNS:
```powershell
Add-DnsServerPrimaryZone -NetworkId "192.168.1.0/24" -ReplicationScope "Forest"
Add-DnsServerResourceRecordPtr -ZoneName "1.168.192.in-addr.arpa" -Name "10" -PtrDomainName "WS2016-LAB.raminder.local"
Add-DnsServerResourceRecordA -ZoneName "raminder.local" -Name "fileserver" -IPv4Address "192.168.1.10"
```

### Backup:
```powershell
Install-WindowsFeature -Name Windows-Server-Backup -IncludeManagementTools
$policy = New-WBPolicy
$fileSpec = New-WBFileSpec -FileSpec "C:\Shares"
Add-WBFileSpec -Policy $policy -FileSpec $fileSpec
$backupLocation = New-WBBackupTarget -NetworkPath "\\WS2016-LAB\BackupShare" -Credential (Get-Credential)
Add-WBBackupTarget -Policy $policy -Target $backupLocation
Start-WBBackup -Policy $policy
```

### RDP:
```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "UserAuthentication" -Value 0
net localgroup "Remote Desktop Users" "RAMINDER\IT-Team" /add
```

### Audit Policy:
```powershell
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
auditpol /set /subcategory:"User Account Management" /success:enable /failure:enable
auditpol /set /subcategory:"File System" /success:enable /failure:enable
auditpol /set /subcategory:"Audit Policy Change" /success:enable /failure:enable
Get-EventLog -LogName Security -Newest 10 | Select-Object TimeGenerated, EventID, Message | Format-List
```

### Account Lockout:
```powershell
net accounts /lockoutthreshold:5
net accounts /lockoutduration:30
net accounts /lockoutwindow:30
Get-ADUser jsmith -Properties LockedOut, BadLogonCount | Select-Object Name, LockedOut, BadLogonCount
Unlock-ADAccount -Identity jsmith
```

### Roaming Profiles:
```powershell
New-SmbShare -Name "RoamingProfiles" -Path "C:\RoamingProfiles" -FullAccess "Everyone"
$users = @("jsmith","dlee","edavis","sjones","kwhite","pharris","mwilson","rgreen","tclark","lbrown","jtaylor","nmartin")
foreach ($user in $users) {
    Set-ADUser -Identity $user -ProfilePath "\\WS2016-LAB\RoamingProfiles\$user"
}
```

### WSUS:
```powershell
Install-WindowsFeature -Name UpdateServices -IncludeManagementTools
& "C:\Program Files\Update Services\Tools\WsusUtil.exe" postinstall CONTENT_DIR=C:\WSUS
$wsus = Get-WsusServer
$wsusConfig = $wsus.GetConfiguration()
$wsusConfig.SyncFromMicrosoftUpdate = $true
$wsusConfig.Save()
Import-Module UpdateServices
Get-WsusComputer -All | Select-Object FullDomainName, IPAddress, LastSyncTime
```

---

## 📸 Screenshots

| # | Screenshot | Description |
|---|---|---|
| 01 | ![OU Structure](screenshots/01-OU-Structure.png) | OU structure in ADUC |
| 02 | ![IT OU Users](screenshots/02-Users-IT-OU.png) | Users in IT OU |
| 03 | ![HR OU Users](screenshots/03-Users-HR-OU.png) | Users in HR OU |
| 04 | ![Finance OU Users](screenshots/04-Users-Finance-OU.png) | Users in Finance OU |
| 05 | ![Management OU Users](screenshots/05-Users-Management-OU.png) | Users in Management OU |
| 06 | ![IT Group](screenshots/06-IT-Team-Group-Members.png) | IT-Team group members |
| 07 | ![HR Group](screenshots/07-HR-Team-Group-Members.png) | HR-Team group members |
| 08 | ![Finance Group](screenshots/08-Finance-Team-Group-Members.png) | Finance-Team group members |
| 09 | ![Management Group](screenshots/09-Management-Team-Group-Members.png) | Management-Team group members |
| 10 | ![Password GPO](screenshots/10-GPO-Password-Policy.png) | Password Policy GPO |
| 11 | ![Desktop GPO](screenshots/11-GPO-Desktop-Lockdown.png) | Desktop Lockdown GPO |
| 12 | ![DNS Zone](screenshots/12-DNS-Forward-Lookup-Zone.png) | DNS Forward Lookup Zone |
| 13 | ![Domain Join](screenshots/13-Win10-Domain-Join-Verified.png) | Windows 10 domain join verified |
| 14 | ![System Properties](screenshots/14-Win10-System-Properties.png) | Windows 10 system properties |
| 15 | ![AD Computers](screenshots/15-Win10-In-AD-Computers.png) | Windows 10 client in AD Computers |
| 16 | ![Domain User Login](screenshots/16-Win10-Domain-User-Login.png) | Domain user jsmith logged in |
| 17 | ![DHCP IPConfig](screenshots/17-DHCP-Win10-IPConfig.png) | Windows 10 DHCP lease confirmed |
| 18 | ![DHCP Lease](screenshots/18-DHCP-Active-Lease.png) | Active DHCP lease in DHCP Manager |
| 19 | ![DHCP Reservation](screenshots/19-DHCP-Reservation.png) | DHCP reservation for Win10 client |
| 20 | ![File Share Access](screenshots/20-FileServer-IT-Share-Access.png) | jsmith accessing IT share |
| 21 | ![Shares List](screenshots/21-FileServer-Shares-List.png) | All shares in Server Manager |
| 22 | ![NTFS Permissions](screenshots/22-FileServer-NTFS-Permissions.png) | NTFS permissions on IT folder |
| 23 | ![Reverse Lookup](screenshots/23-DNS-Reverse-Lookup-Zone.png) | Reverse Lookup Zone with PTR records |
| 24 | ![Custom A Record](screenshots/24-DNS-Custom-A-Record.png) | Custom A record in DNS Manager |
| 25 | ![DNS Test](screenshots/25-DNS-Resolution-Test.png) | DNS resolution tests in PowerShell |
| 26 | ![Backup Job](screenshots/26-Backup-Job-Completed.png) | Backup job completed successfully |
| 27 | ![Backup Files](screenshots/27-Backup-Files-Created.png) | Backup files created in BackupShare |
| 28 | ![RDP Connected](screenshots/28-RDP-Connected-To-Server.png) | RDP connection from Win10 to Server |
| 29 | ![RDP Session](screenshots/29-RDP-Active-Session.png) | Active RDP session via query session |
| 30 | ![Event Viewer](screenshots/30-Audit-Event-Viewer-Security-Log.png) | Security log in Event Viewer |
| 31 | ![Audit Policy](screenshots/31-Audit-Policy-Settings.png) | Audit policy settings in PowerShell |
| 32 | ![Lockout Policy](screenshots/32-Account-Lockout-Policy.png) | Account lockout policy settings |
| 33 | ![Account Locked](screenshots/33-Account-Locked-Out.png) | jsmith account locked after 5 attempts |
| 34 | ![Account Unlocked](screenshots/34-Account-Unlocked.png) | jsmith account unlocked |
| 35 | ![7-Zip Installed](screenshots/35-Software-7Zip-Installed.png) | 7-Zip installed via GPO |
| 36 | ![Software GPO](screenshots/36-Software-Deployment-GPO.png) | Software Deployment GPO |
| 37 | ![Roaming Profile](screenshots/37-Roaming-Profile-Server-Folder.png) | Roaming profile folder on server |
| 38 | ![Profile AD Path](screenshots/38-Roaming-Profile-AD-Path.png) | Profile path set in AD |
| 39 | ![WSUS Console](screenshots/39-WSUS-Console.png) | WSUS management console |
| 40 | ![WSUS GPO](screenshots/40-WSUS-GPO-Policy.png) | WSUS GPO policy settings |
| 41 | ![WSUS Computer](screenshots/41-WSUS-Computer-Registered.png) | Computer registered with WSUS |

---

## 📚 References
- [Server Academy](https://www.serveracademy.com/)
- [Microsoft Docs — AD DS](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)
- [Microsoft Docs — DHCP](https://docs.microsoft.com/en-us/windows-server/networking/technologies/dhcp/dhcp-top)
- [Microsoft Docs — File Server](https://docs.microsoft.com/en-us/windows-server/storage/fsrm/fsrm-overview)
- [Microsoft Docs — DNS](https://docs.microsoft.com/en-us/windows-server/networking/dns/dns-top)
- [Microsoft Docs — Windows Server Backup](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-backup/windows-server-backup)
- [Microsoft Docs — Remote Desktop](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/welcome-to-rds)
- [Microsoft Docs — Audit Policy](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/advanced-security-audit-policy-settings)
- [Microsoft Docs — WSUS](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

---

*Built as part of my SysAdmin portfolio — demonstrating hands-on experience with Windows Server 2016 in a home lab environment.*
