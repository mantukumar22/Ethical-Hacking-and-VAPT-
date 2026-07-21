# Day 06 — WHOIS & DNS Recon
[← Day 05](day05.md) | [Back to overview](README.md) | [Next: Day 07 →]

## What are WHOIS records?
- WHOIS records provide detailed information about the **registered owner** of a domain name — contact details, registration dates, and the domain's status.
- These records are accessible through **WHOIS databases** maintained by domain registrars and registry organizations.
- Classic **passive recon** technique — no direct interaction with the target's live infrastructure.

## What is DNS?
- **DNS (Domain Name System)** translates and maps alphabetic domain names (website addresses) to the numeric **IP addresses** of computers/servers — and can also perform the reverse (IP → domain).
- Uses **UDP (User Datagram Protocol)** and operates on **port 53**.
- **Top-Level Domains (TLDs)**: `.com`, `.gov`, `.edu`, `.org`, etc.

## DNS Record Types

| Record Type | Description |
|---|---|
| **A** | Maps a domain to an IPv4 address |
| **AAAA** | Maps a domain to an IPv6 address |
| **CNAME** | Aliases one domain name to another |
| **NS** | Specifies the authoritative nameserver for a domain |
| **SOA** | Contains admin info about the domain (primary nameserver, zone update settings) |
| **TXT** | Stores text info, often used for domain verification (e.g. SPF/DKIM records) |
| **MX** | Directs email to the correct mail servers |

## 🖥️ Practice Log (hands-on, followed along in Kali terminal + browser)

