# Day 05 — Google Dorking (Google Hacking)
[← Day 04](day04.md) | [Back to overview](README.md) | [Next: Day 06 →](day06.md)

## What is Google Dorking?
- Also called **Google Hacking**.
- The use of **advanced search operators** in Google to find specific information that isn't visible through regular search queries.
- Leverages Google's search engine to locate hidden data on websites — sensitive files, configuration files, or vulnerabilities accidentally exposed to the public web.
- It's a form of **passive reconnaissance** (Day 04) — you never touch the target directly, you just search what Google already indexed.

## Core Google Dork Operators

| Dork | Description | Example Usage |
|---|---|---|
| `site:` | Restricts search results to a specific domain | `site:example.com` |
| `filetype:` | Searches for specific file types (PDF, DOC, XLS, etc.) | `filetype:pdf site:example.com` |
| `inurl:` | Finds URLs containing a specific string | `inurl:login` |
| `intitle:` | Looks for pages with specific words in the page title | `intitle:"Google Dorking"` |
| `allintitle:` | Searches for pages with **all** specified words in the title | `allintitle:Google Dorking Techniques` |
| `intext:` | Searches for specific text within the body of a web page | `intext:"open source intelligence"` |

📝 *My note:* You can **chain operators** together — that's where dorking gets powerful. e.g. `site:microsoft.com filetype:pdf` or `site:microsoft.com "Passwords" filetype:xlsx`.

## 🖥️ Practice Log (hands-on, followed along in Firefox on Kali)

**1. Scoped site search**
```
site:microsoft.com
```
→ Confirms which subdomains/pages Google has indexed for a domain (visualstudio.microsoft.com, clarity.microsoft.com, azure.microsoft.com, forms.microsoft.com, etc.) — useful for mapping an organization's attack surface / subdomains during recon.

![site: search results for microsoft.com](images/day05-site-search.png)

**2. Narrowing with a keyword**
```
site:microsoft.com "Xbox"
```
→ Google even auto-suggests related filter chips (Login, Games, Series X, Controller, Support...) — shows how a target's indexed content can be explored topic by topic.

![site: + keyword search showing filter chips](images/day05-site-xbox.png)

**3. Hunting for exposed file types**
```
site:microsoft.com filetype:pdf
```
→ Returns publicly indexed PDFs (Terms of Use, FAQs, internal-looking docs) hosted on the domain. This is the classic technique for finding leaked whitepapers, internal presentations, or configs that were never meant to be crawled.

![site: + filetype:pdf results](images/day05-filetype-pdf.png)

**4. Combining keyword + filetype (the "juicy" dork)**
```
site:microsoft.com "Passwords" filetype:xlsx
```
→ This is the dangerous combo — searching for a sensitive keyword **and** restricting to a spreadsheet filetype. In the demo, results were mostly false positives (community forum spreadsheets mentioning the word "passwords" in policy discussions), but this exact pattern is how real credential leaks get found in the wild. It illustrates *why* organizations must be careful about what gets indexed.

![site: + keyword + filetype:xlsx results](images/day05-passwords-xlsx.png)

**5. Exploit-DB's Google Hacking Database (GHDB)**
- Visited `https://www.exploit-db.com/google-hacking-database`
- A crowd-sourced, categorized library of ready-made dorks, e.g.:
  - `intext:"aws_access_key_id" | intext:"aws_secret_access_key" filetype:json | filetype:yaml` → *Files Containing Passwords*
  - `intitle:index of /etc/ssh` → *Files Containing Passwords*
  - `site:.edu filetype:xls "root" database` → *Files Containing Juicy Info*
  - `inurl:"cgi-bin/koha"` → *Vulnerable Servers*
- Each dork is tagged by **category** (Files Containing Passwords, Vulnerable Servers, Various Online Devices, Files Containing Juicy Info, Files Containing Usernames) and credited to the researcher who submitted it.

![Exploit-DB Google Hacking Database listing](images/day05-ghdb.png)

## ➕ Extra context (added beyond the slides)
- **Full list of useful operators not shown in slides:**
  - `cache:` — view Google's cached version of a page
  - `related:` — find sites related to a domain
  - `-` (minus) — exclude a term, e.g. `"SSL Network Extender Login" -checkpoint.com`
  - `"..."` (quotes) — exact phrase match
  - `|` (OR) — match either term, e.g. `filetype:json | filetype:yaml`
  - `*` (wildcard) — matches any word, e.g. `site:uat.* inurl:login`
- **GHDB (Google Hacking Database)** on Exploit-DB is maintained by the OffSec team and is the go-to reference once you understand the operators — browse it instead of memorizing every dork.
- Real-world defensive takeaway (Blue Team side): regularly **Google-dork your own organization** to find what's accidentally indexed, and use `robots.txt` / proper access controls to keep sensitive files out of search engines.
- Google Dorking is 100% passive/legal to *search* — the legal risk starts if you then try to access or download something you find that you're not authorized to have (e.g., an exposed private key or credential file).

## 🧠 Quick Revision Questions
1. What does chaining `site:` and `filetype:` together let you do that using them separately doesn't?
2. Why is Google Dorking considered passive reconnaissance?
3. What's the GHDB and who maintains it?
4. Name one operator not covered in the slide table and what it does.

## 🔑 New Glossary Terms
- **Google Dorking / Google Hacking** — using advanced search operators to find hidden/exposed data indexed by Google
- **GHDB** — Google Hacking Database (Exploit-DB's crowd-sourced dork repository)

---
[← Day 04](day04.md) | [Back to overview](README.md) | [Next: Day 06 →](day06.md)
