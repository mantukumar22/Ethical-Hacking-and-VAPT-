# Day 03 — Ethical Hacking Methodology & Cyber Kill Chain
[← Back to overview](README.md)

## 5-Phase Ethical Hacking Methodology
1. **Reconnaissance / Footprinting**
2. **Scanning**
3. **Gaining Access**
4. **Maintaining Access**
5. **Clearing Tracks**

| Phase | Description | Techniques Used |
|---|---|---|
| **Reconnaissance** | Gathering info about the target to identify potential vulnerabilities | Google Dorking, WHOIS lookups, DNS enumeration, Social engineering tactics |
| **Scanning** | Using tools to identify vulnerabilities in the target system | Network mapping, Port scanning, Vulnerability scanning |
| **Gaining Access** | Attempting to exploit identified vulnerabilities for unauthorized access | SQL injection, Buffer overflow attacks, Phishing attacks |
| **Maintaining Access** | Establishing a persistent connection to assess duration of undetected access | Installing rootkits, creating hidden user accounts, tunneling |
| **Clearing Tracks** | Removing traces of activity from system logs | Modifying/deleting log files, deleting evidence of unauthorized access |

## Cyber Kill Chain Methodology (Lockheed Martin model — 7 stages)
`Recon → Weaponization → Delivery → Exploitation → Installation → Command & Control (C2) → Actions on Objectives`

| Phase | Description | Techniques Used |
|---|---|---|
| **Installation** | Attacker installs malware to establish a persistent foothold | Installing backdoors, setting up remote access tools |
| **Command and Control** | Attacker sets up a C2 channel to remotely manipulate the compromised system undetected | Creating covert communication channels, sending commands to malware |
| **Actions on Objectives** | Attacker carries out their final goal | Data exfiltration, ransomware encryption, service disruption |

📝 *My note — how the two models relate:*
The 5-phase methodology is the **ethical hacker's** internal workflow. The Cyber Kill Chain describes how **real attackers** operate end-to-end — used by **Blue Teams** to decide at which stage to detect/block an intrusion. Rough mapping:

| Ethical Hacking Phase | Roughly Maps to Kill Chain Stage(s) |
|---|---|
| Reconnaissance | Recon |
| Scanning | Recon / Weaponization |
| Gaining Access | Delivery / Exploitation |
| Maintaining Access | Installation / C2 |
| Clearing Tracks | Actions on Objectives (covering tracks) |

## ➕ Extra context (added beyond the slides)
- **MITRE ATT&CK Framework**: a more modern, granular companion/alternative to the Kill Chain — maps specific real-world attacker Tactics, Techniques & Procedures (TTPs). Worth exploring once comfortable with the Kill Chain basics.
- Blue teams try to disrupt attacks at the *earliest possible* stage — the earlier the catch, the less damage done.

## 🧠 Quick Revision Questions
1. List the 5 phases of the ethical hacking methodology in order.
2. What's the difference between "Installation" and "Command & Control" in the Kill Chain?
3. Why is disrupting an attack early in the Kill Chain preferable to disrupting it later?

## 🔑 New Glossary Terms
- **C2 (Command & Control)** — attacker's remote channel to control a compromised system
- **Rootkit** — software designed to hide the presence of malware/unauthorized access
- **MITRE ATT&CK** — framework cataloging real-world attacker tactics & techniques

---
[← Day 02](day02.md) | [Back to overview](README.md) | [Next: Day 04 →](day04.md)
