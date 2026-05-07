# Project 10 – Virtualized Cybersecurity Home Lab with Active Directory, Windows Server 2022, and Kali Linux

![VirtualBox](https://img.shields.io/badge/Oracle-VirtualBox-183A61?style=flat&logo=virtualbox&logoColor=white)
![Windows Server](https://img.shields.io/badge/Microsoft-Windows%20Server%202022-0078D4?style=flat&logo=windows&logoColor=white)
![Kali Linux](https://img.shields.io/badge/Kali-Linux-557C94?style=flat&logo=kalilinux&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)
![Domain](https://img.shields.io/badge/Domain-Home%20Lab%20%7C%20Active%20Directory%20%7C%20VM%20Networking%20%7C%20Cybersecurity%20Foundation-blueviolet)

---

## Overview

This project documents the build of a fully functional virtualized cybersecurity home lab from scratch following the **MyDFIR 3-part YouTube series**. The lab includes a **Windows Server 2022 Domain Controller** with Active Directory Domain Services, a **Kali Linux** attacker machine, and internal Host-Only networking between both VMs — creating a safe, self-contained environment for future security research and practice. This lab serves as the foundation for all future offensive and defensive cybersecurity projects.

---

## Environment

| Tool | Purpose |
|------|---------|
| Oracle VirtualBox | VM hypervisor and network configuration |
| Windows Server 2022 (Evaluation) | Domain Controller with Active Directory |
| Kali Linux 2026.1 | Attacker and security testing machine |
| Active Directory Domain Services | Domain management, OUs, users, and groups |
| VirtualBox Host-Only Networking | Isolated internal network between VMs |
| GitHub | Documentation and version control |

---

## Lab Architecture

### Virtual Machines

| VM | OS | RAM | Disk | Role |
|----|-----|-----|------|------|
| WindowsServer2022 | Windows Server 2022 (64-bit) | 4096 MB | 60 GB | Domain Controller |
| kali-linux-2026.1 | Kali Linux | 2048 MB | 20 GB | Attacker Machine |

### Network Configuration

| VM | Adapter 1 | Adapter 2 | IP (Host-Only) |
|----|-----------|-----------|----------------|
| WindowsServer2022 | NAT | Host-Only | 192.168.56.101 |
| Kali Linux | NAT | Host-Only | 192.168.56.102 |

### Active Directory Design

| Component | Value |
|-----------|-------|
| Domain Name | lab.local |
| Organizational Unit | Employees |
| Users Created | Allen Garcia, David Carter, Jaden Miller, Nasir Banks, Sydney Jones |

---

## Build Walkthrough

---

### 🟡 Step 1 — Provisioned Windows Server 2022 VM in VirtualBox

Created a new Virtual Machine in VirtualBox for Windows Server 2022. Configured hardware allocation — 4096 MB RAM, 2 processors, 60 GB dynamic hard disk — and mounted the Windows Server 2022 Evaluation ISO. Completed the Windows Server installation with Desktop Experience. Confirmed the VM summary before finalizing the build.

**VM Configuration:**
- VM Name: WindowsServer2022
- Guest OS: Windows Server 2022 (64-bit)
- Base Memory: 4096 MB
- Processors: 2
- Hard Disk: 60 GB

![VM Summary Config](vm-summary-config.png)
*VirtualBox New VM Summary — WindowsServer2022 configured with 4GB RAM, 2 CPUs, 60GB disk*

---

### 🔵 Step 2 — Installed Active Directory Domain Services

Opened Server Manager on the Windows Server 2022 VM and launched the Add Roles and Features Wizard. Selected Active Directory Domain Services, Group Policy Management, and all required Remote Server Administration Tools. Installation completed successfully on WIN-SKL2R87SUMP. Promoted the server to a Domain Controller with domain name lab.local.

**Roles Installed:**
- Active Directory Domain Services
- Group Policy Management
- AD DS and AD LDS Tools
- Active Directory module for Windows PowerShell
- Active Directory Administrative Center
- AD DS Snap-Ins and Command-Line Tools

![AD DS Installation](adds-installation.png)
*Add Roles and Features Wizard — AD DS installation completed successfully on WIN-SKL2R87SUMP*

---

### 🔵 Step 3 — Created OU and User Accounts in Active Directory

Opened Active Directory Users and Computers and created an Organizational Unit named **Employees** under lab.local. Created five fictional user accounts within the OU — Allen Garcia, David Carter, Jaden Miller, Nasir Banks, and Sydney Jones — each configured with passwords and User type confirmed.

![AD Users and Computers](ad-users-computers.png)
*Active Directory Users and Computers — Employees OU with 5 user accounts confirmed under lab.local*

---

### 🟠 Step 4 — Configured Host-Only Network Adapter on Windows Server VM

Navigated to VirtualBox Settings > Network for the WindowsServer2022 VM and enabled Adapter 2 as a Host-Only Adapter attached to the VirtualBox Host-Only Ethernet Adapter. This creates the isolated internal network segment that allows the two VMs to communicate directly without internet exposure.

![Windows Server Network Config](windows-server-network-config.png)
*WindowsServer2022 VirtualBox Network Settings — Adapter 2 configured as Host-Only Ethernet Adapter*

---

### 🟠 Step 5 — Configured Host-Only Network Adapter on Kali Linux VM

Applied the same Host-Only Adapter configuration to the Kali Linux VM on Adapter 2. Both VMs now share the same VirtualBox Host-Only network segment, allowing internal communication between the Domain Controller and the attacker machine.

![Kali Network Config](kali-network-config.png)
*Kali Linux VirtualBox Network Settings — Adapter 2 configured as Host-Only Ethernet Adapter matching Windows Server*

---

### 🟠 Step 6 — Verified IP Configuration on Windows Server

Ran `ipconfig` in the Windows Server Administrator Command Prompt to confirm both network adapters were active. Ethernet adapter showed the domain-assigned IPv4 address of 10.0.2.15 on the NAT interface. Ethernet 2 (Host-Only) confirmed IPv4 address of 192.168.56.101 on the internal network segment.

**Windows Server IP Addresses:**
- Ethernet (NAT): 10.0.2.15
- Ethernet 2 (Host-Only): 192.168.56.101
- DNS Suffix: lan

![Windows Server IPConfig](windows-server-ipconfig.png)
*Windows Server ipconfig — Host-Only adapter confirmed at 192.168.56.101 on internal network*

---

### 🟣 Step 7 — Verified IP Configuration on Kali Linux

Ran `ip a` on Kali Linux to confirm both network interfaces. eth0 showed the NAT address of 10.0.2.15. eth1 confirmed the Host-Only IP of 192.168.56.102 on the internal network — placing both VMs on the same 192.168.56.0/24 segment for direct communication.

**Kali Linux IP Addresses:**
- eth0 (NAT): 10.0.2.15
- eth1 (Host-Only): 192.168.56.102

![Kali IP Config](kali-ip-config.png)
*Kali Linux ip a — eth1 confirmed at 192.168.56.102 on Host-Only internal network segment*

---

### 🟢 Step 8 — Verified VM-to-VM Connectivity via Ping

Pinged the Windows Server Host-Only IP (192.168.56.101) from Kali Linux to confirm direct VM-to-VM connectivity over the internal network. All 25+ packets returned successful replies with consistent TTL of 128 and average response times under 0.5ms — confirming the internal lab network is fully operational.

**Ping Result:**
- Target: 192.168.56.101 (Windows Server Host-Only)
- Source: Kali Linux (192.168.56.102)
- Result: 100% success, TTL 128, avg ~0.25ms

![Kali Ping Windows Server](kali-ping-windows-server.png)
*Kali Linux pinging 192.168.56.101 — continuous successful replies confirming VM-to-VM connectivity*

---

### ✅ Step 9 — Confirmed Kali Linux VM Running

Confirmed Kali Linux 2026.1 desktop environment running in VirtualBox. VM fully operational and ready for future security testing, network scanning, and Active Directory attack simulation exercises.

![Kali Desktop Running](kali-desktop-running.png)
*Kali Linux 2026.1 desktop running in VirtualBox — attacker machine operational*

---

### ✅ Step 10 — Confirmed Windows Server 2022 VM Running

Confirmed Windows Server 2022 Standard Evaluation desktop running in VirtualBox. Domain Controller fully operational with Active Directory configured, users provisioned, and internal networking verified.

![Windows Server Desktop Running](windows-server-desktop-running.png)
*Windows Server 2022 Standard Evaluation desktop running in VirtualBox — Domain Controller operational*

---

## Current Lab Status

| Component | Status |
|-----------|--------|
| Windows Server 2022 VM | ✅ Running |
| Kali Linux VM | ✅ Running |
| Active Directory Domain Services | ✅ Installed and promoted |
| Domain (lab.local) | ✅ Active |
| Employees OU + Users | ✅ Created |
| Host-Only Networking | ✅ Configured on both VMs |
| VM-to-VM Connectivity | ✅ Verified via ping |
| VirtualBox Snapshots | 🔄 In progress |

---

## Skills Demonstrated

| Skill | How It Was Applied |
|-------|--------------------|
| VM Provisioning | Built and configured two VMs from ISO in VirtualBox |
| Windows Server Administration | Installed roles, promoted DC, managed Server Manager |
| Active Directory | Created domain, OU structure, and user accounts |
| VM Networking | Configured Host-Only adapters for isolated internal communication |
| Network Verification | Confirmed IP assignments and VM-to-VM connectivity via ping |
| Linux CLI | Used ip a and ping to verify Kali network configuration |
| Lab Documentation | Documented full architecture, IPs, and build steps |

---

## Lessons Learned

**A home lab is not optional — it is your proof of work.** Every cybersecurity skill worth having requires a safe place to practice it. This lab creates that environment. Active Directory attacks, SIEM log collection, network scanning, and malware analysis all require real systems to work against. Without a lab, you are studying theory. With a lab, you are building experience.

**VM networking requires intentional design.** NAT gives a VM internet access. Host-Only creates an isolated segment between VMs. Without adding the Host-Only adapter on both machines and verifying the IPs, the ping test would fail and the lab would not function. Understanding the difference between NAT, Host-Only, and Internal networking is a foundational skill for anyone building or defending a network environment.

**Active Directory is the backbone of enterprise Windows environments.** Every organization running Windows uses AD for authentication, group policy, and access control. Building it from scratch — installing AD DS, promoting the DC, creating OUs, and provisioning users — gives you direct experience with the system that attackers target most in real-world breaches. You cannot defend what you have not built.

---

## 💼 Real-World Application

Every enterprise SOC, red team, and IT operations environment runs on infrastructure similar to what is built here. Blue teamers use AD environments to practice detection and response. Red teamers use them to simulate credential attacks and lateral movement. Help desk engineers troubleshoot AD daily. Security analysts investigate AD logs in every Windows-based incident. Building this lab from scratch demonstrates the technical initiative and foundational competency that hiring managers across all cybersecurity roles look for in entry-level candidates.

---

## References

- [MyDFIR Step 1 — Home Lab Foundation](https://www.youtube.com/watch?v=kku0fVfksrk)
- [MyDFIR Step 2 — Expanding the Lab](https://www.youtube.com/watch?v=5iafC6vj7kM)
- [MyDFIR Step 3 — Finalizing the Lab](https://www.youtube.com/watch?v=-8X7Ay4YCoA)
- [Full MyDFIR Home Lab Playlist](https://www.youtube.com/playlist?list=PLG6KGSNK4PuBWmX9NykU0wnWamjxdKhDJ)
- [VirtualBox Download](https://www.virtualbox.org)
- [Windows Server 2022 Evaluation](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022)
- [Kali Linux Download](https://www.kali.org/get-kali/)
