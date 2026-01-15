# Wazuh Implementation & Debugging Log: Windows Agent & Dashboard Configuration

**Date:** January 14-15, 2026  
**Author:** Akula Ashrith (hallowedcave25)  
**Status:** Ongoing  

## Environment Setup
* **Host OS:** EndeavourOS (Arch Linux)
* **Hypervisor:** KVM/QEMU (Windows VM)
* **Wazuh Deployment:** Docker (Manager, Indexer, Dashboard)
* **Agent Node:** Windows 10 VM (IP: `192.168.100.175`)

---

## 1. Objective
To establish a functional Wazuh SIEM environment where the Dockerized Wazuh Manager (hosted on EndeavourOS) successfully receives telemetry from a Windows VM Agent, and to visualize this data via the Wazuh Dashboard.

---

## 2. Phase I: Establishing Windows Agent Connectivity
**Status:** ✅ **Success**

### Initial Issue
The Windows Agent was installed on the VM but failed to register with the Manager. Logs indicated:
* `Unable to connect to enrollment service`
* `Invalid Key`

### Debugging Steps & Resolution

#### A. Network Interface Verification
We identified that the bridge interface handling VM traffic was down on the host.
* **Discovery:** `virbr1` interface was `DOWN`.
* **Action:** Brought the interface up to allow traffic flow between Host and VM.

#### B. Firewall Configuration (Host-side)
The EndeavourOS host firewall was blocking ingestion ports.
* **Action:** Updated firewall rules to accept incoming traffic from the VM network.
* **Ports Opened:** * `1514` (Agent communication)
    * `1515` (Enrollment)

#### C. Docker Network Mode
* **Attempt:** Initially tried `network_mode: "host"` to bypass NAT, but this caused port conflicts.
* **Resolution:** Reverted to standard bridge networking and explicitly mapped ports in `docker-compose.yml`.

**Result:** The Windows Agent successfully connected and registered with the Manager.

---

## 3. Phase II: Dashboard & API Configuration
**Status:** ⚠️ **Unresolved / In Progress**

### Context
After establishing Agent connectivity, we attempted to load the Wazuh Dashboard. The interface failed to load correctly, citing API connection errors.

### Debugging Steps

#### A. API Connection Error Analysis
The Dashboard could not communicate with the Manager API. The error suggested it was attempting to reach `localhost` from within the Docker container, which does not resolve to the Manager container.

#### B. Configuration Modification (`wazuh.yml`)
We accessed the `wazuh-dashboard` container to modify the configuration manually.
* **File Edited:** `wazuh.yml`
* **Change:** Updated the `url` parameter.
    * *From:* `https://localhost`
    * *To:* `https://wazuh.manager` (Internal Docker DNS name)

### Current State
Despite the URL update, the Dashboard is still failing to load the interface completely. The backend connection (Agent -> Manager) is stable, but the frontend (Dashboard -> Manager API) configuration requires further troubleshooting.

---

## 4. Next Steps
1.  **Log Analysis:** Inspect `wazuh-dashboard` container logs for HTTP 500 or timeout errors.
2.  **Indexer Health:** Verify `wazuh.indexer` status, as the Dashboard relies on it for authentication.
3.  **Browser Console:** Check client-side errors when attempting to load the Dashboard.
