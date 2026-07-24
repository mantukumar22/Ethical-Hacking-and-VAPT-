# 🛡️ Ethical Hacking — 30 Day Learning Plan
*Following: "Ultimate Ethical Hacking Full Course 2026 in Hindi | Kali Linux" — InventYourShit*

This repo is my personal study log for learning ethical hacking in 30 days. Each day of the course gets its own notes file (`day01.md`, `day02.md`, ...). This README is the **overview, syllabus, and study plan** — the "what, how, and when" — not the detailed notes themselves.

⚠️ **Legal & Ethics Reminder (read this before Day 1):**
Everything in this course is legal **only** on systems you own or have explicit written permission to test — your own lab VMs, TryHackMe/HackTheBox boxes, or an authorized bug bounty scope. Practicing these techniques on real people, companies, or accounts without authorization is a crime (e.g., CFAA in the US, IT Act 2000 in India). Every day's practice work should happen inside an isolated VM/lab, never on live third-party systems.

---

## 🎯 Why I'm doing this
To build practical, hands-on offensive security skills (recon → exploitation → post-exploitation → reporting) as a foundation toward roles like Penetration Tester / SOC Analyst / Bug Bounty Hunter, and eventually certifications like **eJPT → CompTIA PenTest+ → OSCP**.

## 🧰 What I need before starting
- [ ] A host laptop/PC (8GB+ RAM recommended)
- [ ] VirtualBox or VMware installed
- [ ] Kali Linux VM (image or ISO from kali.org)
- [ ] A free TryHackMe and/or HackTheBox account (for legal practice labs)
- [ ] Note-taking setup (this repo!) + a snapshot/rollback habit for the VM

## 🗺️ How I'm structuring the learning
1. **Watch** the day's section of the course video.
2. **Summarize** slides into that day's `dayXX.md` file in my own words.
3. **Expand** each topic with extra context/tools/commands the video didn't fully cover.
4. **Practice** hands-on in Kali/TryHackMe before moving to the next day.
5. **Review** the glossary + quiz questions at the end of the week.

🖼️ **All practice screenshots live in [`images/`](images/)** and are indexed in **[IMAGES.md](IMAGES.md)** — check there if a picture isn't rendering in a day's notes.

---

## 📅 30-Day Syllabus & Progress Tracker

### Week 1 — Foundations & Recon
| Day | Topic | Notes File | Status |
|---|---|---|---|
| 1 | Ethical Hacking 101 (what is hacking, hacker types, testing types, red/blue/purple team) | [day01.md](day01.md) | ✅ Done |
| 2 | Installing Kali Linux (VirtualBox setup) | [day02.md](day02.md) | ✅ Done |
| 3 | Ethical Hacking Methodology & Cyber Kill Chain | [day03.md](day03.md) | ✅ Done |
| 4 | Intro to Reconnaissance (passive vs active) | [day04.md](day04.md) | ✅ Done |
| 5 | Google Dorking (Google Hacking) | [day05.md](day05.md) | ✅ Done |
| 6 | WHOIS & DNS Recon | [day06.md](day06.md) | ✅ Done |
| 7 | Social Media Recon | [day07.md](day07.md) | ✅ Done |

### Week 2 — Fingerprinting, Recon Depth & Scanning
| Day | Topic | Notes File | Status |
|---|---|---|---|
| 8 | Identifying Website Technologies (Wappalyzer, BuiltWith) | [day08.md](day08.md) | ✅ Done |
| 9 | Subdomain Enumeration (Subfinder, c99.nl) | [day09.md](day09.md) | ✅ Done |
| 10 | Identifying Firewalls / WAF (WAFW00F) | [day10.md](day10.md) | ✅ Done |
| 11 | Scanning with Nmap (ports, protocols, scan types) | [day11.md](day11.md) | ✅ Done |
| 12 | Directory Bruteforcing (Gobuster, Feroxbuster) + TryHackMe Vulnversity | [day12.md](day12.md) | ✅ Done |
| 13 | Social Engineering Fundamentals | day13.md | ⬜ |
| 14 | Metasploit Basics | day14.md | ⬜ |

