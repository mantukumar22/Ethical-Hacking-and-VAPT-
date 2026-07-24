# Day 10 — Identifying Firewalls (WAF)
[← Day 09](day09.md) | [Back to overview](README.md) | [Next: Day 11 →](day11.md)

## What is a Firewall?
- A **firewall** is a security system designed to monitor and control incoming and outgoing network traffic based on predetermined security rules.
- It acts as a barrier between a trusted internal network (home/office) and untrusted external networks (the internet).
- A **WAF (Web Application Firewall)** is a specialized firewall that filters, monitors, and blocks HTTP traffic to and from a web application specifically — protecting against things like SQL injection, XSS, and bad bots at the application layer.

## Tool Covered: WAFW00F
**WAFW00F** — "The Web Application Firewall Fingerprinting Toolkit" — sends requests to a target and analyzes the response differences to determine if a WAF is present, and often **which vendor's WAF** it is.

```bash
wafw00f url1 [url2 [url3 ... ]]
# example: wafw00f http://www.victim.org/
```

### 🖥️ Practice Log

**WAFW00F banner / usage (no target specified):**
![wafw00f banner and usage info](images/day10-wafw00f-banner.png)

**Scanning real targets — spotify.com and amazon.in:**
```
[*] Checking https://spotify.com
[+] Generic Detection results:
[*] The site https://spotify.com seems to be behind a WAF or some sort of security solution
[~] Reason: The response was different when the request wasn't made from a browser.
Normal response code is "403", while the response code to a modified request is "200"
[~] Number of requests: 4

[*] Checking https://amazon.in
[+] The site https://amazon.in is behind Cloudfront (Amazon) WAF.
[~] Number of requests: 2
```
![wafw00f results for spotify.com and amazon.in](images/day10-wafw00f-results.png)

📝 *My note:* Two different detection outcomes in one session — **Generic Detection** (spotify.com: WAF presence confirmed, but vendor unknown, based on inconsistent response codes to modified requests) vs. a **specific vendor match** (amazon.in: identified as Cloudfront/Amazon WAF by its signature). This distinction matters — a named WAF means an attacker can look up known bypass techniques for that specific product.

## Why It Matters
Knowing whether a WAF is present — and which one — changes the entire approach to later exploitation phases. Attempting SQL injection or XSS payloads against a WAF-protected endpoint without WAF-aware evasion techniques will simply get blocked (and logged, alerting the defenders). This is why WAF fingerprinting happens **before** any exploitation attempt in a real engagement.

## ➕ Extra Context
- Common WAF vendors WAFW00F can detect: Cloudflare, Akamai, Imperva/Incapsula, AWS WAF, F5 BIG-IP ASM, Sucuri, and more.
- WAF detection results directly inform which tools/techniques are safe to use later — e.g., automated scanners like sqlmap have `--tamper` scripts specifically designed to bypass known WAFs.
- Defensive angle: a well-configured WAF should give **consistent** responses regardless of how a request is crafted — inconsistency (like spotify.com's 403→200 shift) is itself a fingerprintable signal.

## 🧠 Quick Revision Questions
1. What's the difference between a generic firewall and a Web Application Firewall (WAF)?
2. How did WAFW00F conclude amazon.in was behind a WAF, and how is that different from how it flagged spotify.com?
3. Why does identifying the *specific* WAF vendor matter for later exploitation phases?

## 🔑 New Glossary Terms
- **WAF (Web Application Firewall)** — a firewall that filters HTTP-specific traffic to protect web applications
- **WAFW00F** — an open-source WAF fingerprinting tool
- **Generic Detection** — identifying that *some* WAF/security solution is present without naming the specific vendor

---
[← Day 09](day09.md) | [Back to overview](README.md) | [Next: Day 11 →](day11.md)
