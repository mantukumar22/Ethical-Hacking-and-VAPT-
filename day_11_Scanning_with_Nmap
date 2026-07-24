# Day 11 — Scanning with Nmap
[← Day 10](day10.md) | [Back to overview](README.md) | [Next: Day 12 →](day12.md)

## What are Ports?
- In computer networking, **ports** are like doorways or entry points that allow different types of data to flow in and out of a device or network.
- Each port is associated with a specific service or application, so different types of data are directed to the correct program.

## What are Protocols?
- **Protocols** are sets of rules or standards that define how data is transmitted and received between devices.
- They ensure devices (computers, smartphones, servers) can communicate in a way everyone understands, regardless of manufacturer or device type.

## Common Ports & Services Reference

| Port | Protocol | Service | Use |
|---|---|---|---|
| 20 | TCP | FTP Data Transfer | Transferring files in FTP sessions |
| 21 | TCP | FTP Command Control | Commands and control in FTP sessions |
| 22 | TCP | SSH | Secure remote login and command execution |
| 25 | TCP | SMTP | Sending emails between servers |
| 53 | UDP | DNS | Resolving domain names to IP addresses |
| 80 | TCP | HTTP | Transmitting web pages over the internet |
| 443 | TCP | HTTPS | Secure web traffic, encrypting client↔server data |

## Tool Covered: Nmap
**Nmap ("Network Mapper")** is the industry-standard tool for host discovery, port scanning, service/version detection, OS fingerprinting, and scriptable vulnerability checks (NSE).

### Key flag categories (from `nmap` help output)
- **Target specification**: hostnames, IPs, ranges, CIDR (`microsoft.com/24`, `10.0.0-255.1-254`)
- **Host discovery**: `-sL` (list scan), `-sn` (ping scan, no port scan), `-Pn` (skip host discovery, treat all as up)
- **Scan techniques**: `-sS/-sT/-sA/-sW/-sM` (TCP SYN/Connect/ACK/Window/Maimon), `-sU` (UDP), `-sN/-sF/-sX` (Null/FIN/Xmas stealth scans)
- **OS detection**: `-O`
- **Timing & performance**: `-T<0-5>` (timing templates, higher = faster/noisier)
- **Firewall/IDS evasion & spoofing**: `-f` (fragment packets), `-D` (decoy scan), `-S` (spoof source IP), `-e` (specify interface)
- **Output options**: `-oA` (all 3 formats at once), `-v`/`-d` (verbosity/debug), `--reason`, `--open` (show only open ports)
- **Misc**: `-6` (IPv6), `-A` (enables OS detection + version detection + script scanning + traceroute all at once)

### 🖥️ Practice Log

**`sudo nmap -sS -sV -sC -O scanme.nmap.org`** — SYN stealth scan + version detection + default scripts + OS detection, against Nmap's official public test target:
![sudo nmap password prompt before scan](images/day11-nmap-sudo-prompt.png)

📝 *My note:* This scan took a **very long time** (stats showed it still running past 50 minutes elapsed) — a good real-world lesson that combining `-sS -sV -sC -O` against a live target is thorough but slow. Faster/narrower scans (e.g. specifying `-p` for specific ports, or a higher `-T` timing template) are more practical for quick recon.

**`sudo nmap -A microsoft.com`** — aggressive scan (OS detection, version detection, script scanning, traceroute) against microsoft.com:
```
Nmap scan report for microsoft.com (20.76.201.171)
Host is up (0.030s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    Kestrel
|_http-title: Did not follow redirect to https://www.microsoft.com/
|_http-server-header: Kestrel
```
![nmap -A results against microsoft.com showing port 80 open running Kestrel](images/day11-nmap-microsoft-results.png)

📝 *My note:* Nmap identified port 80 open, running **Kestrel** (the cross-platform web server used by ASP.NET Core) and correctly noted it redirects to HTTPS. Most other ports came back filtered — a sign of a firewall dropping unsolicited traffic rather than actively refusing it (which would show as "closed").

## Why It Matters
Scanning is Phase 2 of the Ethical Hacking Methodology (Day 03) — this is where reconnaissance data turns into a concrete list of **open ports, running services, and versions** that can be cross-referenced against known vulnerabilities (CVEs) in the next phase.

## ➕ Extra Context
- **Filtered vs Closed vs Open**: *Open* = service actively listening; *Closed* = port reachable but nothing listening; *Filtered* = Nmap can't tell because a firewall is dropping/blocking probes.
- `-T4` is a commonly used "safe but fast" timing template for CTFs/labs; `-T0`/`-T1` are used to evade IDS detection at the cost of speed.
- Nmap's **NSE (Nmap Scripting Engine)** (`-sC` or `--script=`) can run vulnerability-detection scripts (e.g. `vuln` category) directly during a scan — worth exploring once comfortable with basic scans.
- Always confirm scan authorization — scanning `scanme.nmap.org` is explicitly permitted by Nmap's own project for practice; scanning random third-party domains is not.

## 🧠 Quick Revision Questions
1. What's the difference between `-sS` and `-sT` scan types?
2. Why might most ports show as "filtered" rather than "closed" on a scan?
3. What does the `-A` flag combine into a single scan?
4. Which port/service did the microsoft.com scan find open, and what software was running on it?

## 🔑 New Glossary Terms
- **Port** — a numbered logical endpoint for network communication tied to a specific service
- **Protocol** — a rule set governing how data is transmitted/received (e.g. TCP, UDP, HTTP)
- **NSE (Nmap Scripting Engine)** — Nmap's built-in scripting framework for automated recon/vuln checks
- **Kestrel** — the default cross-platform web server for ASP.NET Core applications

---
[← Day 10](day10.md) | [Back to overview](README.md) | [Next: Day 12 →](day12.md)
