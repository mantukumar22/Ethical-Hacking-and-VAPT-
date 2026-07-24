# Day 12 — Directory Bruteforcing
[← Day 11](day11.md) | [Back to overview](README.md) | [Next: Day 13 →]

## What are Directories?
- Directories on a server refer to folders or containers where files are stored and organized.
- Just like folders on your computer, server directories help store and categorize files in a logical way — and help the ethical hacker locate hidden or unlinked content (backup files, admin panels, config files) that isn't shown anywhere in the site's normal navigation.

## What is Directory Bruteforcing?
The technique of systematically requesting a huge list of common directory/file names against a target web server to discover paths that exist but aren't publicly linked — using a **wordlist** of likely names.

## Tools Covered

### 1. Gobuster
```bash
gobuster dir -u http://10.10.208.10:3333/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```
Fast, Go-based directory/file brute-forcer.

### 2. Feroxbuster
```bash
feroxbuster --url https://microsoft.com -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```
A more feature-rich brute-forcer with recursion, auto-filtering of false positives, and link extraction built in.

## 🖥️ Practice Log — Lab: TryHackMe "Vulnversity"
Room description: *"Learn about active recon, web app attacks and privilege escalation."* Target machine assigned: `10.10.208.10`.

**Gobuster scan running against the Vulnversity target:**
![gobuster dir scan running against 10.10.208.10:3333](images/day12-gobuster-running.png)

**Gobuster results — discovered directories:**
```
/.htaccess (Status: 403)
/.hta (Status: 403)
/.htpasswd (Status: 403)
/css (Status: 301)
/fonts (Status: 301)
/images (Status: 301)
/index.html (Status: 200)
/internal (Status: 301)
/js (Status: 301)
/server-status (Status: 403)
```
![gobuster results showing discovered directories](images/day12-gobuster-results.png)

📝 *My note:* `/internal` stands out immediately — a directory name like that, discovered only through brute-forcing (never linked from the homepage), is exactly the kind of lead a real pentester chases first.

**Browsing a discovered directory — `Index of /css`** (Apache's default directory listing, exposing every file since directory listing wasn't disabled):
![Index of /css directory listing](images/day12-index-css.png)

📝 *My note:* Because Apache's directory listing was left **enabled**, every discovered folder (`/css`, `/js`, `/images`) fully exposes its file listing with timestamps and sizes — itself a minor misconfiguration. Production servers should disable directory indexing (`Options -Indexes` in Apache) precisely to prevent this kind of casual browsing.

**Feroxbuster scan against microsoft.com:**
```
301  GET  0l  0w  0c  Auto-filtering found 404-like response and created new filter
[####################] - 74s  4728/4728  0s  found:0  errors:0
```
![feroxbuster scan against microsoft.com](images/day12-feroxbuster-microsoft.png)

📝 *My note:* Feroxbuster's **auto-filtering** feature detected that microsoft.com returns a custom "soft 404" page (a 200-status page that looks like an error) and automatically filtered it out — preventing thousands of false positives. Result: 0 real hits from the common wordlist, showing microsoft.com's public-facing surface is well-locked-down against this basic technique.

**Feroxbuster scan against paytm.com — much noisier results:**
```
403  GET  93l  349w  4513c  https://paytm.com/.htpasswd
403  GET  93l  349w  4513c  https://paytm.com/.git
403  GET  93l  349w  4513c  https://paytm.com/.ssh
200  GET  1l   1w    258c   https://paytm.com/.well-known/assetlinks.json
200  GET  1l   116w  3429c  https://paytm.com/cdn-cgi/styles/cf.errors.ie.css
429  GET  1l   3w    16c    https://paytm.com/.well-known/change-password
```
![feroxbuster scan against paytm.com showing many discovered paths](images/day12-feroxbuster-paytm.png)

📝 *My note:* Notice paytm.com returns **403 (Forbidden)** rather than 404 for sensitive paths like `.git`, `.ssh`, `.bash_history`, and `.htpasswd` — meaning those paths **exist** on the server but access is blocked. That's still valuable intel: it confirms the file/folder is there, and a misconfiguration or future vulnerability could expose it. The **429 (Too Many Requests)** responses also show paytm.com's rate-limiting kicking in against the scan.

## Why It Matters
Directory bruteforcing is often how real breaches start — an exposed `.git` folder can leak entire source code, a forgotten `/backup` folder can leak a database dump, and an `/internal` or `/admin` path can be a foothold for privilege escalation (the next stage of the Vulnversity room, and of the 5-phase methodology from Day 03).

## ➕ Extra Context
- **SecLists** (`/usr/share/wordlists/seclists/`) is the standard wordlist collection on Kali — `common.txt` is a good starting point; `raft-large-directories.txt` goes much deeper.
- Status code meanings during brute-forcing: **200** = found & accessible, **301/302** = redirect (often a real directory), **403** = exists but forbidden, **429** = rate-limited (back off), **404** = genuinely not found.
- Always compare a "soft 404" (custom error page returning HTTP 200) against a real one — tools like feroxbuster/ffuf auto-detect this via response-size filtering; gobuster does not by default, so manual verification matters.
- This day sets up directly into the next phase of Vulnversity: once `/internal` or similar is found, the next steps are typically web app attacks and privilege escalation.

## 🧠 Quick Revision Questions
1. What's the difference between gobuster and feroxbuster's handling of false-positive "soft 404" pages?
2. Why is a 403 response to `/.git` still useful information, even though access is blocked?
3. What Apache setting would prevent the `Index of /css` directory listing from being publicly browsable?
4. Why did the paytm.com scan show 429 responses partway through?

## 🔑 New Glossary Terms
- **Directory Bruteforcing** — systematically requesting wordlist-based paths to discover hidden files/folders
- **Soft 404** — a page that returns HTTP 200 but displays "not found" content, tricking naive scanners
- **Auto-filtering** — a scanner feature that detects and ignores repetitive false-positive responses
- **Rate Limiting (429)** — a server defense that throttles repeated requests from the same source

---
[← Day 11](day11.md) | [Back to overview](README.md) | [Next: Day 13 →]
