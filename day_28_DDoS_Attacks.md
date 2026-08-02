# Day 28 — DDoS Attacks

## 🎯 Goal
Understand DoS/DDoS attacks, botnets, and practice with stress-testing tools (`LOIC`, `Net-Strike`).

---

## 📖 What is a DoS Attack?
A **Denial-of-Service** attack aims to make a computer or network resource **unavailable to its intended users**, typically by overwhelming the target with excessive traffic/requests, disrupting normal operations.

- **DoS** = attack from a single source
- **DDoS** (Distributed Denial-of-Service) = attack from many sources simultaneously (often a **botnet**)

---

## 🤖 How a Botnet Works
```
1. Infection            → Malware spreads via spam emails, infected websites, social media posts
2. Connection            → Infected machine (bot/zombie) connects to Command & Control (C2) server
3. Control                → Cybercriminal (botmaster) issues commands via C2
4. Multiplication         → Bot spreads malware to more machines, growing the botnet
```
Each infected machine becomes a "zombie" that can be remotely commanded to flood a target simultaneously — this is what powers most large-scale DDoS attacks.

---

## 📊 Types of DDoS Attacks

| Type | Description |
|---|---|
| **Volume-Based Attacks** | Overwhelm the target with high traffic volumes — e.g., **ICMP floods** or **UDP floods** — saturating bandwidth |
| **Protocol Attacks** | Exploit weaknesses in network protocols — e.g., **SYN floods** — consume server resources via incomplete connection (handshake) requests |
| **Application-Layer Attacks** | Target specific application functions (e.g., HTTP floods) mimicking legit traffic — hardest to detect |

---

## 🛠️ Tools

### 1. LOIC (Low Orbit Ion Cannon)
- A **network stress testing application** (downloadable via SourceForge).
- Attack options: **Timeout**, **HTTP Subsite**, **TCP/UDP message**, **Port**, **Method (UDP/TCP/HTTP)**, **Threads**, **Speed slider**
- UI shows: Selected target IP, Attack status (Idle/Connecting/Requesting/Downloading/Downloaded/Requested/Failed counters)
- Example: targeting `192.168.29.150` with a UDP flood on port 80, 10 threads.

> ⚠️ Downloads flagged "Malware Detected — download at own risk" on SourceForge — use only in isolated lab environments (e.g. against your own Metasploitable VM).

### 2. Net-Strike
```bash
git clone <Net-Strike repo>
cd Net-Strike
python app.py     # or similar entry script
```
Menu:
```
───── Attack Types ─────
1. TCP SYN Flood
2. ICMP Flood
3. UDP Flood
4. HTTP Flood
5. Exit
[>] Select Attack Type »
```
- Author: `isPique`, Version 1.0
- Lets you choose attack type interactively and specify a target IP/port.

---

## 🧪 Lab Setup Notes
- Practiced against a local **Metasploitable 2** VM (`192.168.29.150`) inside VirtualBox — never test on systems you don't own/have written permission for.
- Monitored target system resource usage (`top` command) during flood — showed spikes in CPU (`python`, `shred` processes) confirming load impact.

---

## ✅ Key Takeaways
- DDoS ≠ hacking in the traditional sense — it's about **availability**, not confidentiality/integrity, but still illegal without authorization.
- Botnets are the backbone of most large-scale DDoS attacks (e.g., Mirai botnet using IoT devices).
- Defenses include: rate limiting, Web Application Firewalls (WAF), **Anycast/CDN** distribution (e.g., Cloudflare), SYN cookies for protocol attacks.
- Tools like LOIC/Net-Strike are for **learning attack mechanics in a lab**, never for real-world targets.
- 
