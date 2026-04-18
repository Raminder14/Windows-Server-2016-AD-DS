# 🖥️ Windows Server 2016 — SysAdmin Lab Portfolio

> **SysAdmin Portfolio Project** | Raminder Dhillon  
> 📧 raminderjitsingh59@gmail.com | 💼 [LinkedIn](https://linkedin.com/in/raminderjeet-dhillon-220445136) | 🐙 [GitHub](https://github.com/Raminder14)

---

## 📋 Project Overview

This project demonstrates the installation and configuration of a full Windows Server 2016 lab environment including Active Directory Domain Services (AD DS), Windows 10 client domain join, DHCP Server, and File Server with NTFS permissions. The entire lab was built from scratch in a VirtualBox environment running on Linux Mint.

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

### 1. Base Server Setup
- Installed Windows Server 2016 with Desktop Experience
- Set static IP address: `192.168.1.10`
- Renamed server to `WS2016-LAB`
- Installed VirtualBox Guest Additions

### 2. Active Directory Domain Services
- Installed AD DS role using PowerShell
- Promoted server to **Domain Controller**
- Created new forest and domain: `raminder.local`
- NetBIOS Name: `RAMINDER`
- Domain Functional Level: Windows Server 2016
- All 5 FSMO roles hosted on WS2016-LAB

### 3. Organisational Units (OUs)
```
raminder.local
├── IT
├── HR
├── Finance
└── Management
```

### 4. Users (12 Total)

| OU | Users |
|---|---|
| IT | John Smith (jsmith), David Lee (dlee), Emma Davis (edavis) |
| HR | Sarah Jones (sjones), Karen White (kwhite), Paul Harris (pharris) |
| Finance | Mike Wilson (mwilson), Rachel Green (rgreen), Tom Clark (tclark) |
| Management | Lisa Brown (lbrown), James Taylor (jtaylor), Nancy Martin (nmartin) |

### 5. Security Groups

| Group | Members |
|---|---|
| IT-Team | jsmith, dlee, edavis |
| HR-Team | sjones, kwhite, pharris |
| Finance-Team | mwilson, rgreen, tclark |
| Management-Team | lbrown, jtaylor, nmartin |

### 6. Group Policy Objects (GPOs)

| GPO | Linked To | Purpose |
|---|---|---|
| Password-Policy | raminder.local | Enforces strong password policy (min 8 chars, 30 day expiry) |
| Desktop-Lockdown | HR, Finance OUs | Disables Task Manager and Control Panel |

### 7. DNS
- DNS role installed automatically with AD DS
- Forward Lookup Zone: `raminder.local`
- A Record: `WS2016-LAB → 192.168.1.10`
- SRV Records: Kerberos, LDAP, Global Catalog all registered

---

## ✅ Project 2 — Windows 10 Client Domain Join

- Installed Windows 10 Pro in VirtualBox
- Set static IP: `192.168.1.20`
- DNS pointing to Domain Controller: `192.168.1.10`
- Successfully joined to `raminder.local` domain
- Verified domain login with user `RAMINDER\jsmith`
- Logon server confirmed as `\\WS2016-LAB`
- Client visible in AD Computers container

---

## ✅ Project 3 — DHCP Server

### Scope Details

| Setting | Value |
|---|---|
| Scope Name | LAN-Scope |
| Scope ID | 192.168.1.0 |
| Start Range | 192.168.1.50 |
| End Range | 192.168.1.100 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |
| DNS Server | 192.168.1.10 |
| DNS Domain | raminder.local |
| Lease Duration | 8 days |
| Exclusion Range | 192.168.1.1 — 192.168.1.49 |

### DHCP Reservation

| Setting | Value |
|---|---|
| Reserved IP | 192.168.1.20 |
| Client MAC | 08-00-27-df-ad-57 |
| Description | Win10-Client Reservation |

---

## ✅ Project 4 — File Server

### Shared Folders
Created department shares on `C:\Shares\` and shared over the network:

| Share Name | Path | Description |
|---|---|---|
| IT | C:\Shares\IT | IT Department Share |
| HR | C:\Shares\HR | HR Department Share |
| Finance | C:\Shares\Finance | Finance Department Share |
| Management | C:\Shares\Management | Management Department Share |

### NTFS Permissions
Each department group has Full Control over their own folder only:

| Folder | Group | Permission |
|---|---|---|
| C:\Shares\IT | RAMINDER\IT-Team | Full Control |
| C:\Shares\HR | RAMINDER\HR-Team | Full Control |
| C:\Shares\Finance | RAMINDER\Finance-Team | Full Control |
| C:\Shares\Management | RAMINDER\Management-Team | Full Control |

### Share Permissions
| Share | Group | Access |
|---|---|---|
| IT | RAMINDER\IT-Team | Full |
| HR | RAMINDER\HR-Team | Full |
| Finance | RAMINDER\Finance-Team | Full |
| Management | RAMINDER\Management-Team | Full |

### Access Test
- Logged into Win10-Client as `RAMINDER\jsmith` (IT-Team member)
- Successfully created file on `\\WS2016-LAB\IT` share
- Confirmed NTFS and Share permissions working correctly

---

## 💻 Key PowerShell Commands Used

### AD DS:
```powershell
# Install AD DS Role
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Promote to Domain Controller
Install-ADDSForest -DomainName "raminder.local" -DomainNetbiosName "RAMINDER" -ForestMode "WinThreshold" -DomainMode "WinThreshold" -InstallDns:$true -Force:$true

# Create OUs
New-ADOrganizationalUnit -Name "IT" -Path "DC=raminder,DC=local"

# Create Users
New-ADUser -Name "John Smith" -SamAccountName "jsmith" -UserPrincipalName "jsmith@raminder.local" -Path "OU=IT,DC=raminder,DC=local" -AccountPassword (ConvertTo-SecureString "Admin@2016" -AsPlainText -Force) -Enabled $true

# Create Security Groups
New-ADGroup -Name "IT-Team" -GroupScope Global -GroupCategory Security -Path "OU=IT,DC=raminder,DC=local"

# Add Users to Groups
Add-ADGroupMember -Identity "IT-Team" -Members "jsmith","dlee","edavis"

# Create GPO
New-GPO -Name "Password-Policy"
New-GPLink -Name "Password-Policy" -Target "DC=raminder,DC=local"
```

### DHCP Server:
```powershell
# Install DHCP Role
Install-WindowsFeature -Name DHCP -IncludeAllSubFeature -IncludeManagementTools

# Authorize DHCP in AD
Import-Module DHCPServer
Add-DhcpServerInDC -DnsName "WS2016-LAB.raminder.local" -IPAddress 192.168.1.10

# Create Scope
Add-DhcpServerv4Scope -Name "LAN-Scope" -StartRange 192.168.1.50 -EndRange 192.168.1.100 -SubnetMask 255.255.255.0 -State Active

# Set Options
Set-DhcpServerv4OptionValue -ScopeId 192.168.1.0 -Router 192.168.1.1
Set-DhcpServerv4OptionValue -ScopeId 192.168.1.0 -DnsServer 192.168.1.10 -DnsDomain "raminder.local"

# Add Exclusion and Reservation
Add-DhcpServerv4ExclusionRange -ScopeId 192.168.1.0 -StartRange 192.168.1.1 -EndRange 192.168.1.49
Add-DhcpServerv4Reservation -ScopeId 192.168.1.0 -IPAddress 192.168.1.20 -ClientId "08-00-27-DF-AD-57" -Description "Win10-Client Reservation"
```

### File Server:
```powershell
# Install File Server Role
Install-WindowsFeature -Name FS-FileServer -IncludeManagementTools

# Create Folders
New-Item -Path "C:\Shares\IT" -ItemType Directory
New-Item -Path "C:\Shares\HR" -ItemType Directory
New-Item -Path "C:\Shares\Finance" -ItemType Directory
New-Item -Path "C:\Shares\Management" -ItemType Directory

# Create Shares
New-SmbShare -Name "IT" -Path "C:\Shares\IT" -Description "IT Department Share"
New-SmbShare -Name "HR" -Path "C:\Shares\HR" -Description "HR Department Share"
New-SmbShare -Name "Finance" -Path "C:\Shares\Finance" -Description "Finance Department Share"
New-SmbShare -Name "Management" -Path "C:\Shares\Management" -Description "Management Department Share"

# Set NTFS Permissions
$acl = Get-Acl "C:\Shares\IT"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("RAMINDER\IT-Team","FullControl","ContainerInherit,ObjectInherit","None","Allow")
$acl.SetAccessRule($rule)
Set-Acl "C:\Shares\IT" $acl

# Set Share Permissions
Grant-SmbShareAccess -Name "IT" -AccountName "RAMINDER\IT-Team" -AccessRight Full -Force
Grant-SmbShareAccess -Name "HR" -AccountName "RAMINDER\HR-Team" -AccessRight Full -Force
Grant-SmbShareAccess -Name "Finance" -AccountName "RAMINDER\Finance-Team" -AccessRight Full -Force
Grant-SmbShareAccess -Name "Management" -AccountName "RAMINDER\Management-Team" -AccessRight Full -Force
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

---

## 📚 References
- [Server Academy](https://www.serveracademy.com/)
- [Microsoft Docs — AD DS](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)
- [Microsoft Docs — DHCP](https://docs.microsoft.com/en-us/windows-server/networking/technologies/dhcp/dhcp-top)
- [Microsoft Docs — File Server](https://docs.microsoft.com/en-us/windows-server/storage/fsrm/fsrm-overview)

---

*Built as part of my SysAdmin portfolio. More projects coming soon.*
