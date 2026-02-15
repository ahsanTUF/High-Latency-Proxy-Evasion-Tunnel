# 2. Server Configuration: BBR & Xray-Core

This module covers the critical internal optimizations required to handle high-latency packets and the installation of the stealth proxy engine.

## I. TCP BBR Congestion Control
Standard TCP congestion control (CUBIC) interprets packet loss as a signal to drastically reduce speed. In a high-latency (200ms+) proxy environment, this results in the connection "stalling" to 0 B/s. **BBR (Bottleneck Bandwidth and RTT)** uses a different algorithm that maintains high throughput even during minor packet drops.

### 1. Enable BBR
Execute these commands on your Ubuntu server:
```bash
# Set the default queuing discipline to Fair Queuing
echo "net.core.default_qdisc=fq" | sudo tee -a /etc/sysctl.conf

# Set the TCP congestion control to BBR
echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee -a /etc/sysctl.conf

# Apply the changes immediately
sudo sysctl -p

```

### 2. Verify Activation

Execute the following to confirm BBR is running:

```bash
sysctl net.ipv4.tcp_congestion_control
# Expected output: net.ipv4.tcp_congestion_control = bbr

```

---

## II. Xray-Core (VLESS + Vision) Installation

The server must run a proxy core that can handle "Vision" flow. This protocol adds randomized padding to TLS handshakes, making the traffic indistinguishable from a standard Google Chrome session.

### 1. Installation Script

The most efficient method is using a management script (like 3X-UI) or the official Xray-core install:

```bash
bash <(curl -Ls [https://raw.githubusercontent.com/mzz001/Xray-install/main/install.sh](https://raw.githubusercontent.com/mzz001/Xray-install/main/install.sh))

```

### 2. Mandatory Protocol Parameters

When configuring the inbound connection, the following settings are **mandatory** for this bypass method:

| Parameter | Value | Description |
| --- | --- | --- |
| **Protocol** | `VLESS` | Lightweight, no-encryption protocol (the SSH tunnel handles encryption). |
| **Port** | `443` | Standard HTTPS port to blend in with web traffic. |
| **Flow** | `xtls-rprx-vision` | **Critical:** Prevents DPI from detecting proxy signatures. |
| **Transmission** | `TCP` | Required for the SSH tunnel bridge. |
| **Security** | `REALITY` or `TLS` | Reality is recommended for superior stealth. |
| **Listen IP** | `127.0.0.1` | **Security:** The proxy should only accept connections from your local SSH tunnel. |

### 3. Client UUID Generation

Generate a unique ID for your connection:

```bash
cat /proc/sys/kernel/random/uuid

```

*Save this UUID. You will need it for the Nekoray configuration.*

---

## III. Performance Hardening

Increase the system's open file limit to prevent the server from dropping connections when under heavy load (e.g., hundreds of BitTorrent peers).

1. Edit the limits file:
```bash
sudo nano /etc/security/limits.conf

```


2. Add these lines to the bottom:
```text
* soft nofile 51200
* hard nofile 51200

```


3. Apply system-wide:
```bash
sudo sysctl -w fs.file-max=51200

```



> **CRITICAL:** If you fail to set the **Listen IP** to `127.0.0.1`, your proxy will be exposed to the public internet. By binding it to the local loopback, you ensure that only someone with your SSH `.key` file can access the proxy via the tunnel.
