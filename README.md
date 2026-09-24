# Ethical Hacking — Hands-On Lab Knowledge Base

A practical, module-by-module walkthrough of offensive security, built from a university ethical-hacking
course and organised the way you'd actually learn it: **understand the goal → pick the right tools → do the
lab → capture the evidence.** It follows the well-known Certified Ethical Hacker (CEH) domain structure and
maps each domain to a real lab folder with reports and screenshots.

> **Who this is for:** students starting out in penetration testing, people preparing for CEH, and anyone
> who wants a clear map of *what* each attack phase is, *why* it matters, and *how* to practise it safely in
> a home lab. It's written to coach you through the work, not to lecture at you.

> ⚠️ **Ethics and scope.** Everything here is for a **controlled lab you own or are authorised to test**.
> Never run these techniques against systems you don't have explicit written permission to test. Learning
> to attack is how you learn to defend — keep it legal.

---

## How to use this repo

1. **Start at Module 2 and go in order** — each phase builds on the last (you can't enumerate what you
   haven't scanned).
2. For each module, read the short **overview** (what/why), skim the **tools**, then open the group's
   **lab report** and **screenshots** to see it done end to end.
3. **Build a safe lab first:** a hypervisor (VMware/VirtualBox), an attacker VM (Kali or Parrot), and a
   couple of deliberately vulnerable targets (e.g. Metasploitable, a Windows eval VM, DVWA/OWASP Juice
   Shop) on an **isolated NAT/host-only network**. Nothing here should touch the internet or a real network.

## The attack lifecycle, module by module

| # | Module | What you'll learn (in one line) |
|---|---|---|
| 02 | **Reconnaissance** | Gather information on a target passively and actively (OSINT, WHOIS, DNS, Google dorking) before touching it. |
| 03 | **Scanning** | Discover live hosts, open ports and services (Nmap host discovery, port and service/version scans). |
| 04 | **Enumeration** | Pull detailed info from services — users, shares, banners (SMB, SNMP, LDAP, NetBIOS). |
| 05 | **Vulnerability Assessment** | Identify and rank weaknesses with scanners and manual checks; separate exposure from confirmed risk. |
| 06 | **System Hacking** | Gain access, escalate privilege, and maintain/cover access on a host. |
| 07 | **Malware Threats** | Understand trojans, worms, and RATs, and how to analyse and detect them safely. |
| 08 | **Sniffing** | Capture and read network traffic; understand ARP spoofing and switch-level attacks. |
| 09 | **Social Engineering** | The human attack surface — phishing, pretexting, and how to defend against it. |
| 10 | **Denial of Service** | How DoS/DDoS works at the network and application layer, and how to detect it. |
| 11 | **Session Hijacking** | Steal or fixate sessions at the network and application level; why tokens must expire. |
| 12 | **Evading IDS, Firewalls & Honeypots** | How detection works and how attackers try to slip past it. |
| 13 | **Hacking Web Servers** | Attack the server layer — misconfig, default creds, known-CVE exploitation. |
| 14 | **Hacking Web Applications** | The app layer — brute force, XSS/CSRF, parameter tampering, known plugin/library flaws. |
| 15 | **SQL Injection** | Read and manipulate a database through unsafe queries; then fix it. |
| 16 | **Wireless Attacks** | Attack and assess Wi-Fi — encryption, capture, and rogue access points. |
| 17 | **Mobile Security** | Android/iOS attack surface and app security basics. |
| 18 | **IoT & OT Security** | The weak spots of connected devices and industrial systems. |
| 19 | **Cloud Security** | Cloud misconfigurations and the shared-responsibility model. |
| 20 | **Cryptography** | The crypto you actually need — hashing, encryption, and how it's attacked. |

Each folder above contains a **lab report** and the **screenshots/commands** that back it up.

## A realistic learning path

- **Week 1–2:** Modules 2–5 (recon → scanning → enumeration → vuln assessment) — the "find the way in" phase.
- **Week 3–4:** Modules 6–12 (system hacking through evasion) — the "get in and stay in" phase.
- **Week 5–6:** Modules 13–15 (web servers, web apps, SQLi) — the highest-demand skills in the field.
- **Week 7+:** Modules 16–20 (wireless, mobile, IoT, cloud, crypto) — broaden your surface.

## Credits and attribution

The lab reports and screenshots in each module were produced by **students of the CY201 course**, and each
report **credits its own group members** — that work belongs to them. The learning structure, overviews and
this guide are original instructional material by **Abdullah Bin Zarshaid** (course instructor), shared to
help others learn.

- **Instructional content and guides:** © Abdullah Bin Zarshaid — free to reuse for learning with attribution.
- **Student lab reports:** remain the work of their named authors, credited in each module.
- **Trademarks:** "CEH" and "Certified Ethical Hacker" are trademarks of EC-Council. This repository is an
  independent educational resource organised around publicly known domain names; it does **not** reproduce
  EC-Council courseware and is not affiliated with or endorsed by EC-Council.

If this helped you, a ⭐ helps other learners find it.
