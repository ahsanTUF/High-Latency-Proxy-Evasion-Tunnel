# 5. Application Tuning: qBittorrent & Steam Optimization

BitTorrent and Steam are "aggressive" protocols. They attempt to open hundreds of simultaneous connections and flood the network with UDP packets. In a tunneled environment, this behavior will immediately saturate the SSH buffer or trigger the university firewall's flood protection, dropping your speed to 0 B/s. 

You must strictly govern these applications to maintain a stable, high-speed connection.

---

## I. qBittorrent: The "No-Crash" Configuration
These settings prevent the application from overwhelming the `xtls-rprx-vision` flow and ensure the payload is obfuscated from Deep Packet Inspection (DPI).

### 1. Connection Limits (Tools > Options > Connection)
| Parameter | Value | Critical Logic |
| :--- | :--- | :--- |
| **Proxy Server Type** | `(None)` | **Mandatory:** Do not use internal proxies. Nekoray TUN handles routing natively. Double-proxying causes 0 B/s stalls. |
| **Global Max Connections** | `50` | Prevents the proxy state table from overflowing. |
| **Max Connections per Torrent** | `20` | Focuses bandwidth on a few high-quality peers. |
| **UPnP / NAT-PMP** | `Unchecked` | Prevents useless background noise as the tunnel cannot map ports. |

### 2. Protocol & Encryption (Tools > Options > BitTorrent)
* **Encryption mode:** `Require encryption`. 
    * *Logic:* Forces peers to encrypt data, masking BitTorrent signatures from DPI filters.
* **Peer Discovery (DHT/PeX/LPD):** `All Unchecked`.
    * *Logic:* These use raw UDP. In an SSH tunnel, they fail and generate "dead" traffic that stalls the pipeline.

### 3. Bandwidth Management (Tools > Options > Speed)
* **Apply rate limit to transport overhead:** `Checked`.
* **Apply rate limit to peers on LAN:** `Checked`.
* **Download Limit:** `3000` KiB/s. 
    * *Scaling:* Start at 1500 and increment by 500 every 3 minutes until you find your university's "burst" ceiling.
* **Upload Limit:** `500` KiB/s.
    * *Logic:* Uploading too fast delays the TCP "ACK" packets for your download, causing the speed to collapse.

### 4. High-Latency Tuning (Tools > Options > Advanced)
* **Outgoing connections per second:** `10`. (Reduces "SYN" spam that triggers firewalls).
* **Max outstanding requests to a single peer:** `50`. 
    * *Logic:* Balances the 200ms delay by keeping enough data "in flight" without overflowing the buffer.
* **Asynchronous I/O threads:** `4`. (Optimized for standard laptop SSD/NVMe performance).
* **µTP-TCP mixed mode algorithm:** `Prefer TCP`.

---

## II. Steam: Stable Downloading
Steam handles tunneling better than BitTorrent but requires a hard bandwidth cap to prevent TCP window collapse.

1.  **Open Steam > Settings > Downloads.**
2.  **Limit download speed:** Enable and set to `3000` KB/s (or 3 MB/s).
3.  **Throttle downloads while streaming:** Unchecked.
4.  **Clear Download Cache:** Perform this if you see the "0 B/s" flatline even when Nekoray is active.

---

## III. The "Stall to Zero" Recovery Protocol
If you see speeds peak at 5 MiB/s and then gradually drop to 0 B/s:

1.  **The Cause:** You hit the university's burst limit, and your TCP window collapsed.
2.  **The Fix:**
    * Pause all downloads.
    * Go to Git Bash, press `Ctrl + C`, and re-run the SSH command.
    * Lower your qBittorrent/Steam download limit by 500 KiB/s.
    * Resume the download.

---
> **Final Module:** Proceed to **6_DAILY_MAINTENANCE.md** for the quick-start checklist and common troubleshooting.
