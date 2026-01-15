# Enterprise Security Operations Lab

**Status:** 🚧 Configuration Phase

### Objective
To simulate a corporate network environment and practice Blue Team defense strategies, including log ingestion, intrusion detection, and incident response.

### Infrastructure Topology
* **Host Node:** EndavourOS
* **Attacker Node:** Kali Linux (running Nmap, Metasploit)
* **Victim Node:** Windows 10 Pro VM
* **Defense Node:** Wazuh SIEM (Manager & Indexer)

### Implementation Roadmap
- [x] Virtual Environment Setup (Virtual Machine Manager)
- [x] Deploy Wazuh Agents on victim machines
- [ ] Configure `iptables` and Firewall rules
- [ ] Simulate Brute-Force SSH attack and verify SIEM alerts
