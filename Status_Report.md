## Project Overview

The primary objective of the Enterprise Security Operations Lab is to simulate a realistic corporate network environment for hands-on cybersecurity research and practice. The lab supports core Blue Team defense strategies, including centralized log ingestion, continuous intrusion detection, threat monitoring, and operational incident response workflows.

## Current Status

**Status:** On Hold

Initial core infrastructure setup, persistent storage routing, and basic SIEM deployment are complete. The baseline foundation is ready for full activation and endpoint onboarding when operations resume.

## Updated Architecture Topology

The lab operates on a hybrid physical-virtual infrastructure distributed across two physical nodes, linked over an encrypted overlay network.

| Component / Node | OS / Platform | Function & Details |
| --- | --- | --- |
| **Server Node** | ZorinOS (Old Laptop) | Hosts the primary Wazuh SIEM Manager and Indexer. Configured with permanent data routing to a dedicated 1TB HDD to accommodate long-term telemetry storage. |
| **Primary Host Node** | EndeavourOS (Main Laptop) | The primary workstation host, providing virtualization infrastructure for target and attack systems. |
| **Attacker Node** | Kali Linux (VM on Main Laptop) | Virtual machine operating on the primary host, isolated for offensive security testing and validation. |
| **Victim Node** | Windows 10 Pro (VM on Main Laptop) | Virtual machine operating on the primary host, configured as a standard enterprise endpoint for defensive monitoring. |
| **Networking** | Tailscale Mesh VPN | Connects all nodes and virtual environments across physical networks over the internet. |

## Work Completed So Far

The following technical milestones establish the foundation of the lab server:

* **Identity & Access Management:** Created an isolated `labadmin` system user on the server to separate administrative tasks from default system operations, and configured secure remote SSH access.
* **Storage Allocation:** Formatted and mounted a dedicated 1TB external HDD to `/mnt/labdata` via auto-mount configuration in `/etc/fstab` to ensure storage persistence across reboots.
* **SIEM Deployment:** Installed the Wazuh SIEM platform on ZorinOS. Custom symlinks redirect heavy data directories to the `/mnt/labdata` HDD mount, which prevents root drive exhaustion.
* **Remote Networking:** Installed and configured Tailscale on the server node to join the secure mesh network, enabling remote connectivity without opening public ports.
