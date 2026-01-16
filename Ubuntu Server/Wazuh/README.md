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
![Wazuh Done Status](./Screenhots/waz2.png)
