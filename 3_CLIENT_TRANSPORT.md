# 3. Client-Side: The Transport Bridge (Git Bash & SSH)

The Transport Bridge is the core of the bypass. It creates a "wormhole" between your computer and the Oracle server by wrapping all data inside an encrypted SSH tunnel. This makes your traffic appear to the university proxy as a simple, secure management session.

## I. Prerequisites & Installation
You must install **Git for Windows** to obtain the `connect.exe` utility. Standard Windows Command Prompt or PowerShell cannot natively traverse HTTP proxies with SSH.

1.  **Download:** [Git for Windows Official Site](https://gitforwindows.org/).
2.  **Installation:** Use the default settings. Ensure **"Git Bash"** is selected during installation.
3.  **The Key File:** Ensure your private key file (e.g., `ssh-key.key`) is in a known directory. 
    * **Crucial:** Windows paths in Git Bash use forward slashes and a specific drive notation (e.g., `C:\Users` becomes `/c/Users`).

---

## II. The Master Execution Command
Open **Git Bash** and execute the following command. This command contains five critical parameters that must be correct for the tunnel to initialize.

### The Command Template
```bash
ssh -c aes128-gcm@openssh.com -o Compression=no -o TCPKeepAlive=yes -i "[LOCAL_PATH_TO_KEY]" -L 4444:127.0.0.1:443 -o "ProxyCommand=$(which connect) -H [UNI_PROXY_IP]:[PORT] %h %p" ubuntu@[SERVER_IP]

```

### Parameter Breakdown & Logic

| Flag | Value / Example | Critical Logic |
| --- | --- | --- |
| `-c` | `aes128-gcm@openssh.com` | **Hardware Acceleration:** Uses the CPU's AES-NI instructions. Prevents the 94 Mbps stream from overloading your CPU. |
| `-o` | `Compression=no` | **Throughput Optimization:** Compression adds latency and CPU overhead. Disabling it is mandatory for high-speed downloads. |
| `-i` | `"/c/Users/user/Documents/ssh-key.key"` | **Identity:** The absolute path to your `.key` file using Git Bash syntax. |
| `-L` | `4444:127.0.0.1:443` | **Port Forwarding:** Connects your local port `4444` to the server's internal port `443`. |
| `-o` | `ProxyCommand=$(which connect)...` | **The Bypass:** Forces SSH to travel *through* the university's HTTP proxy (`172.30.10.11:3128`) instead of trying to connect directly. |

---

## III. Verification & Connection Stability

Once you press **Enter**, look for the following signs of success:

1. **Host Key Authenticity:** If it asks `Are you sure you want to continue connecting (yes/no/[fingerprint])?`, type `yes` and press Enter.
2. **The Prompt:** You should eventually see `ubuntu@instance-XXXXXXXX:~$`. This means the tunnel is live.
3. **Active State:** **DO NOT CLOSE THIS WINDOW.** If the Git Bash window is closed, the local port `4444` dies, and your internet will disconnect immediately.

---

## IV. Critical Troubleshooting (The "1000 Cycles" Fixes)

If the command fails or hangs, check these specific failure points:

* **Connection Refused:** Your `SERVER_IP` is wrong, or you forgot to open Port 443 in the Oracle Cloud VCN Ingress Rules.
* **Permission Denied (publickey):** Your path to the `.key` file is wrong, or the key file has "too open" permissions. (In Git Bash, run `chmod 600 /path/to/key` to fix permissions).
* **ProxyCommand Error:** Ensure you are using **Git Bash**, not CMD. `$(which connect)` only works in a Bash environment.
* **Zero Speed/Stalling:** If the tunnel was working but now shows 0 B/s, the university proxy likely timed out the socket. Press `Ctrl + C` in Git Bash and re-run the command to reset the buffer.

---

> **Next Step:** Keep this Git Bash window open and proceed to **4_NEKORAY_SETUP.md** to configure the routing engine.
