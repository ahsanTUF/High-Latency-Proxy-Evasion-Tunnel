# 1. Server Infrastructure: Oracle Cloud & VCN Setup

To maintain a stable bypass, the remote environment must be configured to allow non-standard SSH tunneling and prevent the cloud firewall from dropping long-lived TCP connections.

## I. Instance Provisioning
Use the Oracle Cloud Console to create your remote endpoint.

| Parameter | Recommended Setting |
| :--- | :--- |
| **Operating System** | Ubuntu 24.04 LTS (Minimal is preferred) |
| **Shape** | VM.Standard.E4.Flex (or Always Free ARM Ampere) |
| **OCPUs** | 1 (Minimum) |
| **Memory** | 1 GB (Minimum) |
| **SSH Keys** | **Save both Private and Public keys.** You cannot recover them later. |

---

## II. Virtual Cloud Network (VCN) Configuration
By default, Oracle Cloud blocks all ports. You must manually open the ports required for the transport bridge.

### 1. Ingress Rules (Incoming Traffic)
Navigate to **Networking** > **Virtual Cloud Networks** > **[Your VCN]** > **Security Lists**. Add the following rules:

| Stateless | Source CIDR | IP Protocol | Source Port Range | Destination Port Range | Description |
| :--- | :--- | :--- | :--- | :--- | :--- |
| No | `0.0.0.0/0` | TCP | All | `22` | Standard SSH Access |
| No | `0.0.0.0/0` | TCP | All | `443` | Xray/VLESS Protocol (Vision) |

### 2. Egress Rules (Outgoing Traffic)
Ensure the default egress rule allows all traffic:
* **Destination CIDR:** `0.0.0.0/0`
* **IP Protocol:** All Protocols

---

## III. OS-Level Firewall Adjustment
Even after opening VCN ports, the internal Ubuntu `iptables` or `ufw` may block traffic.

1.  Connect to your server via standard SSH.
2.  Disable `ufw` (uncomplicated firewall) to ensure it does not interfere with Xray:
    ```bash
    sudo ufw disable
    ```
3.  Flush existing iptables rules to prevent conflicts:
    ```bash
    sudo iptables -F
    sudo iptables -X
    sudo iptables -t nat -F
    sudo iptables -t nat -X
    sudo iptables -t mangle -F
    sudo iptables -t mangle -X
    sudo iptables -P INPUT ACCEPT
    sudo iptables -P FORWARD ACCEPT
    sudo iptables -P OUTPUT ACCEPT
    ```
4.  Install `iptables-persistent` to ensure these rules survive a server reboot:
    ```bash
    sudo apt-get update
    sudo apt-get install iptables-persistent -y
    sudo netfilter-persistent save
    ```

---

## IV. Critical Instance Verification
Before proceeding to the next module, verify the instance accessibility:

* **Public IP:** Ensure you have the reserved Static IPv4 address.
* **Ping Test:** Ping the server from a network **without** restrictions.
* **SSH Check:** Ensure you can log in using the `.key` file generated during provisioning.

> **CRITICAL:** Do not use ports other than `443` for the proxy core. Many university firewalls use Deep Packet Inspection (DPI) to drop traffic on non-standard ports, but will allow `443` (HTTPS) to pass through.
