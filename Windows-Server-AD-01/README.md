# Project 1: Secure Enterprise Infrastructure & SOC Lab Deployment

##  Overview
This project involves the end-to-end installation and configuration of **Windows Server 2022 Data Center** to serve as the primary Domain Controller (DC-022) for a secure hybrid enterprise environment.

##  System Specifications & Resources
| Feature | Details |
| :--- | :--- |
| **Operating System** | Windows Server 2022 Data Center |
| **Hostname** | DC-022 |
| **RAM** | 2 GB (Optimized for 20 users) |
| **CPU** | 2 Cores |
| **Storage** | 40 GB SATA |

---

##  Phase 1: Installation & Troubleshooting
During the initial setup in VMware Workstation, a critical issue was encountered that prevented the OS installation.

###  The Problem: "No Images Available"
Upon reaching the operating system selection screen, the installer displayed an error: **"No images are available"**. This was likely due to a conflict with the virtual hard disk controller type or the initial disk configuration.

> **Visual Evidence of Error:**
> ![OS Image Error](./Screenshots/Scr_Image_problem.png)

###  The Solution: Disk Reconfiguration
To resolve this, I performed the following troubleshooting steps:
1. **Hard Disk Removal:** Deleted the existing virtual hard disk from the VM settings.
2. **Floppy Removal:** Removed the virtual Floppy drive and the associated autoinst.flp file from the VM settings.
3. **New Disk Creation:** Added a new Hard Disk via the **Add Hardware Wizard**.
4. **Controller Type:** Specifically selected **SATA** to ensure compatibility.
5. **Capacity & Allocation:** Set a **40 GB** maximum disk size and chose to **split the virtual disk into multiple files** for better performance and portability.

> **Troubleshooting Steps Captured:**
> ![Default VM settings](./Screenshots/Scr_Default_VM_settings.png)
> ![VM settings after removing Hard Disk & Floppy](./Screenshots/VM_settings_after_removing_Hard_Disk_&_Floppy.png)
> ![Adding New Disk](./Screenshots/Scr_add_hard_disk_1.png)
> ![Selecting SATA](./Screenshots/Scr_add_hard_disk_2.png)
> ![Final Disk Setup](./Screenshots/Scr_add_hard_disk_4.png)

###  Result
After reconfiguring the disk hardware, the installer successfully identified the drive, allowing the installation to proceed.
Finally the problem was fixed
> ![The result](./Screenshots/Scr_Probem_Fixed.png)

###  The installation was completed successfully
The installation was completed
> ![Windows Server 2022 Desktop](./Screenshots/Scr_Windows_Server_Desktop.png)

---

##  Phase 2: Network & DNS Configuration
[cite_start]After a successful OS installation, the server was hardened with a static network identity to support **Active Directory Domain Services (AD DS)** and **DNS**.

### IP Addressing Plan:
- **Static IP:** `10.0.0.10`
- **Subnet Mask:** `255.0.0.0`
- **Default Gateway:** `10.0.0.1`
- **Preferred DNS:** `10.0.0.10` (Primary for AD DS resolution)

> **Network Configuration Screenshot:**
> ![IPv4 Setup](./Screenshots/static-ip.png)

---

##  Phase 3: Domain Services Implementation
Successfully initiated the deployment of core identity services.

- **Roles Selected:** AD DS & DNS Server.
- **Implementation Method:** Simultaneous installation for integrated zone management.

> **Role Selection Screenshot:**
![AD DS & DNS Installation](./Screenshots/AD_X.png)

###  Role Installation Success
The core services have been successfully installed on **DC-2022**.

* **Services Installed:** AD DS, DNS Server, and Group Policy Management.
* **Status:** Installation succeeded, pending promotion to Domain Controller.

> **Evidence:**
> ![Installation Progress](./Screenshots/AD_Z.png)

##  Forest Creation & Infrastructure Naming
To align with enterprise standards, I have opted for a dedicated internal namespace.

* **Root Domain Name:** `SOC.local`
* **Functional Level:** Windows Server 2016 (Ensuring modern security features like Protected Users Group).
* **Design Choice:** Used `.local` to ensure a completely isolated environment, mimicking a secure corporate internal network (Intranet).

