# Self-Hosted Pi-hole DNS Server (Docker on Linux Mint)

A documented guide for setting up and troubleshooting a network-wide ad-blocking and tracking-prevention DNS server running via Docker on Linux Mint.

## 🛠️ Stack & Architecture
- **OS:** Linux Mint
- **Containerization:** Docker & Docker Compose
- **DNS Server:** Pi-hole v6
- **Web Admin Interface:** Bound to port `8080`
- **DNS Service (Port 53):** Correctly bound and listening via `docker-proxy` (`0.0.0.0:53`)

---

## 🔍 Comprehensive Troubleshooting & Solutions Log

### 1. iPhone DNS Configuration & Port 53 Verification
- Configured manual DNS on the iOS client pointing to the laptop's local IP address.
- Verified DNS port activation via command line tools (`ss -tuln`) to ensure port 53 (TCP/UDP) was active.

### 2. Resolving "Non-Local Network" Dropped Queries
- **Symptoms:** Queries arrived from the local network, but were dropped with: `ignoring query from non-local network 192.xxx.xx.xx`.
- **Root Cause:** Pi-hole's internal security settings treated requests coming across the Docker bridge as non-local.
- **Solution:** Changed Pi-hole's **Interface settings** in the web dashboard from *"Allow only local requests"* to **"Permit all origins"**.

### 3. Upstream DNS Forwarding
- Configured robust **Upstream DNS Servers** (Cloudflare `1.1.1.1` / Google `8.8.8.8`) under **Settings -> DNS**.

### 4. Firewall Validation (UFW)
- Ensured local firewall rules allowed DNS traffic (`sudo ufw allow 53/tcp`, `sudo ufw allow 53/udp`).

---

## 🚀 Deployment Reference (Docker Run)
```bash
docker run -d \
  --name pihole \
  -p 53:53/tcp \
  -p 53:53/udp \
  -p 8080:80/tcp \
  -e TZ="America/New_York" \
  -e FTLCONF_LOCAL_IPV4="192.168.x.x" \
  --restart unless-stopped \
  pihole/pihole:latest
