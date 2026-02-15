# Application Layer Constraints

High-latency proxies drop connections if SYN queues or MTU buffers overflow. The BitTorrent protocol must be strictly throttled to slide under anomaly-detection thresholds.

## 1. General Network Settings (Tools > Options > Connection)
* **Proxy Server Type:** `(None)`. (Do not double-encapsulate traffic within the TUN adapter).
* **UPnP / NAT-PMP:** Unchecked.

## 2. Protocol Enforcement (Tools > Options > BitTorrent)
* **Enable DHT:** Unchecked.
* **Enable PeX:** Unchecked.
* **Enable Local Peer Discovery:** Unchecked.
* **Encryption mode:** `Require encryption`.

## 3. TCP State Constraints (Tools > Options > Connection)
* **Global maximum number of connections:** `50`
* **Maximum number of connections per torrent:** `20`

## 4. High-Latency BDP Tuning (Tools > Options > Advanced)
* **Network interface:** `Any interface`
* **Outgoing connections per second:** `10`
* **Maximum outstanding requests to a single peer:** `50`
* **Asynchronous I/O threads:** `4` (Optimized for standard NVMe/SSD drive architectures).
* **$\mu$TP-TCP mixed mode algorithm:** `Prefer TCP`

## 5. Bandwidth Throttling (Tools > Options > Speed)
* **Apply rate limit to transport overhead:** Checked.
* **Apply rate limit to peers on LAN:** Checked.
* **Download limit:** `3000` KiB/s (Adjust based on local proxy burst-disconnect threshold).
* **Upload limit:** `500` KiB/s (Strict constraint required to prevent TCP ACK delays on the download stream).
