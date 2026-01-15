# ✅ Resolution Report: Migration to KVM & Lab Success

**Date:** January 13, 2026
**Reference:** [Incident Report: Home Lab Connectivity Failure](https://github.com/hallowedcave25/enterprise-security-homelab/blob/main/INCIDENT_REPORT_INITIAL_SETUP_FAILURES.md)
**Status:** 🟢 **Resolved / Operational**
**Environment:** KVM/QEMU (via virt-manager), Kali Linux (Attacker), Windows 10 (Victim), EndeavourOS (Host)

## 1. Executive Summary
Following the persistent network isolation failures with VirtualBox on an Arch-based host (EndeavourOS), the infrastructure was migrated to a native Linux KVM/QEMU hypervisor. The "VirtualBox NAT Network" approach was abandoned in favor of a "Split-Network" topology (NAT + Isolated).

**Result:** Full bidirectional connectivity has been established between the Attacker and Victim. The lab is now operational for the Red/Blue team project.

## 2. Root Cause Analysis
The previous connectivity failure was attributed to:
1.  **Kernel Incompatibility:** Rolling-release kernel updates (Arch Linux) breaking VirtualBox kernel modules (`vboxnetflt`/`vboxnetadp`).
2.  **Hypervisor Limitations:** VirtualBox's userspace networking stack struggled to maintain stable routing for the isolated subnet on the updated host system.

## 3. Resolution Steps: Infrastructure Migration

### A. Pivot to KVM/QEMU
* **Action:** Replaced VirtualBox with **KVM (Kernel-based Virtual Machine)** and `virt-manager`.
* **Rationale:** KVM is Type-1, running directly in the Linux kernel, offering superior stability and performance via `virtio` drivers.

### B. New Network Topology ("Split-Brain" Design)
To solve the isolation issue while retaining internet for tools, a dual-NIC strategy was implemented:

1.  **Network 1 (WAN / Internet):**
    * **Type:** NAT (Default KVM Network)
    * **Host:** Kali Linux (`eth0`)
    * **Purpose:** Repository updates and tool downloads.
2.  **Network 2 (LAN / Kill Zone):**
    * **Type:** Fully Isolated (No Host/Internet Routing)
    * **Subnet:** `192.168.100.0/24`
    * **Host:** Kali Linux (`eth1`) & Windows 10 (`Ethernet 1`)
    * **Purpose:** Attack traffic and logging.

### C. The Windows "Driver Trap" Fix
* **Issue:** Windows 10 installer failed to detect KVM virtual disks ("No drives found").
* **Fix:** Mounted `virtio-win.iso` during installation to load Red Hat VirtIO SCSI drivers.
* **Optimization:** Installed **QEMU Guest Agent** post-boot for improved mouse integration and shutdown handling.

## 4. Verification & Current System State

### Connectivity Test (Ping)
**Source:** Kali Linux (Attacker)
**Destination:** Windows 10 (Victim - `192.168.100.175`)

```bash
$ ping 192.168.100.175
PING 192.168.100.175 (192.168.100.175) 56(84) bytes of data.
64 bytes from 192.168.100.175: icmp_seq=1 ttl=128 time=0.632 ms
64 bytes from 192.168.100.175: icmp_seq=2 ttl=128 time=0.646 ms
```
Final Configuration Kali Linux:
--------------------------------
eth0: Up (Internet Access)
eth1: Up (192.168.100.170) - Waked up via nmcli

Windows 10 Victim:
--------------------------------
Ethernet: Up (192.168.100.175)
Firewall: DISABLED (Public/Private) to allow ICMP/Reverse Shells.
