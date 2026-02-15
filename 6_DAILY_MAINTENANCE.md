# 6. Daily Operational Protocol & Troubleshooting

This final module ensures the long-term stability of your bypass. Following the exact sequence of activation is mandatory to prevent routing errors or "ghost" connections that block the tunnel.

## I. The Daily Start-Up Checklist
Execute these steps in order every time you boot your Windows machine.

1.  **Transport Activation:**
    * Open **Git Bash**.
    * Press the **Up Arrow** key to recall your master SSH command.
    * Press **Enter** and minimize the window once the server prompt appears.
2.  **Encapsulation Activation:**
    * Launch **Nekoray**.
    * Ensure **TUN Mode** is checked.
    * Right-click the `Master-Tunnel` profile and select **Start**.
3.  **Application Launch:**
    * Launch your download client (Steam or qBittorrent).
    * Verify the global download limit is active (e.g., `3000` KiB/s).

---

## II. Critical Operational Rules
To avoid getting your server IP or local port flagged, adhere to these constraints:

* **Traffic Isolation:** Never run qBittorrent and Steam simultaneously. The aggregate connection count will collapse the SSH socket.
* **Protocol Enforcement:** If you are downloading a torrent, ensure the trackers are showing as "Working" in the **Trackers** tab. If they are all "Not working," the tunnel is disconnected or your Fake DNS has failed.
* **The "3-Minute Rule":** After increasing any speed limits, wait exactly 3 minutes. The server-side **BBR algorithm** needs this time to calibrate packet pacing to your university's network.

---

## III. Advanced Troubleshooting (The "Zero-Speed" Matrix)
If your internet connection "magically" stops working, use this table to diagnose the failure point.

| Symptom | Probable Cause | Corrective Action |
| :--- | :--- | :--- |
| **"Connection Refused" in Git Bash** | Server port 443 is blocked or Xray is down. | Log into the Oracle dashboard and verify the instance status. |
| **Speeds peak then hit 0 B/s** | TCP Window Collapse / Burst Limit triggered. | Close the download, restart Git Bash SSH, and lower the speed limit by 500. |
| **0 Peers in qBittorrent** | UDP Leak / DHT is active. | Ensure DHT, PeX, and LPD are disabled in BitTorrent settings. |
| **Websites load, but Games don't** | TUN Mode is disabled. | Enable the **TUN Mode** checkbox in Nekoray and restart the profile. |
| **"Fake DNS" errors in logs** | Local DNS cache corruption. | Run `ipconfig /flushdns` in a Windows Command Prompt. |

---

## IV. Weekly Server Maintenance (Administrator)
Once a week, perform these steps on the Ubuntu server to ensure the kernel remains optimized:

1.  **Update Packages:**
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
2.  **Clear Logs:** Xray logs can grow large and consume disk space.
    ```bash
    sudo truncate -s 0 /var/log/xray/access.log
    sudo truncate -s 0 /var/log/xray/error.log
    ```
3.  **Reboot:**
    ```bash
    sudo reboot
    ```

---
> **Final Note:** This bypass is a delicate balance of protocol obfuscation and bandwidth management. If you are greedy with speed, the university firewall will win. Stay within the **1.5 MiB/s to 3.5 MiB/s** range for 24/7 stability.