> **Configuration Screenshot:**
> ![New Forest Setup](./Screenshots/AD_11.png)

###  Prerequisites Verification
Before final promotion, the system performed a mandatory check. 
- **Result:** All checks passed successfully.
- **Note on Warnings:** DNS Delegation warning is expected as this is the first root DNS server in a new forest.

> **Verification Evidence:**
> ![Prerequisites Check](./Screenshots/AD_17.png)

##  Phase 3 Complete: Domain Controller Live
The server **DC-2022** is now the primary Domain Controller for the `SOC.local` forest.

###  Key Implementation Details:
* **Domain Name:** SOC.local (Internal Infrastructure).
* **Active Roles:** AD DS, DNS Server, and Group Policy Management.
* **Functional Level:** Windows Server 2016 (Optimized for compatibility and security features).

###  Post-Installation Verification:
The Server Manager dashboard confirms all services are operational and healthy.
![Domain Controller Status](./Screenshots/AD_19.png)

To verify the integrity of the Domain Controller, I executed a service health check via PowerShell:
- **Command:** `Get-Service adws, kdc, netlogon, dns`
- **Result:** All critical identity services are confirmed **Running**.

> **Verification Screenshot:**
> ![Active Directory Services Status](./Screenshots/AD_20.png)

##  Phase 4: Directory Services & Objects Management
Designed and implemented a professional Active Directory hierarchy to emulate a real-world enterprise environment.

###  Organizational Structure
Created a structured hierarchy under the root OU `SOCOT_Corp` to ensure granular control over network objects:
- **Admin-Users:** Reserved for high-privileged accounts.
- **Standard-Users:** Contains general staff accounts for security simulation.
- **Groups:** Centralized security and distribution group management.
- **Workstations:** Dedicated container for domain-joined assets.

###  User Provisioning
Populated the directory with multiple user objects to simulate a busy corporate network, facilitating future security testing and auditing.

> **Final Directory Structure & User List:**
> ![OU & User Management](./Screenshots/Ad_24.png)

##  Phase 5: Network Services Optimization (DNS)
Fine-tuned the DNS infrastructure to support external connectivity and advanced security auditing.

- **DNS Forwarding:** Configured Google DNS (8.8.8.8) as a forwarder to enable controlled internet access for domain assets.
- **Reverse Lookup Zone:** Implemented the `0.0.10.in-addr.arpa` zone. This is critical for mapping IP addresses back to hostnames, which is essential for investigative tasks and SIEM log enrichment.

> **DNS Configuration View:**
> ![DNS Forwarding](./Screenshots/AD_28.png)
> ![Reverse Lookup Zone](./Screenshots/AD_31.png)

##  Phase 6: Role-Based Access Control (RBAC) Implementation

To mimic a real-world enterprise security posture, I have implemented a strict **Group-Based Access Control** system. This ensures that permissions are managed at the group level rather than individual assignments, facilitating easier auditing and scalability.

###  Group Strategy
I have established specialized Security Groups within the `Groups` OU to enforce the Principle of Least Privilege (PoLP):
* **`GRP_IT_Admins`**: Full administrative control over the domain infrastructure.
* **`GRP_Security_Auditors`**: Configured with Read-only access for monitoring and centralized log collection—a critical role for future SOC operations and SIEM integration.
* **Departmental Groups (`GRP_HR_Users`, `GRP_Sales_Users`)**: Isolated groups created to manage standard users based on their corporate roles.

###  Verification
The "Active Directory Users and Computers" console confirms that the logical structure is correctly populated, with users (e.g., Ahmed, Amine, Kareem) organized within their respective departments and linked to their security groups.

> **Administrative Structure Preview:**
> ![Final Administrative Structure](./Screenshots/AD34.png)

##  Phase 7: Perimeter Security & Network Segmentation (pfSense)

To transition from a basic lab to a high-fidelity enterprise environment, I implemented a **Three-Tier Network Architecture**. This phase focuses on isolating critical infrastructure from external threats and adversary simulation zones using **pfSense** as the core security gateway.

