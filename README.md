# Enterprise Security Operations Lab

Status: ⏸️ On Hold

## Objective
To simulate a corporate network environment and practice Blue Team defense strategies. These strategies cover log ingestion, intrusion detection, network traffic monitoring, and incident response.

## Infrastructure Topology
* Server Node: ZorinOS (Old Laptop) running Wazuh SIEM Manager & Indexer with data mapped to a 1TB HDD.
* Primary Host Node: EndeavourOS (Main Laptop).
* Attacker Node: Kali Linux VM running Nmap and Metasploit.
* Victim Node: Windows 10 Pro VM.
* Networking: Tailscale routing that securely connects all nodes.

## Implementation Roadmap
* [x] Create an isolated labadmin account and enable SSH access on the Server Node.
* [x] Format the 1TB HDD as ext4 and permanently mount it to `/mnt/labdata` via `/etc/fstab`.
* [x] Install Wazuh SIEM directly on ZorinOS and create symlinks to route heavy log data to the HDD.
* [x] Install Tailscale on the Server Node to secure a static IP address.
* [ ] Deploy Windows 10 and Kali Linux Virtual Machines on the EndeavourOS host.
* [ ] Install Tailscale inside both Virtual Machines.
* [ ] Deploy the Wazuh Agent on the Windows 10 Victim Node and connect it to the server.
* [ ] Simulate Windows and SSH attacks to trigger SIEM alerts.

For Details visit: [Status Report](https://github.com/hallowedcave25/enterprise-security-homelab/blob/main/Status_Report.md).
