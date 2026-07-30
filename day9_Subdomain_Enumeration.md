# Day 09 — Subdomain Enumeration
[← Day 08](day08.md) | [Back to overview](README.md) | [Next: Day 10 →](day10.md)

## What are Subdomains?
- A subdomain is part of a larger domain name that helps organize and navigate different sections of a website.
- In a URL, the subdomain appears **before** the main domain name.
- Examples: `blog.example.com`, `shop.example.com`.

## Why Enumerate Subdomains?
Large organizations often run dozens (or thousands) of subdomains — staging environments, internal tools, regional sites, forgotten test servers — many of which are **less protected** than the main domain and can become the easiest way in.

## Tools Covered

### 1. Subfinder (terminal, Kali)
A fast passive subdomain enumeration tool from ProjectDiscovery.
```bash
subfinder -d microsoft.com
```
Loads provider config, then enumerates subdomains using passive sources (no direct probing of the target).

### 2. Subdomain Finder (subdomainfinder.c99.nl)
A free web-based tool that scans a domain and returns every discovered subdomain along with its resolved IP and whether it sits behind Cloudflare — plus export options (CSV/JSON) and a Whois-check shortcut.

### 🖥️ Practice Log

**Running subfinder against microsoft.com:**
![subfinder running against microsoft.com](images/day09-subfinder-running.png)

**Subfinder results — hundreds of real subdomains discovered**, including things like `mail14-am1on0100.outbound.messaging.microsoft.com`, `login.groove.microsoft.com`, `certification.drm.microsoft.com`, and internal-looking hosts like `ppe.adcenterapi.microsoft.com` (PPE = pre-production environment):
![subfinder results listing subdomains](images/day09-subfinder-results.png)

📝 *My note:* The tool reported **hundreds of subdomains found in ~11 seconds** — entirely passively, using indexed DNS data rather than brute-forcing. Several names (`-uat-`, `-sit-`, `ppe.`) hint at staging/testing environments, which are exactly the kind of target a real pentester would flag for closer inspection.

**Subdomain Finder (c99.nl) scan of paytm.com — 164 subdomains found**, most-used IP identified, with a full table of subdomain → IP → Cloudflare status, plus Whois-check and CSV/JSON export buttons:
![c99.nl subdomain finder results for paytm.com](images/day09-c99-paytm-results.png)

📝 *My note:* Notice subdomains like `accounts-analyticsapp.paytm.com`, `api-payouts.paytm.com`, and `app.loyalty-internal-cloud.paytm.com` resolving to an internal-looking `10.60.16.15` IP — a strong signal of an internal service accidentally exposed via public DNS.

## Why It Matters
Every subdomain is a **new attack surface**. A forgotten staging subdomain running an old app version, or an internal tool exposed by mistake, is often far easier to compromise than the hardened production domain. Subdomain enumeration is a core step between Reconnaissance and Scanning in the 5-phase methodology (Day 03).

## ➕ Extra Context
- Other popular subdomain tools: **Amass**, **Sublist3r**, **assetfinder**, **crt.sh** (searches SSL certificate transparency logs — certificates often leak internal subdomain names).
- Passive tools (Subfinder, c99.nl) don't touch the target directly; combining results with **active** verification (e.g. `httpx` to check which subdomains are actually live) is the natural next step.
- Defensive takeaway: audit DNS records regularly and decommission unused subdomains — they're free real estate for attackers otherwise.

## 🧠 Quick Revision Questions
1. What's the difference between a subdomain and a top-level domain (TLD)?
2. Why might a subdomain like `ppe.adcenterapi.microsoft.com` be more interesting to a pentester than the main site?
3. Name one tool (besides Subfinder) that can enumerate subdomains via certificate transparency logs.

## 🔑 New Glossary Terms
- **Subdomain** — a prefix to a domain name denoting a subsection of a site/organization
- **PPE** — Pre-Production Environment (staging, often less hardened than production)
- **Certificate Transparency Log** — public log of issued SSL/TLS certificates, often leaks subdomain names

---
[← Day 08](day08.md) | [Back to overview](README.md) | [Next: Day 10 →](day10.md)
