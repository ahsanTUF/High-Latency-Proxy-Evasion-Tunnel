# Local Transport Bridge

Establish a hardware-accelerated TCP tunnel bypassing local proxy restrictions using OpenSSH.

## 1. Dependencies
Install [Git for Windows](https://gitforwindows.org/). This provides the `connect.exe` binary required to route SSH traffic through an HTTP proxy.

## 2. Execution Command
Open Git Bash. Execute the transport tunnel command. 

Modify the identity file path (`-i`), local proxy IP (`-H`), and server IP to match the active environment variables:

\`\`\`bash
ssh -c aes128-gcm@openssh.com -o Compression=no -o TCPKeepAlive=yes -i "/c/Users/user/Documents/ahsan's TUF/Ethernet Bypass/ssh-key-2026-02-14.key" -L 4444:127.0.0.1:443 -o "ProxyCommand=$(which connect) -H 172.30.10.11:3128 %h %p" ubuntu@141.147.54.236
\`\`\`

## 3. State Management
Retain the active terminal state. Do not close Git Bash while the network is required.
