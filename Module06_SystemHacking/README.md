# Module 06 — System Hacking

> *Gain access, prove it, escalate it, and understand how you'd be caught.*

**The goal.** Use a weakness you identified to gain a foothold on a host, then escalate from a low-privileged user to administrator/root, and understand persistence and log-clearing — so you can both demonstrate impact and defend against it.

**Where it fits.** This is where reconnaissance and enumeration pay off. It's also the phase with the most legal and ethical weight — only ever on a machine you own or are authorised, in writing, to test.

## The four sub-phases

1. **Gaining access** — password attacks, exploiting a vulnerable service, or abusing a misconfiguration.
2. **Privilege escalation** — from `user` to `root`/`SYSTEM` via kernel bugs, weak permissions, SUID binaries, or stored credentials.
3. **Maintaining access** — how persistence works (so you can hunt for it).
4. **Clearing tracks** — how logs get tampered with (so you can protect them).

## Command cheat-sheet

```bash
# --- Password attacks ---
hydra -L users.txt -P rockyou.txt ssh://10.10.10.5      # online brute (service)
hydra -l admin -P rockyou.txt 10.10.10.5 http-post-form \
  "/login:user=^USER^&pass=^PASS^:Invalid"
john --wordlist=rockyou.txt hashes.txt                  # offline crack
hashcat -m 1000 hashes.txt rockyou.txt                  # NTLM offline crack

# --- Getting a shell (Metasploit) ---
msfconsole -q
#  search <service/version>  →  use <exploit>  →  set RHOSTS/LHOST  →  run

# --- Linux privilege escalation (enumerate first) ---
./linpeas.sh                          # automated privesc surface
sudo -l                               # what can this user run as root?
find / -perm -4000 -type f 2>/dev/null   # SUID binaries
getcap -r / 2>/dev/null               # file capabilities
# check GTFOBins for any binary you can abuse

# --- Windows privilege escalation ---
whoami /priv                          # token privileges (SeImpersonate...)
.\winPEAS.exe                         # automated privesc surface
# check LOLBAS for living-off-the-land binaries
```

## Walk it in your lab

Target: **Metasploitable 2** (Linux) and/or a **Windows eval VM**.

1. **Access.** Pick a vulnerable service from Module 05 and get a shell (via Metasploit or a manual exploit), or brute a weak SSH/FTP login with `hydra`.
2. **Situational awareness.** `id` / `whoami`, OS version, running processes, network — know where you landed.
3. **Escalate.** Run `linpeas`/`winPEAS`, then follow one concrete path (a `sudo -l` entry, a SUID binary, a writable service). Confirm with `id` / `whoami /priv`.
4. **Understand persistence & logs** conceptually — where they live, how they're detected — without needing to deploy anything.
5. **Capture evidence** at every step (commands + screenshots). Evidence is the point.

## What good looks like

A clear chain: *initial access → proof of low-priv shell → the specific escalation path → proof of root/SYSTEM*, each step backed by a command and a screenshot. Impact you can't evidence isn't impact.

## Detection & defence

| Attacker signal | Defender response |
|---|---|
| Brute-force against SSH/RDP/web | Lockouts, MFA, fail2ban, strong passwords |
| Known-exploit payloads | Patch; EDR; least privilege |
| SUID/sudo/token abuse | Audit SUID; tighten `sudoers`; remove dangerous privileges |
| Log clearing | Ship logs off-host (SIEM); make them tamper-evident |

## Common junior mistakes

- Firing exploits before enumerating — you skip the easy win and make noise.
- Getting a shell and stopping, without escalating or evidencing impact.
- Not stabilising the shell (upgrade to a PTY) and losing it on the first mistake.

---

## Worked example — student lab (CY201)

This module includes a full class lab write-up by its student authors, kept as submitted:

- **[CEH_Module6_LabReport_Group3.docx](CEH_Module6_LabReport_Group3.docx)** — the group's report
- **[Commands Used.txt](Commands%20Used.txt)** · **[Tools Used.txt](Tools%20Used.txt)**
- Step screenshots under `Lab1/`, `Lab 3/` and `Lab4/` (by task)

*This work was produced by its student group and remains theirs, credited to them. It's here as a worked example of the phase above.*

## Go deeper

- [GTFOBins](https://gtfobins.github.io) · [LOLBAS](https://lolbas-project.github.io) · [HackTricks — privesc](https://book.hacktricks.xyz)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
