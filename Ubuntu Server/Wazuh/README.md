##  SOC Manager & Wazuh Deployment

In this stage, I successfully deployed the central monitoring node (SOC Manager) using Docker containers.

###  System Configuration
- **Host:** Ubuntu Server (soc-manager)
- **IP Address:** 10.0.0.20 (Static)
- **Architecture:** Managed via pfSense firewall (10.0.0.1 gateway)

###  Implementation Steps
1. **Network Alignment:** Configured the static IP and DNS to ensure connectivity through the local gateway.
2. **Infrastructure Prep:** Adjusted `vm.max_map_count` and prepared the Docker environment.
3. **Security:** Generated self-signed SSL certificates for secure communication between nodes.
4. **Orchestration:** Deployed the Wazuh stack (Indexer, Server, Dashboard) using `docker-compose`.

###  Verification
All containers are running correctly as shown in the deployment logs:
![Wazuh Done Status](./Screenhots/wz0.png)


### Case Study: Troubleshooting Service Availability in Dockerized Wazuh Stack


##  Incident Investigation
While the infrastructure was deployed successfully, a critical gap was identified between the **Container Status** and **Service Availability**. 

### 1. Persistence of Zombie Containers
Despite the backend services being unreachable, Docker reported that the containers had been running for over **14 hours**. This indicates that the processes were alive at the OS level, but the application services (Wazuh Dashboard & Indexer) were stuck in an initialization loop or failing internal health checks.

**Evidence:**
- **Container Uptime:** `Up 14 hours` (Confirmed via `docker ps`).
- **Service Name:** `single-node_wazuh.dashboard_1`.
![Service Status](./Screenhots/wz5.png)

### 2. Browser Connection Failure
Attempting to access the SOC Web Interface resulted in a persistent loading state followed by a connection timeout.

**Evidence:**
- **URL:** `https://10.0.0.20`
- **Error:** `Hmmm... can't reach this page`.
- **Symptoms:** The browser fails to establish a TCP handshake with Port 443, suggesting the Dashboard service is not binding correctly to the host network.
![The browser failure](./Screenhots/wzpr.png)

###  3. Root Cause (from Logs)

### 1. Backend Connectivity Failure (Indexer Unreachable)
* **Error:** `[ConnectionError]: connect ECONNREFUSED 172.18.0.2:9200`.
* **Impact:** The Dashboard cannot establish a socket connection with the Indexer API. This is the primary reason the web interface fails to load.
![single-node_wazuh.dashboard_1 logs](./Screenhots/wzz.png)

### 2. Critical Resource Constraint (Low RAM)

![logs](./Screenhots/ww.png)

* **Detection:** System logs identify total available memory as `3869MB` (~4GB).
* **Impact:** The Wazuh Indexer and Manager require significant heap memory to start. Operating the full stack on 4GB causes services to stall or refuse connections due to resource exhaustion.

### 3. Service State Deadlock (Index Conflict)
* **Error:** `[resource_already_exists_exception]: index [wazuh-statistics-...] already exists`.
* **Impact:** Because containers remained in a failed state for 14 hours, the Indexer is stuck attempting to recreate existing indices, preventing the security handshake from completing.

### 4. Administrative Command Failure (Naming Mismatch)
* **Detection:** Attempts to run `docker exec` or `docker logs` failed with `No such container`.
* **Impact:** Commands were issued using shorthand names (e.g., `wazuh.dashboard`) instead of the actual container names defined by the compose project (`single-node_wazuh.dashboard_1`).
  
##  Evidence Log
| Component | Status | Log Evidence |
| :--- | :--- | :--- |
| **Network** | Timeout | `10.0.0.20 took too long to respond` |
| **Memory** | Insufficient | `Total RAM: 3869MB` |
| **API** | Refused | `connect ECONNREFUSED 172.18.0.2:9200` |
| **Storage** | Conflict | `resource_already_exists_exception` |


### Recommended Resolution Steps
