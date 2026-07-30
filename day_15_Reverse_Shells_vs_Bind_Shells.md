# Day 15 — Reverse Shells vs Bind Shells, Staged vs Stageless Payloads
[← Back to overview](README.md)

## Two Questions Every Payload Must Answer
Before any exploit fires, two design choices decide how the resulting shell behaves and how it gets delivered:
1. **Who connects to whom?** → Reverse shell vs. Bind shell.
2. **How much of the payload travels up front?** → Staged vs. Stageless payload.

## Reverse Shell vs. Bind Shell
| Feature | Bind Shell | Reverse Shell |
|---|---|---|
| **Connection Initiation** | Attacker connects to a listening port on the target. | Target machine initiates a connection back to the attacker. |
| **Listener Location** | Listener runs on the **target** machine. | Listener runs on the **attacker's** machine. |
| **IP Address Requirement** | Attacker must know the victim's IP address. | Attacker does not need to know the victim's IP address. |
| **Firewall Bypass** | More likely to be blocked by firewalls. | Can bypass firewalls as the target connects outward. |
| **Common Use Cases** | Often used for persistence and direct control. | Commonly used for initial access and bypassing firewalls. |
| **Security Implications** | Exposes a port on the target, making it vulnerable. | Less exposure since the connection is initiated by the target. |
| **Typical Protocols Used** | Often uses TCP/UDP directly on specified ports. | Frequently utilizes HTTP/S or other common protocols to avoid detection. |

**Why reverse shells dominate real-world engagements:** most corporate networks heavily restrict *inbound* traffic but
allow far more *outbound* traffic (users need to browse the web). A bind shell asks the firewall "let me in" — a
reverse shell asks it "let me out," which is a much easier ask and one the target's own network often doesn't stop.

## Staged vs. Stageless Payloads
| Feature | Staged Payload | Non-Staged (Stageless) Payload |
|---|---|---|
| **Definition** | Composed of multiple parts, including a stager and the final payload. | A self-contained package that includes everything needed to execute. |
| **Execution Process** | Requires an initial stager to connect and download the final payload. | Executes in a single step without needing a stager. |
| **Network Dependency** | Needs network connectivity for the stager to retrieve the final payload. | Can operate independently once executed, as it does not require additional downloads. |
| **Complexity** | More complex due to multiple components and stages. | Simpler as it is a single executable with all functionalities embedded. |
| **File Size** | Generally smaller due to the separation of stages. | Typically larger since it contains all necessary code in one executable. |
| **Stealth and Detection** | More detectable due to the presence of multiple network interactions and a stager. | Generally stealthier as it minimizes network interactions and executes directly. |

Visual model:
- **Staged:** `Victim → Dropper → beacons out → C2 Server` → C2 sends Stage 2 → Stage 2 downloaded & injected into
  memory → **reverse shell established** back to the attacker.
- **Stageless:** `Victim` runs a single `stageless.exe` → **reverse shell over TCP/1337** direct to the attacker —
  no dropper, no separate stage-2 fetch.

## Practice — Listing Payloads with msfvenom
```bash
msfvenom -l payloads | grep "windows/meterpreter/reverse_tcp"
```
Sample of what a filtered payload list looks like — note the `(staged)` vs `(stageless)` labels baked into the
descriptions:
```
linux/x86/meterpreter/reverse_tcp                (staged)  Run the Meterpreter / Metasploit server, Connect back to attacker and spawn a Met
linux/x64/meterpreter/reverse_tcp                (staged)  Run the Meterpreter / Metasploit server, Connect back to attacker and spawn a Met
linux/zarch/meterpreter/reverse_tcp              (staged)  Run the Meterpreter / Metasploit server, Connect back to attacker and spawn a Met
osx/x86/meterpreter/reverse_tcp                  (staged)  Run the Meterpreter / Metasploit server, Connect back to attacker and spawn a Met
osx/x64/meterpreter/reverse_tcp                  (staged)  Run the Meterpreter / Metasploit server, Connect back to attacker and spawn a Met
php/meterpreter/reverse_tcp                                Run a PHP Meterpreter server, Connect back to attacker and spawn a Meterpreter shell
windows/meterpreter/reverse_tcp                             Run the Meterpreter / Metasploit server, Connect back to attacker and spawn a Met
windows/x64/meterpreter/reverse_tcp                         Meterpreter shell, reverse TCP stager. Requires Windows XP SP2 or newer.
```
Every payload here shares the same underlying idea (a reverse Meterpreter shell), but the platform/architecture
(`windows`, `linux`, `osx`, `php`), bitness (`x86`/`x64`), and stage type differ — this list is what gets picked from
when building the real payload used in Day 17's exploitation walkthrough.

## Why It Matters
Choosing reverse over bind (almost always reverse, for firewall-bypass reasons above) and stageless vs. staged
(stageless = simpler/more portable single file; staged = smaller initial footprint, useful when the delivery
mechanism itself is size-constrained) is a decision made **before** `msfvenom` is ever run. Getting it wrong means a
shell that never calls back, or a payload that silently fails because the network path it depends on doesn't exist.

## 🧠 Quick Revision Questions
1. Why is a reverse shell generally more reliable than a bind shell when the target sits behind a corporate firewall?
2. What extra step does a staged payload require that a stageless payload does not?
3. Give one scenario where a smaller staged payload would be preferable to a larger stageless one.

## 🔑 New Glossary Terms
- **Listener** — a process waiting for an incoming network connection.
- **Stager** — the small first-stage payload whose only job is to fetch and load the real, larger payload.
- **C2 (Command & Control) Server** — the attacker's server that a compromised host beacons back to.
- **msfvenom** — Metasploit's standalone payload generation and encoding tool.

---
[← Day 14](day14.md) | [Back to overview](README.md) | [Next: Day 16 →](day16.md)
