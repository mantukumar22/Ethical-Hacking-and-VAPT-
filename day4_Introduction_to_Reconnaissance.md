# Day 04 — Introduction to Reconnaissance
[← Back to overview](README.md)

## What is Reconnaissance?
- The **initial phase** of a pentest/security assessment.
- The hacker gathers as much information as possible about a target system/network **before attempting any exploits**.
- Goal: understand system architecture, identify vulnerabilities, and find potential points of entry.

## Types of Reconnaissance

| Aspect | Passive Reconnaissance | Active Reconnaissance |
|---|---|---|
| **Definition** | Gathering info *without* direct interaction with target | *Direct interaction* with target system |
| **Techniques** | Public info sources, social media, OSINT tools (Google) | Port scanning, vulnerability scanning, network probing (Nmap, Nessus) |
| **Risk of Detection** | Low — no direct contact | High — direct engagement can trigger alerts/logs |
| **Information Accuracy** | Less detailed, relies on public data | More accurate & detailed |
| **Resource Intensity** | Low — uses readily available data | High — time and tool intensive |
| **Vulnerability ID** | Limited — no interaction with target | Effective — can identify specific weaknesses |
| **Use Case** | Initial stealthy info gathering | Pentesting phases needing detailed system insight |

## ➕ Extra context (added beyond the slides — tools to try)

**Passive recon tools/techniques:**
- `whois <domain>` — domain registration info
- Google Dorking (`site:`, `filetype:`, `intitle:`) — find exposed files/pages
- **theHarvester** — emails, subdomains, names from public sources
- **Shodan.io** — search engine for exposed devices/services
- Social media OSINT, LinkedIn, company "About/Careers" pages (reveal tech stack, employee names — useful for social engineering later)
- **Wayback Machine** — historical versions of a site

**Active recon tools/techniques:**
- `nmap` — port scanning, service/version detection (`nmap -sV -A <target>`)
- `nslookup` / `dig` — active DNS queries
- **Nessus / OpenVAS** — vulnerability scanners
- **Nikto** — web server vulnerability scanner
- Banner grabbing (`nc <ip> <port>`)

📝 *Practical rule of thumb:* Always start passive, stay invisible as long as possible, and only move to active scanning once the target scope is confirmed — active recon is noisier and can trip IDS/IPS alerts.

## 🧠 Quick Revision Questions
1. Why does passive recon carry lower risk of detection than active recon?
2. Name two passive and two active recon tools.
3. Why start with passive recon before moving to active?

## 🔑 New Glossary Terms
- **OSINT** — Open-Source Intelligence
- **Banner Grabbing** — technique to identify service/software version running on an open port
- **IDS/IPS** — Intrusion Detection/Prevention System

---
[← Day 03](day03.md) | [Back to overview](README.md) | [Next: Day 05 →](day05.md)
| **Information Accuracy** | Less detailed, relies on public data | More accurate & detailed |
| **Resource Intensity** | Low — uses readily available data | High — time and tool intensive |
| **Vulnerability ID** | Limited — no interaction with target | Effective — can identify specific weaknesses |
| **Use Case** | Initial stealthy info gathering | Pentesting phases needing detailed system insight |

## ➕ Extra context (added beyond the slides — tools to try)

**Passive recon tools/techniques:**
- `whois <domain>` — domain registration info
- Google Dorking (`site:`, `filetype:`, `intitle:`) — find exposed files/pages
- **theHarvester** — emails, subdomains, names from public sources
- **Shodan.io** — search engine for exposed devices/services
- Social media OSINT, LinkedIn, company "About/Careers" pages (reveal tech stack, employee names — useful for social engineering later)
- **Wayback Machine** — historical versions of a site

**Active recon tools/techniques:**
- `nmap` — port scanning, service/version detection (`nmap -sV -A <target>`)
- `nslookup` / `dig` — active DNS queries
- **Nessus / OpenVAS** — vulnerability scanners
- **Nikto** — web server vulnerability scanner
- Banner grabbing (`nc <ip> <port>`)

📝 *Practical rule of thumb:* Always start passive, stay invisible as long as possible, and only move to active scanning once the target scope is confirmed — active recon is noisier and can trip IDS/IPS alerts.

## 🧠 Quick Revision Questions
1. Why does passive recon carry lower risk of detection than active recon?
2. Name two passive and two active recon tools.
3. Why start with passive recon before moving to active?

## 🔑 New Glossary Terms
- **OSINT** — Open-Source Intelligence
- **Banner Grabbing** — technique to identify service/software version running on an open port
- **IDS/IPS** — Intrusion Detection/Prevention System

---
[← Day 03](day03.md) | [Back to overview](README.md) | [Next: Day 05 (upcoming) →]