**1. WHOIS lookup via web tool (who.is)**
- Visited `https://who.is` → searched `microsoft.com`
- Returned **Registrar Info**: Name = MarkMonitor, Inc.; WHOIS Server = whois.markmonitor.com; Status flags = `clientDeleteProhibited`, `clientTransferProhibited`, `clientUpdateProhibited`, `serverDeleteProhibited`, `serverTransferProhibited` (these lock flags protect high-value domains from accidental/malicious transfer or deletion)
- Also showed **Registrant/Admin/Technical Contact Information**: Organization = Microsoft Corporation, Address = One Microsoft Way, Redmond, WA 98052, US — a good example of how much a registrar *chooses* to display (many now redact personal WHOIS data for GDPR compliance, but corporate entries like Microsoft's stay public).

**2. WHOIS lookup via terminal (Kali)**
```bash
whois microsoft.com
```
Key fields returned:
```
Domain Name: MICROSOFT.COM
Registry Domain ID: 2724960_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.markmonitor.com
Creation Date: 1991-05-02T04:00:00Z
Registry Expiry Date: 2025-05-03T04:00:00Z
Registrar: MarkMonitor Inc.
Registrar Abuse Contact Email: abusecomplaints@markmonitor.com
Registrar Abuse Contact Phone: +1.2086851750
Name Server: NS1-39.AZURE-DNS.COM
Name Server: NS2-39.AZURE-DNS.NET
Name Server: NS3-39.AZURE-DNS.ORG
Name Server: NS4-39.AZURE-DNS.INFO
DNSSEC: unsigned
```
📝 *My note:* The command-line `whois` gives the same data as who.is but faster and scriptable — useful once you're enumerating many domains (e.g. in a bug bounty recon pipeline).

**3. Basic DNS lookup with `host`**
```bash
host microsoft.com
```
```
microsoft.com has address 20.76.201.171
microsoft.com has address 20.70.246.20
microsoft.com has address 20.236.44.162
microsoft.com has address 20.112.250.133
microsoft.com has address 20.231.239.246
microsoft.com has IPv6 address 2603:1030:c02:8::14
microsoft.com has IPv6 address 2603:1010:3:3::5b
microsoft.com has IPv6 address 2603:1020:201:10::10f
microsoft.com has IPv6 address 2603:1030:b:3::152
microsoft.com has IPv6 address 2603:1030:20e:3::23c
microsoft.com mail is handled by 10 microsoft-com.mail.protection.outlook.com.
```
→ One command reveals: multiple **A records** (IPv4 load balancing), **AAAA records** (IPv6), and the **MX record** (mail handled by Outlook protection servers) all at once.

**4. Querying specific record types with `host -t`**
```bash
host -t ns microsoft.com
```
```
microsoft.com name server ns4-39.azure-dns.info.
microsoft.com name server ns1-39.azure-dns.com.
microsoft.com name server ns2-39.azure-dns.net.
microsoft.com name server ns3-39.azure-dns.org.
```
```bash
host -t mx microsoft.com
```
```
microsoft.com mail is handled by 10 microsoft-com.mail.protection.outlook.com.
```
```bash
host -t mx spotify.com
```
```
spotify.com mail is handled by 1 aspmx.l.google.com.
spotify.com mail is handled by 10 aspmx3.googlemail.com.
spotify.com mail is handled by 5 alt1.aspmx.l.google.com.
spotify.com mail is handled by 10 aspmx5.googlemail.com.
spotify.com mail is handled by 10 aspmx4.googlemail.com.
spotify.com mail is handled by 10 aspmx2.googlemail.com.
spotify.com mail is handled by 5 alt2.aspmx.l.google.com.
```
📝 *My note:* Comparing MX records instantly tells you what email provider a company uses — Microsoft hosts its own mail (Outlook/Exchange), while Spotify runs on **Google Workspace** (aspmx.l.google.com). This kind of fingerprinting is useful later for phishing-simulation scoping and OSINT profiling.

**5. Interactive queries with `nslookup`**
```bash
nslookup
> set type=ns
> microsoft.com
```
```
Non-authoritative answer:
microsoft.com   nameserver = ns4-39.azure-dns.info.
microsoft.com   nameserver = ns1-39.azure-dns.com.
microsoft.com   nameserver = ns2-39.azure-dns.net.
microsoft.com   nameserver = ns3-39.azure-dns.org.
```
```
> set type=mx
> microsoft.com
```
```
Non-authoritative answer:
microsoft.com   mail exchanger = 10 microsoft-com.mail.protection.outlook.com.
```
📝 *My note:* `nslookup`'s **interactive mode** (`set type=...`) is handy when querying many record types against the same domain in one session, instead of retyping `host -t <type> <domain>` each time.

## ➕ Extra context (added beyond the slides)
- **`dig`** is the more modern/powerful alternative to `nslookup` on Linux — e.g. `dig microsoft.com ANY +noall +answer` (though many DNS servers now ignore `ANY` queries for abuse-prevention reasons; query record types individually: `dig microsoft.com MX`).
- **`dig axfr @nameserver domain.com`** — attempts a **DNS zone transfer**; a misconfigured DNS server that allows this to an unauthorized host leaks the entire internal DNS zone. This crosses from passive into **active recon** since it's direct interaction with the nameserver — only attempt on authorized targets.
- WHOIS **privacy/proxy services** (common for personal domains) redact the registrant's real contact info — but corporate domains like microsoft.com often leave it public since there's no individual to protect.
- The `clientTransferProhibited` / `clientDeleteProhibited` flags seen in the WHOIS output are **domain locks** — standard practice for high-value corporate domains to prevent hijacking.
- Tools that automate WHOIS + DNS recon at scale: **Amass**, **Sublist3r**, **dnsrecon** (all pre-installed or easily installed on Kali) — worth trying once comfortable with the manual commands above.

## 🧠 Quick Revision Questions
1. What port and protocol does DNS use?
2. What's the difference between an A record and a CNAME record?
3. How did comparing Microsoft's and Spotify's MX records reveal their email providers?
4. Why is a DNS zone transfer (`axfr`) considered active recon rather than passive?
5. What do the `clientTransferProhibited` / `clientDeleteProhibited` WHOIS flags protect against?

## 🔑 New Glossary Terms
- **WHOIS** — protocol/database for looking up domain registration ownership info
- **DNS (Domain Name System)** — translates domain names ↔ IP addresses
- **A / AAAA / CNAME / NS / SOA / TXT / MX** — core DNS record types (see table above)
- **Zone Transfer (AXFR)** — a DNS mechanism for replicating zone data between servers; a major misconfiguration risk if open to the public
- **TLD** — Top-Level Domain (e.g. `.com`, `.org`, `.edu`)

---
[← Day 05](day05.md) | [Back to overview](README.md) | [Next: Day 07 →]
                                                                                                                                                                                                                          
