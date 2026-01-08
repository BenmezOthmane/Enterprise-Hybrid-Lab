#  Building a Multi-Segmented SOC Lab: pfSense (FreeBSD) Deployment Phase

---

##  Phase 1: Virtual Infrastructure & Architecture
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
| **em2** | OPT1 | Attacker_Zone | 172.16.0.1/24 | Security Testing Isolation 

> ![Network Adapters](./Screenshots/PS2.png)  

##  Phase 2: Installation & Advanced Troubleshooting

### 1. Environment Provisioning
* Defined global LAN segments in VMware to act as virtual switches for network isolation.
* Reconfigured asset `DC-022` (Windows Server 2022) to reside exclusively within the `Corporate_Network` segment.

> ![LAN em1](./Screenshots/PS8.png)  

### 2. Resolving the "Pager Read Error" 
Immediately following the FreeBSD-based installation, the system encountered a `vm_fault: pager read error` and failed to stabilize.

* **The Problem:** A hardware state conflict occurred where the system failed to correctly hand off the boot process from the ISO installer to the virtual disk.
* **The Resolution Path:** 1. Performed a hard power cycle of the virtual machine.
    2. Refreshed the hardware connection settings for the virtual CD/DVD and disk drives.
    3. Successfully stabilized the boot process, allowing **pfSense 2.8.1-RELEASE (amd64)** to initialize.

> ![pager read error](./Screenshots/vmfault.png)  

##  Phase 3: Console-Level Configuration

After stabilization, the FreeBSD-based console menu was used to map and configure the three network interfaces.

### A. Interface Mapping
Mapped logical pfSense interfaces to physical FreeBSD drivers:
* **WAN** -> `em0`
* **LAN** -> `em1`
* **OPT1** -> `em2`

> ![menu](./Screenshots/menu.png)  

### B. Adversarial Zone (OPT1) Setup
Static IP and DHCP services were manually enabled for the OPT1 segment to automate attacker machine connectivity:
* **Static IPv4:** `172.16.0.1/24`.
* **DHCP Range:** `172.16.0.10` to `172.16.0.50`.
* **WebConfigurator:** Defaulted to HTTPS via port 443.

> ![range](./Screenshots/range.png)  

##  Phase 4: Connectivity & Segment Validation

Connectivity was verified from the **Windows Server 2022** (DC-022) instance located in the LAN segment to the firewall gateway.

![Ping Success](./Screenshots/PS1.png)
*Figure 1: Verified ICMP reply from 10.0.0.1 with <1ms latency confirming successful server isolation and gateway routing.*

# Phase 5: Perimeter Security & Gateway Deployment (pfSense)

In this phase, I transitioned the lab from a flat network to a **Segmented Enterprise Architecture** by deploying **pfSense (FreeBSD)**. This firewall acts as the "Protector" of the corporate network and the "Inspector" for all traffic between the lab zones.

### Initial Wizard Parameters:
To align with the enterprise identity created in previous phases, the following parameters were set:
* **Hostname:** `Firewall`
* **Domain:** `SOC.local` (Matching the AD Forest)
* **Primary DNS:** `8.8.8.8` (Google DNS for initial external resolution)


## 1. Network Interface Topology
The firewall was provisioned with three distinct network adapters in VMware, mapped to logical LAN segments for hardware-level isolation:

| Interface | Alias | Assignment | IP Address | Role |
| :--- | :--- | :--- | :--- | :--- |
| **em0** | WAN | NAT | 192.168.149.129/24 | ISP Uplink / Internet Access |
| **em1** | LAN | Corporate_Network | 10.0.0.1/8 | Gateway for DC-022 |
| **em2** | OPT1 | Attacker_Zone | 172.16.0.1/24 | Isolated Malware/Attacker Lab |


## 2. WAN Interface Hardening & Configuration
During the initial Setup Wizard (Step 4 of 9), I applied specific configurations to ensure the firewall functions correctly within a virtualized environment.

### Bypassing Private Network Blocking
Since the WAN interface receives a private IP (`192.168.x.x`) from the host's VMware NAT service, I adjusted the security policy:
* **RFC1918 Networks:** Disabled "Block RFC1918 Private Networks" to allow traffic from the virtual host.
* **Bogon Networks:** Disabled "Block bogon networks" for internal virtualization routing.

> **Evidence of Configuration:**
> ![pfSense WAN Security Setup](./Screenshots/pps2.png)


## 3. Post-Deployment Dashboard & System Health
After completing the wizard, the pfSense WebGUI confirmed a stable and healthy system state.

### System Verification:
* **Version:** 2.8.1-RELEASE (amd64).
* **Uptime:** System stabilized after the initial pager read error resolution.
* **DNS Resolution:** Configured with `127.0.0.1` and `192.168.149.2`.

> **Status Dashboard:**
> ![pfSense Final Dashboard](./Screenshots/pps3.png)


## 4. Final Validation: Internet Connectivity Test
To verify **NAT** and **Routing**, I performed a live connectivity test from **DC-022**.

* **Result:** **Success**. The Domain Controller can now reach the internet securely through the pfSense gateway.

> **Connectivity Proof:**
> ![Internet Connectivity Check](./Screenshots/pps4.png)


##  Next Milestone: SOC Management Layer (Ubuntu Server)
With the network and identity layers operational, we move to deploying **Ubuntu Server** for **Wazuh** and **Zabbix** monitoring.

