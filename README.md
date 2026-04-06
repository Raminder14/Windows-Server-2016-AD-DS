# 🖥️ Windows Server 2016 — Active Directory Domain Services (AD DS)

> **SysAdmin Portfolio Project** | Raminder Dhillon  
> 📧 raminderjitsingh59@gmail.com | 💼 [LinkedIn](https://linkedin.com/in/raminderjeet-dhillon-220445136) | 🐙 [GitHub](https://github.com/Raminder14)

---

## 📋 Project Overview

This project demonstrates the installation and configuration of **Active Directory Domain Services (AD DS)** on Windows Server 2016, including joining a Windows 10 client to the domain. The entire lab was built from scratch in a VirtualBox environment running on Linux Mint.

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
| Client IP | 192.168.1.20 (Static) |
| Domain | raminder.local |
| Network Type | VirtualBox Internal Network |

---

## ✅ What Was Configured

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
Created the following OU structure under `raminder.local`:

```
raminder.local
├── IT
├── HR
├── Finance
└── Management
```

### 4. Users
Created 3 users in each OU (12 users total):

| OU | Users |
|---|---|
| IT | John Smith (jsmith), David Lee (dlee), Emma Davis (edavis) |
| HR | Sarah Jones (sjones), Karen White (kwhite), Paul Harris (pharris) |
| Finance | Mike Wilson (mwilson), Rachel Green (rgreen), Tom Clark (tclark) |
| Management | Lisa Brown (lbrown), James Taylor (jtaylor), Nancy Martin (nmartin) |

### 5. Security Groups
Created one Global Security Group per OU:

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

### 8. Windows 10 Client
- Installed Windows 10 Pro in VirtualBox
- Set static IP: `192.168.1.20`
- DNS pointing to Domain Controller: `192.168.1.10`
- Successfully joined to `raminder.local` domain
- Verified domain login with user `RAMINDER\jsmith`
- Logon server confirmed as `\\WS2016-LAB`
- Client visible in AD Computers container

---

## 💻 Key PowerShell Commands Used

### Server Side:
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

# Verify DNS
Resolve-DnsName "WS2016-LAB.raminder.local"

# Verify Domain
Get-ADDomain
Get-ADDomainController
```

### Client Side:
```powershell
# Join domain
Add-Computer -DomainName "raminder.local" -Credential RAMINDER\Administrator -Restart

# Verify domain join
systeminfo | findstr /B /C:"Domain"
$env:LOGONSERVER
whoami
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
| 14 | ![System Properties](screenshots/14-Win10-System-Properties.png) | Windows 10 system properties showing domain |
| 15 | ![AD Computers](screenshots/15-Win10-In-AD-Computers.png) | Windows 10 client in AD Computers container |
| 16 | ![Domain User Login](screenshots/16-Win10-Domain-User-Login.png) | Domain user jsmith logged into Windows 10 |

---

## 📚 References
- [Server Academy](https://www.serveracademy.com/)
- [Microsoft Docs — AD DS](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)

---

*Built as part of my SysAdmin portfolio. More projects coming soon.*
