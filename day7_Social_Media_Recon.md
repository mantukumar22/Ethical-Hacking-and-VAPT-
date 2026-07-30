# Day 07 — Social Media Recon
[← Day 06](day06.md) | [Back to overview](README.md) | [Next: Day 08 →](day08.md)

## What is Social Media Recon?
- An extension of **passive OSINT reconnaissance** (Day 04) that focuses specifically on the human layer of an organization — employees, executives, and official brand accounts.
- Goal: build a picture of an organization's people, culture, tech stack hints, and potential social-engineering angles, all from publicly available profiles.

## Practical Techniques Covered

**1. Google Knowledge Panel recon**
Searching a company name directly often surfaces a structured knowledge panel: CEO, CFO, CTO, founders, stock price, headquarters, and — critically — direct links to the company's official **Instagram, X (Twitter), LinkedIn, and Facebook** profiles.

**2. Official company profiles**
- **LinkedIn company page**: follower count, employee count, "About" mission statement, affiliated/acquired pages (e.g. Skype under Microsoft), stock info.
- **Instagram**: official brand posts — useful for tone, campaigns, and sometimes staff shown on camera.

**3. Finding employees via Google Dorking + LinkedIn**
```
site:linkedin.com/in working at "microsoft.com"
```
This dork combines Day 05's Google Dorking skills with social recon — it surfaces real employee LinkedIn profiles (HR managers, engineers, executives) indexed publicly, without ever touching LinkedIn's own search/API.

**4. Individual employee profile recon**
Opening an individual profile (e.g. an HR professional's LinkedIn) reveals:
- **About** section — role scope, specializations
- **Services** offered — coaching, consulting, recruitment focus
- **Activity** — recent comments/posts (useful for understanding internal culture, org changes, who reports to whom)
- **"People also viewed" / colleagues panel** — maps out adjacent employees (other HR/recruiting staff at the same company), useful for building an org chart during recon.

**5. Username enumeration across platforms — Sherlock**
```bash
sherlock williamhgates
```
Sherlock checks a single username against dozens of platforms (GitHub, Reddit, Twitter/X, Instagram, LinkedIn, Twitch, Venmo, Xbox Gamertag, etc.) and reports every site where that username exists.

### 🖥️ Practice Log

**Sherlock username check:**
![Sherlock checking username williamhgates](images/day07-sherlock-cmd.png)

**Sherlock results — 21 matches found across platforms:**
![Sherlock results showing 21 matched accounts](images/day07-sherlock-results.png)

📝 *My note:* Sherlock returned real accounts on AllMyLinks, GitHub, LinkedIn, Reddit, Twitch, Twitter/X, Venmo, and even Xbox Gamertag — all from a single username guess. This is exactly how attackers correlate a person's identity across the internet before a targeted phishing or social-engineering attempt.

## Why It Matters
Social media recon is the bridge between **technical OSINT** (Days 04–06) and **human-layer attacks** like phishing and social engineering (covered later in the course). Defenders should assume that an organization's entire employee roster, reporting structure, and even personal usernames can be mapped from public data alone — which is why security awareness training exists.

## ➕ Extra Context
- **Sherlock** is open-source and pre-installed on many recon-focused Kali setups; install via `pip install sherlock-project` or `git clone` if missing.
- Combine Sherlock results with LinkedIn dorking to cross-reference a real name to a set of consistent usernames — a common first step in red-team social-engineering engagements.
- Ethical/legal note: this is all public-record OSINT. It crosses into a problem only if used to harass, impersonate, or gain unauthorized access.

## 🧠 Quick Revision Questions
1. What dork syntax finds LinkedIn employee profiles for a specific company domain?
2. What does Sherlock do, and what is its input?
3. Why is the "Activity" tab on a LinkedIn profile useful during recon?

## 🔑 New Glossary Terms
- **Username Enumeration** — checking whether the same username/handle exists across many platforms to correlate identity
- **Knowledge Panel** — Google's structured info box summarizing an entity (person/company) with quick links to official profiles

---
[← Day 06](day06.md) | [Back to overview](README.md) | [Next: Day 08 →](day08.md)
