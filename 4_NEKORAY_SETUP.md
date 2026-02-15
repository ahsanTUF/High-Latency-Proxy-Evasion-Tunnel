# 4. Nekoray Configuration: Encapsulation & TUN Routing

Nekoray acts as the "brain" of the operation. It takes all the raw internet traffic from your computer, packages it into the stealthy Xray protocol, and sends it into the local tunnel we created in Git Bash.

## I. Core Installation & Setup
1.  **Download:** [Nekoray GitHub Releases](https://github.com/MatsuriDayo/nekoray/releases). Download the `windows64` zip.
2.  **Initial Launch:** Extract the folder and run `nekoray.exe`.
3.  **Core Selection:** When prompted, select **Xray**. This is mandatory to use the `xtls-rprx-vision` protocol.

---

## II. Profile Configuration (The Master Tunnel)
You must manually create the profile to point at your local Git Bash bridge.

1.  Click **Program** > **Add Profile** > **Custom (Manual)**.
2.  Set the following parameters exactly:

| Field | Value | Critical Logic |
| :--- | :--- | :--- |
| **Name** | `Master-Tunnel` | Identification label. |
| **Protocol** | `VLESS` | Matches the server-side proxy engine. |
| **Address** | `127.0.0.1` | **Mandatory:** Points to the start of your SSH tunnel. |
| **Port** | `4444` | Matches the `-L` port forward in your Git Bash command. |
| **UUID** | `[Your-Server-UUID]` | The unique ID generated in Module 2. |
| **Flow** | `xtls-rprx-vision` | **Critical:** Matches the server-side stealth protocol. |
| **Network** | `tcp` | Required for stability over SSH. |
| **Security** | `tls` or `reality` | Ensure this matches your server-side security settings. |
| **Multiplex (Mux)** | `Keep Default` | **STRICT WARNING:** Do NOT enable. Mux breaks the Vision flow. |

---

## III. Routing Settings (Preventing the Infinite Loop)
Without these settings, Nekoray will try to route the university proxy through itself, creating an infinite loop that crashes your network card.

1.  Navigate to **Preferences** > **Routing Setting**.
2.  In the **Simple Route** tab, find the **Direct IP** box.
3.  **Input:** `172.30.10.11` (The university proxy IP).
4.  This tells Nekoray: "Do not touch the university proxy connection; let it bypass the tunnel."

---

## IV. TUN Settings (The DNS Fix)
Standard DNS queries use UDP, which the SSH tunnel often drops. We use **Fake DNS** to resolve hostnames locally and tunnel them via TCP.

1.  Navigate to **Preferences** > **TUN Setting**.
2.  Check the box for **Fake DNS**.
3.  Set **Stack** to `system` or `gvisor`.
4.  Click **OK**.

---

## V. Activation Sequence
To correctly initialize the system:
1.  Verify the Git Bash window from Module 3 is still open and connected.
2.  Right-click the `Master-Tunnel` profile and select **Start**.
3.  On the main screen, check the **TUN Mode** box. 
4.  **UAC Prompt:** Click **Yes** when Windows asks for administrator permission to create the virtual network adapter.

---

## VI. Critical Troubleshooting
* **"System Proxy" vs "TUN Mode":** Only use **TUN Mode**. Do not use "System Proxy" as it only captures web browser traffic and ignores games/Steam/Torrents.
* **Fake DNS Issues:** If websites don't load, open a Windows Command Prompt and type `ipconfig /flushdns`.
* **Loopback Error:** If the logs show "Connection loop detected," verify that the university proxy IP is correctly entered in the **Direct IP** routing field.

---
> **Next Step:** Once the Nekoray logs show "Started," proceed to **5_APPLICATION_TUNING.md** to configure qBittorrent and Steam.
