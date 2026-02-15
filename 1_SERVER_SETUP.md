# Server Infrastructure Optimization

## 1. OS Provisioning
Deploy an Ubuntu 24.04 LTS instance (e.g., Oracle Cloud).
Ensure port 443 is open on the ingress firewall rules.

## 2. Proxy Core Installation
Deploy an Xray/VLESS proxy core on the server.
Bind the proxy listening port strictly to `127.0.0.1:443`.

## 3. TCP Congestion Control (BBR)
The remote server must use BBR to sustain throughput across high-latency connections and ignore proxy packet drops.

Execute via SSH:
\`\`\`bash
echo "net.core.default_qdisc=fq" | sudo tee -a /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
\`\`\`
