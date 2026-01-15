# My Home Lab: Wazuh SIEM Setup & Migration Log

## 1. Overview
This document logs the architecture changes and configuration steps I took to set up a malware analysis lab. Initially, I attempted to host the Wazuh Manager on my host OS (Arch Linux/Omen16) using Docker. However, due to network isolation complexities between the Docker containers and my KVM-isolated Windows VM, I migrated the Wazuh Manager directly onto my Kali Linux attacker machine.

### Final Architecture
* **Host:** Omen16 (Arch Linux) running KVM/QEMU.
* **Wazuh Server:** Kali Linux VM (Acting as both Attacker & SIEM).
* **Victim Agent:** Windows 11 VM.
* **Network:** Isolated Lab Network (`192.168.100.x`).

---

## 2. VM Resource Configuration
To support the heavy Java-based Wazuh Indexer (OpenSearch), I had to modify the Kali VM hardware settings to prevent the OOM (Out of Memory) Killer from terminating the database.

* **RAM:** Increased to **6144 MB (6GB)**.
* **Allocation Type:** **Fixed** (Static) Memory.
    * *Note: I unchecked "Enable Shared Memory" to guarantee resources were locked for the database.*
* **Clipboard Support:** I installed `spice-vdagent` on Kali to enable copy/paste between my host and the VM.
    ```bash
    sudo apt update && sudo apt install spice-vdagent -y
    sudo reboot
    ```

---

## 3. Network Configuration (The "Dual Interface" Fix)
My Kali VM required two network interfaces active simultaneously:
1.  **NAT (eth0):** For Internet access (downloading packages/updates).
2.  **Isolated (eth1):** For communicating with the Windows Victim (`192.168.100.x`).

**The Problem:**
Enabling the Isolated network interface caused a default gateway conflict, killing my Internet access because the OS tried to route internet traffic through the isolated lab network.

**The Fix:**
I manually configured the routing behavior in `nm-connection-editor`:
1.  Opened the Network Connections editor.
2.  Selected the **Isolated Lab** connection (linked to `eth1` with IP `192.168.100.170`).
3.  Navigated to **IPv4 Settings** -> **Routes...** (bottom right).
4.  Checked the box: **[x] Use this connection only for resources on its network**.
5.  *Result: Traffic to `192.168.100.*` stays local; everything else goes to the Internet via NAT.*

---

## 4. Wazuh Manager Installation (On Kali)
Since Kali Linux is a rolling release, the standard Wazuh installation script flagged an OS incompatibility. I forced the installation using the ignore flag.

**Command Used:**
```bash
curl -sO [https://packages.wazuh.com/4.14/wazuh-install.sh](https://packages.wazuh.com/4.14/wazuh-install.sh)
sudo bash ./wazuh-install.sh -a -i

-a: Automated installation (Manager + Indexer + Dashboard).

-i: Ignore system compatibility checks (Required for Kali).

Verification: I confirmed the server processes were active by checking the local agent status:

Bash

sudo /var/ossec/bin/agent_control -l
# Output: ID: 000, Name: attacker (server), IP: 127.0.0.1, Active/Local
5. Windows Agent Setup (The Victim)
I reconfigured the Windows Agent to point to the Kali machine's Isolated IP (192.168.100.170) to ensure traffic stayed within the virtual lab cables.

PowerShell Commands (Admin):

PowerShell

# 1. Clean up old configurations
Get-Package -Name "Wazuh Agent" | Uninstall-Package -Force

# 2. Install & Link to Kali Isolated IP
Invoke-WebRequest -Uri [https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.2-1.msi](https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.2-1.msi) -OutFile wazuh-agent.msi
.\wazuh-agent.msi /q WAZUH_MANAGER="192.168.100.170"

# 3. Start Service
Start-Service -Name WazuhSvc
6. Access & Monitoring
Dashboard URL: https://127.0.0.1 (Accessed via Firefox inside Kali).

Server Health: Checked via Wazuh Menu -> Server management -> Logs.

Agent Status: Confirmed Windows agent appeared as Active (ID: 001).

7. Testing Detection (EICAR)
To verify the detection pipeline, I used the EICAR test string to simulate a malware infection.

I created a file named virus.txt on the Windows Desktop containing the standard test signature:

Plaintext

X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*
Windows Defender immediately detected and deleted the file.

Wazuh Alert: I verified the detection by checking Endpoint Security -> Malware Detection on the Kali Dashboard, looking for Rule ID 60122 (or similar AV alerts).

Troubleshooting: If the alert doesn't appear, check archives.log or ensure the dashboard time filter is set to "Today".
