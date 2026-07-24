# Day 08 — Identifying Website Technologies
[← Day 07](day07.md) | [Back to overview](README.md) | [Next: Day 09 →](day09.md)

## What is Technology Fingerprinting?
- The process of identifying **what a website is built with** — CMS, JavaScript frameworks, analytics tools, tag managers, CDN/PaaS providers, and security services — purely by inspecting the public-facing site.
- A form of **passive reconnaissance**: no exploitation, just observation of headers, scripts, cookies, and page structure.
- Knowing the tech stack tells an attacker (or defender) which **known CVEs, default configs, or misconfigurations** might apply.

## Tools Covered

### 1. Wappalyzer (browser extension)
A Firefox/Chrome extension that scans the current page's source and network requests, then reports detected technologies grouped by category:
- **CMS** (e.g. Adobe Experience Manager)
- **Tag Managers** (Google Tag Manager, Adobe Experience Platform Launch)
- **Analytics** (Azure Monitor, Google Analytics GA4, ClickTale, Contentsquare, TikTok Pixel)
- **JavaScript Libraries** (jQuery, core-js, Lodash, lit-element)
- **Security** (HSTS, Akamai Bot Manager)
- **PaaS** (Azure)

### 2. BuiltWith (builtwith.com)
A web-based lookup tool — enter any domain and get a **Technology Profile** report, plus category-wide trend pages (e.g. "Marketing Automation Usage Distribution in the Top 1 Million Sites").

### 🖥️ Practice Log

**Wappalyzer scan on microsoft.com** — detected an extensive stack across CMS, analytics, tag managers, JS libraries, security, and PaaS categories:
![Wappalyzer results on microsoft.com](images/day08-wappalyzer-microsoft.png)

**BuiltWith homepage** — lookup tool + global trend stats (e.g. Marketing Automation usage across the top 1M sites, showing HubSpot as the most common tool):
![BuiltWith homepage and trends page](images/day08-builtwith-home.png)

**BuiltWith technology profile for youtube.com** — flagged with a "Misleading Technology Profile Warning" (BuiltWith admits it can't accurately fingerprint some large sites due to subdomain complexity), but still surfaced Facebook Domain Insights and Google Font API usage:
![BuiltWith technology profile for youtube.com](images/day08-builtwith-youtube.png)

📝 *My note:* Notice Wappalyzer detected almost nothing on a local file:// page (no network requests to analyze) but a rich stack on the live microsoft.com — a good reminder that these tools rely on **live network traffic and rendered scripts**, not just static HTML.

## Why It Matters
Tech fingerprinting shapes the entire next phase of an engagement. If a site reveals it's running an outdated CMS plugin or a specific analytics SDK with a known vulnerability, that becomes the first lead for **Gaining Access** (methodology Day 03). For defenders, minimizing exposed tech-stack fingerprints (removing version banners, generic error pages) is a basic hardening step.

## ➕ Extra Context
- Command-line alternative to Wappalyzer: **`whatweb <url>`** (pre-installed on Kali) — does the same fingerprinting from the terminal, scriptable for bulk scans.
- **`wafw00f`** (covered Day 10) works alongside these tools to specifically detect Web Application Firewalls.
- BuiltWith's free lookup is rate-limited; the paid tier unlocks historical tech-stack changes over time, useful for tracking when a company migrated CMS or added a new WAF.

## 🧠 Quick Revision Questions
1. Why did Wappalyzer detect "No technologies" on a local file:// page?
2. Name three categories of technology Wappalyzer reports on.
3. Why does BuiltWith sometimes show a "Misleading Technology Profile Warning"?

## 🔑 New Glossary Terms
- **Technology Fingerprinting** — identifying the software/services a website runs on via passive observation
- **PaaS** — Platform-as-a-Service (e.g. Azure, AWS Elastic Beanstalk)
- **Tag Manager** — a tool (e.g. Google Tag Manager) used to manage marketing/analytics scripts on a site without editing code directly

---
[← Day 07](day07.md) | [Back to overview](README.md) | [Next: Day 09 →](day09.md)
