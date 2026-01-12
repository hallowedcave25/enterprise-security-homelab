# 🚩 Incident Report: Home Lab Connectivity Failure

**Date:** January 11, 2026
**Status:** 🔴 **Unresolved / Critical Blocking**
**Environment:** VirtualBox, Kali Linux (Attacker), Windows 10 (Victim), EndeavourOS (Host)

## 1. Executive Summary
The initial deployment of the cybersecurity home lab has stalled due to a persistent network isolation issue. Despite configuring virtual network adapters and disabling host-based firewalls, the Attacker VM (Kali) and Victim VM (Windows) are unable to communicate. **ICMP (Ping) and TCP/HTTP requests fail with 100% packet loss.** All standard troubleshooting methods have been exhausted without success.

## 2. Detailed Failure Log & Methods Tried

### A. Virtual Network Configuration Attempts
* **Method:** Configured both VMs to use "NAT Network" (named `NatNetwork`) to allow internal communication while maintaining internet access.
* **Check:** Verified both VMs were assigned to the same named network in VirtualBox settings.
* **Result:** **Failure.** Machines remained isolated.
* **Method:** Switched "Promiscuous Mode" to "Allow VMs" and "Allow All" on both adapters.
* **Result:** **Failure.** No change in visibility.
* **Method:** Toggled "Cable Connected" option in VirtualBox to force a NIC reset.
* **Result:** **Failure.** Interfaces reset but connectivity was not established.

### B. IP Addressing & Subnet Verification
* **Method:** Verified IP allocation via `ip a` (Kali) and `ipconfig` (Windows).
* **Observation:** Both machines appeared to be on the same subnet (e.g., `10.0.2.x`).
* **Method:** Attempted manual Static IP assignment on Windows to rule out DHCP failure.
* **Result:** **Failure.** Even with static IPs, `ping` requests returned "Destination Host Unreachable."

### C. OS-Level Firewall & Security Attempts
* **Method:** Completely disabled **Windows Defender Firewall** (Domain, Private, and Public profiles).
* **Result:** **Failure.** Windows machine remained invisible to Kali.
* **Method:** Cleared ARP tables and flushed DNS cache on both machines.
* **Result:** **Failure.**

### D. Service Connectivity Tests
* **Test:** ICMP Echo Request (`ping`) from Kali to Windows.
    * **Output:** `Destination Host Unreachable` / 100% Packet Loss.
* **Test:** Python HTTP Server (`python3 -m http.server 80`) on Kali.
    * **Action:** Attempted to access via Windows Edge Browser.
    * **Output:** `ERR_CONNECTION_TIMED_OUT` / "Hmm... can't reach this page."

## 3. Current System State
* **Kali Linux:** Interface is up, has IP, but cannot reach neighbors.
* **Windows 10:** Interface is up, has IP, but acts as if physically disconnected from the Kali network.
* **VirtualBox:** Network indicates "Active," but traffic is not routing between guests.

## 4. Relevant Documentation (Screenshots)

*Reference the following screenshots in the `assets/` folder:*

1.  **`01_vbox_network_settings.png`**: Showing both VMs attached to "NAT Network".
2.  **`02_ip_config_mismatch.png`**: Side-by-side terminal view of `ip a` (Kali) and `ipconfig` (Windows).
3.  **`03_ping_timeout.png`**: The terminal output on Kali showing the "Destination Host Unreachable" error.
4.  **`04_windows_firewall_off.png`**: Proof that Windows Defender is fully disabled.
5.  **`05_browser_failure.png`**: The Edge browser error screen failing to load the Python server.

---
*Logged by: hallowedcave25*
