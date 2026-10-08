# 🏠 Azure Homelab — Active Directory & Windows Server Lab

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Platform](https://img.shields.io/badge/Platform-Microsoft%20Azure-0078D4)
![OS](https://img.shields.io/badge/OS-Windows%20Server%202022-blue)

**Author:** Sikai  
**Platform:** Microsoft Azure  
**Last Updated:** October 2026  
**Status:** 🟢 Active

> A fully documented homelab environment built on Microsoft Azure, featuring a Windows Server 2022 Domain Controller with Active Directory Domain Services (AD DS), custom Organizational Unit (OU) hierarchy, Network Security Group (NSG) hardening, and domain-joined client management.

---

## 📋 Table of Contents

1. [Lab Overview](#-lab-overview)
2. [Architecture Diagram](#-architecture-diagram)
3. [Virtual Machine Configuration](#-virtual-machine-configuration)
4. [NIC Configuration](#-network-interface-card-nic-configuration)
5. [NSG Rules](#-network-security-group-nsg-rules)
6. [Active Directory Domain Services Setup](#-active-directory-domain-services-setup)
7. [Organizational Unit Structure](#-organizational-unit-ou-structure)
8. [Domain Admin Configuration](#-domain-admin-configuration)
9. [Screenshots](#-screenshots)
10. [Repository Structure](#-repository-structure)
11. [Skills Demonstrated](#-skills-demonstrated)
12. [Next Steps](#-notes--next-steps)

---

## 🔍 Lab Overview

This homelab replicates a real-world enterprise identity and infrastructure environment on Microsoft Azure. The lab covers the end-to-end deployment of a Windows Server Domain Controller, Active Directory configuration, OU design, and access control — skills directly applicable to IT administration, help desk, and cloud engineering roles.

**Key Technologies:**
- Microsoft Azure (Virtual Machines, VNet, NSG)
- Windows Server 2022
- Active Directory Domain Services (AD DS)
- DNS Server Role
- Remote Desktop Protocol (RDP)
- PowerShell

---

## 🗺️ Architecture Diagram


---

## 💻 Virtual Machine Configuration

| Property | Value |
|---|---|
| VM Name | AD-DC01 |
| Operating System | Windows Server 2022 Datacenter |
| VM Size | Standard B2s (2 vCPUs, 4 GiB memory) |
| Region | East US |
| Resource Group | HomeLabRG |
| Image | Windows Server 2022 Datacenter — Gen2 |
| Disk Type | Standard SSD |
| Public IP | Assigned (Dynamic) |
| Private IP | 10.0.0.4 (Static) |

![VM Overview](screenshots/01-vm-overview.png)
*Figure 1: Azure VM Overview Blade*

---

## 🌐 Network Interface Card (NIC) Configuration

| Property | Value |
|---|---|
| NIC Name | homelab-nic |
| Virtual Network | HomeLab-VNet |
| Subnet | default (10.0.0.0/24) |
| Private IP Assignment | Static — 10.0.0.4 |
| Public IP | vmpublic01 (Assigned) |
| Accelerated Networking | Disabled |
| IP Forwarding | Disabled |

![NIC Configuration](screenshots/02-nic-config.png)
*Figure 2: Network Interface Card Configuration*

---

## 🔒 Network Security Group (NSG) Rules

| Priority | Name | Port | Protocol | Source | Action |
|---|---|---|---|---|---|
| 300 | RDP | 3389 | TCP | My IP | Allow |
| 310 | HTTP | 80 | TCP | Any | Allow |
| 320 | HTTPS | 443 | TCP | Any | Allow |
| 65000 | AllowVnetInBound | Any | Any | VirtualNetwork | Allow |
| 65001 | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Allow |
| 65500 | DenyAllInBound | Any | Any | Any | Deny |

> ⚠️ **Security Note:** In a production environment, RDP (3389) should be restricted to a specific IP or protected behind Azure Bastion.

![NSG Rules](screenshots/03-nsg-rules.png)
*Figure 3: NSG Inbound Security Rules*

---

## 🏛️ Active Directory Domain Services Setup

### Steps Performed

1. Deployed Windows Server 2022 VM in Azure
2. Assigned a **static private IP** via NIC settings
3. Installed **AD DS role** via Server Manager → Add Roles and Features
4. **Promoted server to Domain Controller** using the AD DS Configuration Wizard
5. Created a new forest with root domain `homelab.local`
6. DNS Server role automatically installed alongside AD DS
7. Rebooted — domain controller promotion confirmed

### Domain Configuration

| Property | Value |
|---|---|
| Domain Name | homelab.local |
| Forest Functional Level | Windows Server 2016 |
| Domain Functional Level | Windows Server 2016 |
| DNS Server | Installed on DC |
| Global Catalog | Yes (default) |

![AD DS Installation](screenshots/04-adds-install.png)
*Figure 4: AD DS Role Installation — Server Manager*

![DC Promotion](screenshots/05-dc-promotion.png)
*Figure 5: Domain Controller Promotion Wizard*

---

## 🗂️ Organizational Unit (OU) Structure


> 📝 The underscore prefix (`_`) floats custom OUs to the top of the ADUC tree, separating them from default Microsoft containers.

![OU Structure](screenshots/06-ou-structure.png)
*Figure 6: OU Hierarchy in Active Directory Users and Computers*

---

## 👑 Domain Admin Configuration

| Property | Value |
|---|---|
| Account | a-sikai |
| Account Type | Domain User (elevated) |
| Group Membership | Domain Admins |
| OU Location | _ADMINS |
| UPN | a-sikai@homelab.local |
| Account Status | Enabled |

### Steps Performed

1. Opened **Active Directory Users and Computers (ADUC)**
2. Navigated to the `_ADMINS` OU
3. Created new user with `a-[username]` naming convention
4. Right-clicked user → **Properties** → **Member Of** → **Add** → `Domain Admins` → **OK**
5. Confirmed group membership

![Domain Admin Membership](screenshots/07-domain-admin.png)
*Figure 7: Domain Admins Group — Members Tab*

![Admin User Properties](screenshots/08-admin-user.png)
*Figure 8: Admin User Account Properties*

---

## 📸 Screenshots

| # | Filename | Description |
|---|---|---|
| 1 | `01-vm-overview.png` | Azure VM overview blade |
| 2 | `02-nic-config.png` | NIC IP configuration |
| 3 | `03-nsg-rules.png` | NSG inbound security rules |
| 4 | `04-adds-install.png` | AD DS role installation |
| 5 | `05-dc-promotion.png` | DC promotion wizard |
| 6 | `06-ou-structure.png` | OU hierarchy in ADUC |
| 7 | `07-domain-admin.png` | Domain Admins group membership |
| 8 | `08-admin-user.png` | Admin user account properties |
| 9 | `09-server-manager.png` | Server Manager dashboard |
| 10 | `10-aduc-overview.png` | ADUC full domain view |

![Server Manager](screenshots/09-server-manager.png)
*Figure 9: Server Manager Dashboard Post-Install*

![ADUC Overview](screenshots/10-aduc-overview.png)
*Figure 10: ADUC Full Domain Tree View*

---

## 📁 Repository Structure


---

## 🛠️ Skills Demonstrated

| Category | Skills |
|---|---|
| Cloud Infrastructure | Azure VM, VNet, NSG, static IP, public IP |
| Windows Server | Server Manager, role installation, post-deployment config |
| Active Directory | AD DS, DC promotion, forest/domain creation |
| Identity & Access | OU design, Domain Admin accounts, group membership |
| DNS | AD-integrated DNS, forward lookup zones |
| Security | NSG hardening, least privilege, RDP restriction |
| Documentation | GitHub README, Markdown, infrastructure diagrams |

---

## 📌 Notes & Next Steps

- [ ] Join a Windows 10/11 client VM to the domain
- [ ] Configure Group Policy Objects (GPOs)
- [ ] Set up a second DC for redundancy
- [ ] Implement Azure Bastion for RDP without public IP exposure
- [ ] Add PowerShell automation scripts to `/scripts`
- [ ] Configure DHCP role on the DC
- [ ] Create bulk user provisioning script with PowerShell

---

## 📬 Contact

**Sikai**  
📧 Sikaisellers11@gmail.com  
🔗 GitHub: [sikaiasellers](https://github.com/sikaiasellers)

---

*This homelab was built for learning, portfolio demonstration, and skill development in enterprise IT environments.*