### Week 3 — Exploitation & Attacks
| Day | Topic | Notes File | Status |
|---|---|---|---|
| 15 | Exploitation with Metasploit | day15.md | ⬜ |
| 16 | Bruteforce Attacks | day16.md | ⬜ |
| 17 | SQL Injection Attacks | day17.md | ⬜ |
| 18 | XSS Attacks | day18.md | ⬜ |
| 19 | Dumping Hashes with Mimikatz | day19.md | ⬜ |
| 20 | Password Cracking | day20.md | ⬜ |
| 21 | Clearing Tracks | day21.md | ⬜ |

### Week 4 — Anonymity, Social Engineering & Real Attacks
| Day | Topic | Notes File | Status |
|---|---|---|---|
| 22 | Becoming Anonymous while Hacking | day22.md | ⬜ |
| 23 | Port Forwarding 101 | day23.md | ⬜ |
| 24 | Social Engineering 101 | day24.md | ⬜ |
| 25 | Hacking Instagram (social eng. demo) | day25.md | ⬜ |
| 26 | DDoS Attacks | day26.md | ⬜ |
| 27 | OS Login Phishing | day27.md | ⬜ |
| 28 | TryHackMe: Vulnversity (guided lab) | day28.md | ⬜ |

### Final Days — Consolidation
| Day | Topic | Notes File | Status |
|---|---|---|---|
| 29 | Full Review + Glossary Consolidation | day29.md | ⬜ |
| 30 | Capstone Lab (apply full methodology end-to-end on a TryHackMe box) | day30.md | ⬜ |

*(Days 1–12 are confirmed from the actual video content and timestamps. Days 13+ topic labels are still estimated from the course's "What We Will Cover" list and will be corrected as each day is actually completed.)*

---

## 📖 Master Glossary (living document — grows every week)
| Term | Meaning |
|---|---|
| CIA Triad | Confidentiality, Integrity, Availability |
| OSINT | Open-Source Intelligence |
| CVE | Common Vulnerabilities and Exposures |
| Payload | Code delivered to exploit a vulnerability |
| Zero-day | Vulnerability unknown to the vendor, no patch exists |
| Rootkit | Software hiding the presence of malware/unauthorized access |
| C2 (Command & Control) | Attacker's remote channel to control compromised systems |
| Banner Grabbing | Identifying service/software version running on an open port |
| IDS/IPS | Intrusion Detection/Prevention System |
| Google Dorking / GHDB | Advanced Google search operators / Exploit-DB's dork repository |
| WHOIS | Protocol/database for domain registration ownership lookup |
| DNS Record Types (A/AAAA/CNAME/NS/SOA/TXT/MX) | See Day 06 for full breakdown |
| Zone Transfer (AXFR) | DNS mechanism for replicating zone data — risky if misconfigured |
| Username Enumeration | Checking if the same username exists across many platforms (e.g. Sherlock) |
| Knowledge Panel | Google's structured info box summarizing an entity with links to official profiles |
| Technology Fingerprinting | Identifying a site's tech stack via passive observation (Wappalyzer, BuiltWith) |
| PaaS | Platform-as-a-Service (e.g. Azure, AWS Elastic Beanstalk) |
| Subdomain / PPE | A prefixed section of a domain; PPE = Pre-Production Environment |
| Certificate Transparency Log | Public log of issued SSL/TLS certs — often leaks subdomain names |
| WAF (Web Application Firewall) | A firewall filtering HTTP traffic to protect web apps specifically |
| WAFW00F | Open-source WAF fingerprinting tool |
| Port / Protocol | Logical network endpoint / rule set governing data transmission |
| NSE (Nmap Scripting Engine) | Nmap's built-in scripting framework for automated recon/vuln checks |
| Directory Bruteforcing | Systematically requesting wordlist-based paths to discover hidden files/folders |
| Soft 404 | A page returning HTTP 200 but displaying "not found" content |
| Rate Limiting (429) | A server defense throttling repeated requests from the same source |

*(Each day's file adds new terms here as they come up.)*

## 🏆 Certifications to consider after this course
- **eJPT** (entry-level, practical)
- **CompTIA PenTest+** (foundational, exam-based)
- **OSCP** (industry-respected, hands-on, harder)
- **CEH** (widely recognized, more theory-heavy)

## 🔗 Legal Practice Platforms
- [TryHackMe](https://tryhackme.com)
- [HackTheBox](https://hackthebox.com)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) (for SQLi/XSS days)
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/) (self-hosted vulnerable web app)

---
*This README is the map. Day-by-day details live in their own files — start with [day01.md](day01.md).*
