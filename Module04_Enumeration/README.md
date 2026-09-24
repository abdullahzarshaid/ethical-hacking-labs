# Module 04 — Enumeration

> *Now that you know a service is there, make it tell you everything it knows.*

**The goal.** Actively query the services you found and pull out concrete facts: usernames, shares, groups, software versions, SNMP data, mail-server behaviour, directory entries. Enumeration turns "port 445 is open" into "here are the shares, users and OS."

**Where it fits.** It sits between scanning and exploitation. Everything you extract here — a username, a share, a version — is a lead for gaining access.

## Concepts that matter

- **Enumeration is active and noisy** — you are interacting with the service directly. Stay in scope.
- **Protocol by protocol.** SMB, SNMP, LDAP, NetBIOS, SMTP, DNS, NFS, RPC — each leaks different data and has its own tooling.
- **Null sessions & defaults.** Anonymous SMB access, `public` SNMP community strings, anonymous LDAP binds — misconfigurations that hand you data for free.
- **User enumeration** feeds password attacks in Module 06.

## Command cheat-sheet

```bash
# SMB / NetBIOS (ports 139/445)
enum4linux-ng -A 10.10.10.5              # all-in-one SMB enumeration
smbclient -L //10.10.10.5 -N            # list shares, null session
crackmapexec smb 10.10.10.5 --shares --users
nmap --script "smb-enum-*" -p445 10.10.10.5

# SNMP (UDP 161) — 'public' is everywhere
snmpwalk -v2c -c public 10.10.10.5      # walk the whole MIB
onesixtyone -c community.txt 10.10.10.5 # brute the community string

# LDAP (389/636)
ldapsearch -x -H ldap://10.10.10.5 -s base namingcontexts
ldapsearch -x -H ldap://10.10.10.5 -b "dc=example,dc=com"

# SMTP user enumeration (25)
smtp-user-enum -M VRFY -U users.txt -t 10.10.10.5

# DNS (zone transfer — often forgotten)
dig axfr example.com @ns1.example.com

# NFS (2049)
showmount -e 10.10.10.5                  # exported shares
```

## Walk it in your lab

Target: **Metasploitable 2** (SMB, SNMP, NFS all exposed).

1. Enumerate SMB with `enum4linux-ng -A <ip>` — capture users, shares, OS, workgroup.
2. Try a null-session share list: `smbclient -L //<ip> -N`, then browse a readable share.
3. Walk SNMP: `snmpwalk -v2c -c public <ip>` — note running processes and interfaces.
4. Check NFS exports with `showmount -e <ip>` and mount a world-readable export read-only.
5. Compile a **leads list**: every username, share and version becomes a row you'll use later.

## What good looks like

A structured facts sheet per host: users, groups, shares (with permissions), OS/version, SNMP findings, and any anonymous/misconfigured access — each tied to the protocol that revealed it.

## Detection & defence

| Attacker signal | Defender response |
|---|---|
| Null-session SMB / share listing | Disable null sessions; restrict share permissions |
| `public` SNMP walk | Change community strings, use SNMPv3, firewall UDP 161 |
| SMTP `VRFY`/`EXPN` probing | Disable VRFY/EXPN; rate-limit |
| DNS zone transfer | Restrict AXFR to authorised secondaries only |

## Common junior mistakes

- Stopping at "port open" without extracting the data behind it.
- Ignoring SNMP — it often hands over process lists, ARP tables and even credentials.
- Not correlating: the username from SMTP is the same one you'll spray in Module 06.

## Go deeper

- [enum4linux-ng](https://github.com/cddmp/enum4linux-ng) · [CrackMapExec/NetExec](https://github.com/Pennyw0rth/NetExec)
- OWASP WSTG — Identity & configuration testing

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
