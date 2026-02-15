# High-Latency Proxy Evasion Tunnel

## Architecture
A hardware-accelerated tunneling protocol designed to bypass Deep Packet Inspection (DPI), restrictive proxy firewalls, and aggressive TCP connection state limitations on enterprise/educational networks. 

This system utilizes an OpenSSH transport layer to bypass local HTTP proxies, encapsulates traffic via an Xray/VLESS core (xtls-rprx-vision), and strictly governs application-layer payloads to prevent firewall burst-disconnects.

## Prerequisites
* Remote Virtual Machine (Ubuntu 24.04 LTS).
* Windows Client OS.
* Git for Windows (provides `connect.exe` for HTTP proxy traversal).
* Nekoray (Windows proxy client).
* SSH Private Key (`.key` or `.pem`).

## Documentation Index
1. [Server Setup](1_SERVER_SETUP.md)
2. [Client Transport](2_CLIENT_TRANSPORT.md)
3. [Nekoray Encapsulation](3_NEKORAY_CONFIG.md)
4. [qBittorrent Optimization](4_QBITTORRENT_OPTIMIZATION.md)
5. [Daily Execution](5_DAILY_EXECUTION.md)
