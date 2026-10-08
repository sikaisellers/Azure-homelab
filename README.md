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
<img width="835" height="385" alt="image" src="https://github.com/user-attachments/assets/5371c96e-0c57-4123-a93f-97fc648ca2a5" />


---

## 💻 Virtual Machine Configuration

<img width="814" height="377" alt="image" src="https://github.com/user-attachments/assets/b00a7e01-5497-4156-a1d2-872f0024fdb8" />

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
<img width="640" height="443" alt="image" src="https://github.com/user-attachments/assets/0442dd08-78ed-42ab-8678-eed8c41175a2" />

---

## 🌐 Network Interface Card (NIC) Configuration
<img width="818" height="285" alt="image" src="https://github.com/user-attachments/assets/72d16e77-0d6b-4547-b452-191e45585cac" />

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
<img width="646" height="276" alt="image" src="https://github.com/user-attachments/assets/60980533-2c04-44a4-9310-e86fd3cbfd29" />

---

## 🔒 Network Security Group (NSG) Rules
<img width="817" height="243" alt="image" src="https://github.com/user-attachments/assets/4de4e6e2-0a99-4f36-835b-8357bbdfb802" />

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
<img width="638" height="309" alt="image" src="https://github.com/user-attachments/assets/a02e8955-874f-4cc8-905c-d3fcfa203c3a" />

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
<img width="817" height="242" alt="image" src="https://github.com/user-attachments/assets/42578bce-d75f-492c-8ef8-3052d4b9b4eb" />

| Property | Value |
|---|---|
| Domain Name | homelab.local |
| Forest Functional Level | Windows Server 2016 |
| Domain Functional Level | Windows Server 2016 |
| DNS Server | Installed on DC |
| Global Catalog | Yes (default) |

![AD DS Installation](screenshots/04-adds-install.png)
*Figure 4: AD DS Role Installation — Server Manager*
<img width="652" height="310" alt="image" src="https://github.com/user-attachments/assets/266fa3d6-bfb5-48dc-9ede-be97f728cef2" />

![DC Promotion](screenshots/05-dc-promotion.png)
*Figure 5: Domain Controller Promotion Wizard*
<img width="645" height="439" alt="image" src="https://github.com/user-attachments/assets/c7fd83f5-df99-426d-b807-2d5a381046fe" />

---

## 🗂️ Organizational Unit (OU) Structure
<img width="815" height="319" alt="image" src="https://github.com/user-attachments/assets/23730721-1e6e-4fe0-9f28-2beb2e022426" />


> 📝 The underscore prefix (`_`) floats custom OUs to the top of the ADUC tree, separating them from default Microsoft containers.

![OU Structure](screenshots/06-ou-structure.png)
*Figure 6: OU Hierarchy in Active Directory Users and Computers*
<img width="642" height="285" alt="image" src="https://github.com/user-attachments/assets/23f806e1-a710-4c91-810f-ad129d413eb1" />

---

## 👑 Domain Admin Configuration
<img width="818" height="246" alt="image" src="https://github.com/user-attachments/assets/4e92cb1f-ae00-488f-b0fc-448877c3ae75" />

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
<img width="646" height="301" alt="image" src="https://github.com/user-attachments/assets/c20d379e-4122-4095-a59d-06d5d24042c8" />

![Admin User Properties](screenshots/08-admin-user.png)
*Figure 8: Admin User Account Properties*
<img width="641" height="450" alt="image" src="https://github.com/user-attachments/assets/62732df4-e1d5-40b1-b28f-b32d4bfbaf2e" />

---

## 📸 Screenshots
<img width="820" height="380" alt="image" src="https://github.com/user-attachments/assets/0f0b5be7-fc8c-44b7-aa0f-647c9fdb60c1" />

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
<img width="644" height="299" alt="image" src="https://github.com/user-attachments/assets/9ac4cf00-a4dc-45ce-af5d-0e6921ef495d" />

![ADUC Overview](screenshots/10-aduc-overview.png)
*Figure 10: ADUC Full Domain Tree View*
<img width="656" height="448" alt="image" src="https://github.com/user-attachments/assets/5083bc1b-7510-4e0d-811b-f35cc37dec2e" />

---

## 📁 Repository Structure
<img width="841" height="402" alt="image" src="https://github.com/user-attachments/assets/0c3c17dd-d6f0-4099-9357-bd68fd474901" />


---

## 🛠️ Skills Demonstrated
<img width="814" height="277" alt="image" src="https://github.com/user-attachments/assets/a6c19eb7-86e1-4ac5-9758-f6439da21a7a" />

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

