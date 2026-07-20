# Day 02 — Installing Kali Linux
[← Back to overview](README.md)

## What is Kali Linux?
- Debian-based Linux distro built for penetration testing, security research, computer forensics, and reverse engineering.
- Maintained by **Offensive Security**.
- Download from **kali.org** → `Get Kali` or `Download`.

## Setup used in the course
- **Oracle VirtualBox** used to run Kali as a VM alongside the host OS — keeps testing tools in a safe, isolated sandbox instead of running directly on your main OS.

## 🛠 My setup checklist
- [ ] Install VirtualBox (or VMware) on host machine
- [ ] Download Kali Linux VM image (pre-built `.ova`) or ISO
- [ ] Allocate ≥ 4GB RAM, ≥ 40GB disk to the VM
- [ ] Enable **NAT/Host-only networking** for isolated lab practice
- [ ] Take a **VM snapshot** right after first boot — makes it easy to roll back if something breaks during exploitation practice
- [ ] Update system: `sudo apt update && sudo apt full-upgrade -y`

## ➕ Extra context (added beyond the slides)
- Alternatives to Kali: **Parrot OS** (lighter, more privacy-focused), **BlackArch** (Arch-based, huge tool repo).
- Kali's default user is no longer root by default (since 2020.1) — use `sudo`.
- Key pre-installed tool categories to explore later: Information Gathering, Vulnerability Analysis, Web Application Analysis, Password Attacks, Wireless Attacks, Exploitation Tools, Sniffing & Spoofing, Post Exploitation, Forensics, Reporting.

## 🧠 Quick Revision Questions
1. Why run Kali in a VM instead of on bare metal alongside your main OS?
2. Why take a snapshot right after first boot?
3. Name one Kali alternative and what makes it different.

## 🔑 New Glossary Terms
- **VM Snapshot** — a saved state of a virtual machine you can roll back to

---
[← Day 01](day01.md) | [Back to overview](README.md) | [Next: Day 03 →](day03.md)
