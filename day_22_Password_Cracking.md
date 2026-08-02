# Day 22 — Password Cracking

## 🎯 Goal
Understand hashing, the difference between hashing and encryption, and practice cracking password hashes using online tools and Kali Linux utilities (`hashcat`, `john`, `hash-identifier`).

---

## 📖 Concepts

### What is a Hash?
Hashing converts data of any size into a **fixed-length string** (the hash value) using a hash function.
- Even a tiny change in input produces a completely different hash (avalanche effect).
- Used for data integrity, indexing, and **password storage**.
- Hashes are **one-way / irreversible** — you can't get the original data back from the hash itself (only by cracking/matching it).

### Hashing vs Encryption

| Feature | Encryption | Hashing |
|---|---|---|
| Process | Two-way (reversible) | One-way (irreversible) |
| Purpose | Confidentiality | Integrity |
| Key required? | Yes (encrypt & decrypt) | No |
| Output | Variable length | Fixed length (e.g. 256-bit SHA-256) |
| Use cases | Securing data in transit (emails, files) | Verifying integrity (passwords) |
| Example algorithms | AES, RSA, Blowfish | MD5, SHA-1, SHA-256 |

### What is Password Cracking?
The process of trying to **guess or recover** a password to gain unauthorized access to a system/account. Common techniques:
- Brute force attack
- Dictionary attack
- Rainbow table attack
- Often automated with tools like `hashcat` / `john`

---

## 🛠️ Tools & Practice

### 1. Online Hash Crackers
- **CrackStation** (`crackstation.net`) — uses a 15-billion-entry lookup table (MD5/SHA1) built from wordlists + Wikipedia words.
  - Example: `ef749ff9a048bad0dd80807fc49e1c0d` → identified as `md5` → cracked to `Password1234`
- **Hashes.com** decrypt tool — same hash confirmed: `ef749ff9a048bad0dd80807fc49e1c0d : Password1234`
  - Tools available: Hash Identifier, Hash Verifier, Hash Generator, Base64 Encoder/Decoder, Email Extractor, 2John Hash Extractor

> Both only work for **unsalted** common hashes already present in their lookup tables.

### 2. `hashcat` (offline cracking)
```bash
hashcat -a 0 -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
```
- `-a 0` → straight/dictionary attack mode
- `-m 1000` → hash type = NTLM
- Example result: `2777b7fec870e04dda00cd7260f7bee6:P@$$W0rd`
- Output shows `Status: Cracked`, speed (kH/s), and progress against the wordlist.

### 3. `john` (John the Ripper)
```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```
- Example: cracked an **SSH private key passphrase** (`id_rsa`) → password found: `beeswax`
- `john` auto-detects hash type: `Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])`
- Use `john --show hash.txt` to reliably display all cracked passwords.

### 4. `hash-identifier`
```bash
hash-identifier
```
- Paste a hash (e.g. `48bb6e862e54f2a795ffc4e541caed4d`) → tool guesses the possible hash algorithm(s).

---

## 🧪 Lab: TryHackMe — "Crack The Hash"
Cracked hashes at increasing difficulty (Task 1 – Level 1, all correct ✅):

| Hash | Type | Cracked Password |
|---|---|---|
| `48bb6e862e54f2a795ffc4e541caed4d` | MD5 | `easy` |
| `CBFDAC6008F9CAB4083784CBD1874F76618D2A97` | SHA1 | `password123` |
| `1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032` | SHA256 | `letmein` |
| `$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom` | bcrypt | `bleh` |
| `279412f945939ba78ce0758d3fd83daa` | MD5 | `Eternity22` |

Room stats: 3174 likes, created by *ben*, free room, 103,424 users completed.

---

## ✅ Key Takeaways
- Never rely on plain hashing without a **salt** for password storage.
- `rockyou.txt` (`/usr/share/wordlists/rockyou.txt`) is the go-to default wordlist in Kali.
- `hashcat` = GPU-accelerated, great for speed; `john` = versatile, auto-detects many formats (including SSH keys, ZIP, PDF, etc. with `*2john` helper scripts).
- Always identify the hash type first (`hash-identifier`, hash length/format) before choosing an attack mode.
  
