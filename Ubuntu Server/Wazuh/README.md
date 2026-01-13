## Wazuh SIEM Deployment

Successfully deployed the Wazuh stack using Docker containers on the SOC Manager.

### Deployment Steps:
1. Adjusted virtual memory: `vm.max_map_count=262144`.
2. Cloned official Wazuh Docker repository (v4.9.0).
3. Generated SSL/TLS certificates for secure internal communication.
4. Orchestrated the stack using `docker-compose`.

### Access Details:
* **URL:** `https://10.0.0.20`
* **User:** `admin`
* **Password:** `SecretPassword`

### Wazuh Security Configuration
* **Certificate Generation:** Successfully generated SSL/TLS certificates using the `generator` container.
* **Configuration Volume:** Verified the creation of the `/config` directory containing all necessary security keys.
* **Deployment:** Initiated the stack deployment using `docker-compose up -d`.
