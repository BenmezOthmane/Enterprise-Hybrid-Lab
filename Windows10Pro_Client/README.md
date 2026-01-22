### Configuring the static network parameters on the Windows Client

Before that, you need to verifying that:
* **Network Binding**: Attached to **LAN Segment: Corporate**.
> ![VM Network settings](./Screenshots/cl0.png)

Then, you should follow these steps, to configuring the static network parameters on the Windows Client:

> ![VM Network settings](./Screenshots/cl1.png)
> ![VM Network settings](./Screenshots/cl2.png)
> ![VM Network settings](./Screenshots/cl3.png)
> ![VM Network settings](./Screenshots/cl4.png)
> ![VM Network settings](./Screenshots/cl5.png)

### Client-to-Server Connectivity Validation

After configuring the static network parameters on the Windows Client, a verification phase was initiated to ensure seamless communication within the **Corporate Network** LAN segment.
> ![VM Network settings](./Screenshots/cl6.png)

### 1. Network Configuration Audit
The client workstation was successfully provisioned with a dedicated static IP to ensure persistent identity:
- **Hostname**: `Client`
- **IPv4 Address**: `10.0.0.30`
- **Subnet Mask**: `255.0.0.0` (Synchronized with AD Machine)
- **Default Gateway**: `10.0.0.1`

### 2. Connectivity Benchmarking (Ping Test)
To verify the Layer 3 reachability between the Client and the Domain Controller (`10.0.0.10`), an ICMP echo request was performed:
- **Target**: `10.0.0.10` (DC-022)
- **Packets**: Sent = 4, Received = 4, Lost = 0 (**0% loss**).
- **Latency**: Average = **0ms** (Sub-millisecond response time).

### 3. Strategic Outcome
The zero-latency connection confirms that the **LAN Segment** is correctly isolated and operational. This establish the fundamental "pipe" required for:
1. Joining the `SOC.local` Active Directory Domain.
2. Routing security telemetry to the Wazuh Manager at `10.0.0.20`.
