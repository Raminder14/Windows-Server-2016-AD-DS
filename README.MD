## Active Directory Structure

> **Note:** Active Directory Domain Services (AD DS) is documented here as designed. 
> Cisco Packet Tracer does not support full AD DS, so this represents the production 
> implementation that would be deployed on Windows Server 2019/2022 in a real environment.

### Domain
- **Domain Name:** auscore.local
- **NetBIOS Name:** AUSCORE
- **Domain Functional Level:** Windows Server 2016 or higher

### Domain Controllers

| Server | IP Address | Role | Site |
|---|---|---|---|
| DC1 | 10.1.100.10 | Primary DC, FSMO holder, Primary DNS | Perth HQ |
| DC2 | 10.1.100.11 | Secondary DC, Secondary DNS | Perth HQ |
| RODC-MEL | 10.2.100.10 | Read-Only Domain Controller | Melbourne |
| RODC-BNE | 10.4.100.10 | Read-Only Domain Controller | Brisbane |
| RODC-SYD | 10.3.100.10 | Read-Only Domain Controller | Sydney |

### Organisational Unit (OU) Structure
### Security Groups

One Global Security Group per department per site — 24 groups total:

| Site | Groups |
|---|---|
| Perth | Perth-IT, Perth-HR, Perth-Finance, Perth-Sales, Perth-Operations, Perth-Management |
| Melbourne | Mel-IT, Mel-HR, Mel-Finance, Mel-Sales, Mel-Operations, Mel-Management |
| Brisbane | Bne-IT, Bne-HR, Bne-Finance, Bne-Sales, Bne-Operations, Bne-Management |
| Sydney | Syd-IT, Syd-HR, Syd-Finance, Syd-Sales, Syd-Operations, Syd-Management |

### Group Policy Objects (GPOs)

| GPO Name | Scope | Settings |
|---|---|---|
| Password-Policy | Domain-wide | 12 char minimum, complexity required, 90 day expiry, 5 password history |
| Desktop-Lockdown | Finance + HR OUs | Disable USB storage, restrict Control Panel, screensaver lock 5 min |
| Software-Deployment | IT OU | Push approved admin tools and management software |
| Screensaver-Lock | All users | Auto-lock screen after 10 minutes idle |
| Drive-Mapping | All users | Auto-map H: (home folder) and S: (shared department drive) |
| Audit-Policy | Domain Controllers | Enable detailed authentication and object access logging |

### Authentication Flow

1. User logs in at workstation in any site
2. Workstation queries DNS for nearest domain controller via SRV records
3. Branch users authenticate against local RODC for low-latency login
4. RODC forwards authentication to writable DC at HQ if needed
5. DC issues Kerberos TGT (Ticket Granting Ticket) — valid for 8 hours
6. User accesses resources using TGT — no password re-entry required

### DNS Integration

AD DS is fully integrated with DNS. All A records, PTR records, and SRV records 
for AD services (LDAP, Kerberos, Global Catalog) are stored in DNS zones on 
DC1 and replicated to DC2 and all branch RODCs.
