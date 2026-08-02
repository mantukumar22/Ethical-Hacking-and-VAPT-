# Day 27 — Hacking Instagram (Phishing Deep Dive)

## 🎯 Goal
Understand phishing in depth, learn its many sub-types, and practice building a credential-harvesting phishing page for Instagram using `zphisher`.

---

## 📖 What is Phishing?
A cyberattack where attackers **impersonate legitimate organizations or individuals** to deceive people into revealing sensitive info (passwords, credit card numbers, personal data) — primarily via fraudulent emails, texts, or phone calls that look authentic.

---

## 📊 Types of Phishing

| Type | Description | Example |
|---|---|---|
| **Deceptive Phishing** | Most common — emails appear to be from legit sources to steal credentials | Email impersonating a bank asking to "update login credentials" |
| **Spear Phishing** | Targeted at specific individuals/orgs using personal info for credibility | Email tailored to an employee, appearing to come from the CEO |
| **Whaling** | Spear phishing targeting high-profile execs with very convincing messages | Email posing as a government agency requesting financial info from a CFO |
| **Vishing** | Voice phishing over phone calls | Call pretending to be from a bank asking for account verification |
| **Smishing** | Phishing via SMS text messages | Text from "bank" asking to click a link for account updates |
| **Clone Phishing** | Exact replica of a legit email with malicious link/attachment swapped in | Cloned "software update" email with a malware link |
| **Typosquatting** | Using domain names similar to legit sites to catch typo-mistakes | `paypa1.com` instead of `paypal.com` |
| **Pharming** | Redirecting users to fake sites even with correct URL (DNS poisoning) | Typing `bank.com` but landing on a fraudulent look-alike site |
| **CEO Fraud (BEC)** | Business Email Compromise — impersonating execs to request transfers | Email "from CEO" instructing urgent wire transfer |

---

## 🛠️ Practical: `zphisher`

### Install
```bash
git clone https://github.com/htr-tech/zphisher.git
cd zphisher
bash zphisher.sh
```
> A beginner-friendly, automated phishing tool with **30+ templates**. Written in Bash, ~12K GitHub stars, GPL-3.0 licensed. Author: `htr-tech` (tahmid.rayat).

⚠️ **Disclaimer (from repo)**: Any use of Zphisher is solely your responsibility; misuse can lead to criminal charges. Made strictly **for educational purposes**.

### Menu — select platform to clone
```
[::] Select An Attack For Your Victim [::]
[01] Facebook   [11] Twitch     [21] DeviantArt
[02] Instagram  [12] Pinterest  [22] Badoo
[03] Google     [13] Snapchat   [23] Origin
[04] Microsoft  [14] Linkedin   [24] DropBox
[05] Netflix    [15] Ebay       [25] Yahoo
[06] Paypal     [16] Quora      [26] Wordpress
[07] Steam      [17] Protonmail [27] Yandex
[08] Twitter    [18] Spotify    [28] StackOverFlow
[09] Playstation[19] Reddit     [29] Vk
[10] Tiktok     [20] Adobe      [30] XBOX
[31] Mediafire  [32] Gitlab     [33] Github
[34] Discord    [35] Roblox
```

### Tunneling
Zphisher auto-installs **Cloudflared** to tunnel the local phishing server to a public URL (e.g. `https://found-portable-durable-minds.trycloudflare.com`).

### Example — Instagram "Verified Badge" phishing page
Victim lands on a fake "Mail Confirmation" page mimicking Instagram's verification request flow:
> "In order to have a verification badge, you need to confirm your email. After mail confirmation, your application will be evaluated within 24 hours..."

Clicking **Confirm E-Mail** leads to a credential-harvesting login form.

---

## ✅ Key Takeaways
- Phishing sub-types differ mainly by **channel** (email/SMS/voice) and **targeting precision** (mass vs spear vs whaling).
- `zphisher` automates the whole flow: template hosting + tunneling (via ngrok/Cloudflared) + credential logging.
- Real-world defense: enable **2FA**, verify sender domains carefully, never trust urgency-based prompts blindly.
- This tool is strictly for **authorized security awareness testing** — using it against real users without consent is illegal.
