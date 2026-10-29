# 🔐 Cybersecurity Lab Environment Setup

*Building an isolated virtual lab for penetration testing and ethical hacking practice*

[![VirtualBox](https://img.shields.io/badge/VirtualBox-7.x-blue?style=for-the-badge&logo=virtualbox)](https://www.virtualbox.org/)
[![Kali Linux](https://img.shields.io/badge/Kali_Linux-2024.x-557C94?style=for-the-badge&logo=kalilinux)](https://www.kali.org/)
[![Networking](https://img.shields.io/badge/NAT_Network-10.0.0.0%2F24-critical?style=for-the-badge)](https://en.wikipedia.org/wiki/Network_address_translation)
[![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)]()

---

## 📌 Project Overview
This project focuses on setting up a **virtual cybersecurity and penetration-testing laboratory** using VirtualBox and Kali Linux. 

The purpose of the lab is to establish a secure, controlled sandbox where security assessment tools, network mapping, vulnerability testing, and ethical hacking techniques can be practiced safely without risking production networks.

The environment is built on a private virtual NAT network, allowing target machines and additional virtual assets to be seamlessly introduced in upcoming milestones.

---

## 🎯 Objectives
* Install and configure Oracle VM VirtualBox.
* Import and configure Kali Linux as a specialized testing VM.
* Create and configure an isolated **NAT Network**.
* Assign and verify a static IP address for the Kali machine.
* Verify outbound connectivity and DNS resolution.
* Create a clean baseline snapshot for quick disaster recovery.
* Document configuration, validation steps, and troubleshooting.

---

## 🛡️ Purpose of the Lab
This isolated environment serves as the foundational testing bed for:
* Network reconnaissance and host discovery
* Port scanning and service enumeration
* Vulnerability assessment and scanning
* Packet analysis and traffic monitoring
* Security tooling experimentation and scripting

> ⚠️ **Important:** This lab is strictly configured for authorized educational and self-hosted testing purposes. Never scan or attack unauthorized targets or external infrastructure.

---

## 🏗️ Lab Architecture
![Lab Topology](1-screenshot-title-image.png)

*The isolated NAT Network allows both external package management/updates and intra-lab communication for future vulnerable machines.*

---

## ⚙️ Lab Configuration

| 🧩 Component | ⚙️ Configuration |
| :--- | :--- |
| 🖥️ Hypervisor | VirtualBox 7.x |
| 🐉 Security OS | Kali Linux |
| 🧠 Kali Allocated RAM | 2048 MB (2 GB) |
| 🌐 Virtual Network Mode | NAT Network |
| 📡 Network CIDR | `10.0.0.0/24` |
| 🐧 Kali Static IP | `10.0.0.2/24` |
| 🚪 Default Gateway | `10.0.0.1` |
| 🌍 Preferred DNS | `8.8.8.8` |
| 📁 Shared Folder | Host `/downloads` (Auto-mount enabled) |
| 📋 Clipboard / Drag & Drop | Bidirectional |
| 🔮 Future Target Range | `10.0.0.3` – `10.0.0.99` |

---

# 🪜 Lab Setup Procedure

### Step 1. Prepare Environment & Hypervisor
* Oracle VM VirtualBox was installed on the host system.
* Virtualization support (Intel VT-x / AMD-V) was enabled in the host BIOS/UEFI.

### Step 2. Create the NAT Network
A private virtual network was defined in VirtualBox's Network Manager:
* **Network Name:** `NatNetwork`
* **IPv4 Prefix:** `10.0.0.0/24`
* **DHCP:** Enabled
* **IPv6:** Disabled

![VirtualBox NAT Network](2-screenshot-network-settings-1.png)

### Step 3. Import Kali Linux VM
* Kali Linux virtual appliance was imported into VirtualBox.
* Assigned **2048 MB RAM** and **2 vCPUs**.
* **General > Advanced:** Set Shared Clipboard and Drag'n'Drop to **Bidirectional**.
* **Shared Folders:** Added host `/downloads` mapped to `downloads` with Auto-mount enabled.
* **Network Adapter 1:** Attached to **NAT Network**, selecting `NatNetwork`.

![Kali VM Settings](3-screenshot-kali-linux.png)

### Step 4. Configure Kali Network Settings
Configured a persistent static IP address on `eth0`:
* **IPv4 Address:** `10.0.0.2`
* **Subnet Mask:** `255.255.255.0` (`/24`)
* **Default Gateway:** `10.0.0.1`
* **DNS:** `8.8.8.8`

Applied via NetworkManager CLI:
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.addresses 10.0.0.2/24
sudo nmcli connection modify "Wired connection 1" ipv4.gateway 10.0.0.1
sudo nmcli connection modify "Wired connection 1" ipv4.dns "10.0.0.1"
sudo nmcli connection modify "Wired connection 1" ipv4.method manual
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
Step 5. Create a Clean VM Baseline SnapshotSnapshot Name: Clean Kali - Network SetupCreated immediately after confirming connectivity to ensure a known-good restore point before performing security assessments or tool installs.🔎 Lab Verification✅ Test🧾 Command🎯 Expected Result🌐 Check IP Assignmentip aInterface eth0 displays 10.0.0.2/24📡 Test Gateway Reachabilityping -c 4 10.0.0.10% packet loss🌍 Test External Reachabilityping -c 4 8.8.8.80% packet loss🔎 Test DNS Resolutionnslookup networkwalks.comResolves domain IP🧰 Verify Security Toolingnmap --versionNmap version successfully displayed🐞 Problems Encountered & SolutionsProblem 1: VirtualBox GUI Hiding Network ConfigurationIssue: VirtualBox 7 was initially set to Basic Mode, which suppressed the NAT Network management interface.Resolution: Navigated to File > Preferences > Interface / Experience Mode, switched the setting from Basic to Expert Mode, and opened the Network Manager directly from the Tools panel.Problem 2: Internet Connectivity Lost After Static IP ConfigurationIssue: In VirtualBox 7 with recent Kali Linux builds, duplicate address detection (DAD) timeout conflicts can cause default route stalls when assigning manual IPv4 addresses.Resolution: Adjusted the connection settings using NetworkManager CLI:Bashsudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection up "Wired connection 1"
Outbound connectivity and name resolution resumed immediately.💡 What I LearnedNAT vs. NAT Network: A standard NAT adapter isolates each VM into its own private sandbox behind the host, preventing inter-VM communication. A NAT Network creates a shared virtual switch where all connected lab machines can communicate with each other while maintaining outbound access.Deterministic Static Addressing: Setting predictable static IP schemes simplifies documentation, port scanning, and routing during penetration tests.Snapshot Management: Establishing a clean initial snapshot guarantees an instant recovery mechanism without needing to reinstall or reconfigure the OS from scratch.🔗 Tools & ResourcesVirtualBoxKali Linux Downloads7-Zip👤 AuthorDevangi — Aspiring Cybersecurity ProfessionalProgram: Cybersecurity at Networkwalks (Batch B083)