###  Network Design & Segmentation
Using VMware Global LAN Segments, I enforced strict hardware-level isolation to emulate real-world security zones:
* **WAN (Internet):** Interfaces with the host machine via NAT to simulate an ISP uplink.
* **Corporate_Network (LAN):** A private, isolated segment dedicated to the Windows Server (DC-022) and authorized workstations.
* **Attacker_Zone (OPT1):** A restricted, isolated segment for adversary simulation tools (e.g., Kali Linux), preventing lateral movement to the production environment.



#  Building a Multi-Segmented SOC Lab: pfSense (FreeBSD) Deployment Phase

---

##  Phase 8: Virtual Infrastructure & Architecture
The firewall is provisioned on VMware Workstation using a FreeBSD-based guest operating system.

### pfSense Node Specifications:
* **Memory:** 1 GB
* **Processor:** 1 Core
* **Storage:** 20 GB
* **Network Adapters:** 1. **Adapter 1 (WAN):** NAT (ISP Simulation / Updates)
    2. **Adapter 2 (LAN):** LAN Segment (Corporate_Network)
    3. **Adapter 3 (OPT1):** LAN Segment (Attacker_Zone)

### Interface Mapping & Addressing:
| Interface | Assigned Name | Mapping | IP Address | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **em0** | WAN | NAT | 192.168.149.129/24 | ISP Simulation / Updates |
| **em1** | LAN | Corporate_Network | 10.0.0.1/8 | Domain Controller Gateway |
| **em2** | OPT1 | Attacker_Zone | 172.16.0.1/24 | Security Testing Isolation |

---

##  Phase 9: Installation & Advanced Troubleshooting

### 1. Environment Provisioning
* Defined global LAN segments in VMware to act as virtual switches for network isolation.
* Reconfigured asset `DC-022` (Windows Server 2022) to reside exclusively within the `Corporate_Network` segment.

### 2. Resolving the "Pager Read Error" 
Immediately following the FreeBSD-based installation, the system encountered a `vm_fault: pager read error` and failed to stabilize.

* **The Problem:** A hardware state conflict occurred where the system failed to correctly hand off the boot process from the ISO installer to the virtual disk.
* **The Resolution Path:** 1. Performed a hard power cycle of the virtual machine.
    2. Refreshed the hardware connection settings for the virtual CD/DVD and disk drives.
    3. Successfully stabilized the boot process, allowing **pfSense 2.8.1-RELEASE (amd64)** to initialize.

---

##  Phase 10: Console-Level Configuration

After stabilization, the FreeBSD-based console menu was used to map and configure the three network interfaces.

### A. Interface Mapping
Mapped logical pfSense interfaces to physical FreeBSD drivers:
* **WAN** -> `em0`
* **LAN** -> `em1`
* **OPT1** -> `em2`

### B. Adversarial Zone (OPT1) Setup
Static IP and DHCP services were manually enabled for the OPT1 segment to automate attacker machine connectivity:
* **Static IPv4:** `172.16.0.1/24`.
* **DHCP Range:** `172.16.0.10` to `172.16.0.50`.
* **WebConfigurator:** Defaulted to HTTPS via port 443.

---

##  Phase 11: Connectivity & Segment Validation

Connectivity was verified from the **Windows Server 2022** (DC-022) instance located in the LAN segment to the firewall gateway.

![Ping Success](image_2eb229.png)
*Figure 1: Verified ICMP reply from 10.0.0.1 with <1ms latency confirming successful server isolation and gateway routing.*

---

##  Phase 12: Initial WebConfigurator Wizard

The management interface was accessed via `https://10.0.0.1` on the internal network.

### Initial Wizard Parameters:
* **Hostname:** `Firewall`
* **Domain:** `SOC.local`
* **Primary DNS:** `8.8.8.8`

---

## 🚀 Next Steps
- [ ] Complete the Web GUI Wizard.
- [ ] **WAN Hardening:** Uncheck "Block Private Networks" for NAT compatibility.
- [ ] **Firewall Policies:** Define rules for cross-segment traffic.
