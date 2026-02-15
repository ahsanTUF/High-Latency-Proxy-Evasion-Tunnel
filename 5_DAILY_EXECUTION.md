# Post-Restart Execution Protocol

Execute these steps upon Windows boot to re-establish the tunnel. Server variables are persistent.

## 1. Transport Activation
Open Git Bash and execute the SSH tunnel command:
\`\`\`bash
ssh -c aes128-gcm@openssh.com -o Compression=no -o TCPKeepAlive=yes -i "/c/Users/user/Documents/ahsan's TUF/Ethernet Bypass/ssh-key-2026-02-14.key" -L 4444:127.0.0.1:443 -o "ProxyCommand=$(which connect) -H 172.30.10.11:3128 %h %p" ubuntu@141.147.54.236
\`\`\`
Minimize the terminal.

## 2. Encapsulation Activation
1. Launch Nekoray.
2. Ensure **TUN Mode** is checked.
3. Start the `Master-Tunnel` profile.

## 3. Application Execution
1. Launch qBittorrent or Steam.
2. **Mandatory Rule:** Do not run both applications simultaneously. Enforce traffic isolation to protect the SSH transport layer from queue collapse.
