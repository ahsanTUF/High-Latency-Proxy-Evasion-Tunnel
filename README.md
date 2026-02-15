# High-Latency Proxy Evasion Tunnel (HEPET)

## 📌 Overview
This repository provides a comprehensive, step-by-step guide to bypassing aggressive network restrictions, Deep Packet Inspection (DPI), and protocol-based throttling (e.g., university or corporate firewalls). 

The architecture utilizes a **three-tier encapsulation model**:
1.  **Transport Layer:** OpenSSH over HTTP Proxy (via `connect.exe`) to penetrate the local gateway.
2.  **Encryption Layer:** Xray-core with `xtls-rprx-vision` to simulate legitimate TLS traffic and evade DPI.
3.  **Routing Layer:** TUN-based interception with Fake DNS to force all system traffic (TCP/UDP) through the established bridge.

## 🚀 Key Features
* **Bypasses Protocol Bans:** Successfully tunnels BitTorrent, Steam, and Gaming traffic where standard VPNs fail.
* **High-Latency Optimization:** Uses TCP BBR on the remote server to maintain 3-10 MiB/s throughput even with 200ms+ pings.
* **Hardware Acceleration:** Utilizes `aes128-gcm` ciphers to minimize CPU overhead on the host machine.
* **DNS Leak Protection:** Implements Fake DNS to prevent local gateway DNS hijacking.

## 📁 Documentation Structure
To ensure a successful deployment, follow the guides in this exact order:

1.  [**Server-Side: Instance & VCN Setup**](1_SERVER_INFRASTRUCTURE.md) — Provisioning the cloud environment.
2.  [**Server-Side: Xray-Core & BBR**](2_SERVER_CONFIGURATION.md) — Optimizing the Linux kernel and proxy engine.
3.  [**Client-Side: Transport Bridge**](3_CLIENT_TRANSPORT.md) — Setting up Git Bash and the SSH tunnel.
4.  [**Client-Side: Nekoray Encapsulation**](4_NEKORAY_SETUP.md) — Routing and TUN configuration.
5.  [**Application-Specific: qBittorrent & Steam**](5_APPLICATION_TUNING.md) — Critical settings to prevent firewall bans.
6.  [**Operational Protocol**](6_DAILY_MAINTENANCE.md) — Daily start-up and troubleshooting.

## ⚠️ Critical Warnings
* **Traffic Isolation:** Do not run multiple high-bandwidth applications (Steam + Torrents) simultaneously. It will collapse the SSH socket buffer.
* **Mux Warning:** Do not enable Multiplexing (Mux) in Nekoray while using `xtls-rprx-vision`. This results in immediate connection termination.
* **Hardware Limits:** The 94 Mbps ethernet cap is a theoretical maximum; practical stability is found between **3000-5000 KiB/s**.

---
**Disclaimer:** This guide is for educational purposes. Ensure you comply with your local network's Terms of Service.
