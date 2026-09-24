# Module 03 — Scanning

> *Find the live hosts, the open ports, and what's actually listening on them.*

**The goal.** Turn a list of addresses into a map of reachable hosts and services: which IPs are up, which TCP/UDP ports are open, and which service and version sits behind each one.

**Where it fits.** Recon told you *where* to look; scanning confirms what is actually alive and exposed, and feeds precise targets into enumeration. You cannot enumerate a service you haven't found.

## Concepts that matter

- **Host discovery** vs **port scanning** vs **service/version detection** — three separate questions, three sets of flags.
- **TCP handshake states.** `open`, `closed`, `filtered` (firewall dropping), `unfiltered` — the response tells you as much about the firewall as the host.
- **Scan types.** SYN (`-sS`, fast, half-open), Connect (`-sT`, no raw-socket privilege needed), UDP (`-sU`, slow but finds DNS/SNMP/TFTP), ping sweeps.
- **Timing & noise.** `-T4` is a sensible lab default; slower is stealthier. Aggressive scans are loud and get logged.

## Command cheat-sheet

```bash
# Host discovery (who's alive on the subnet)
nmap -sn 10.10.10.0/24                    # ping sweep, no port scan
fping -a -g 10.10.10.0/24 2>/dev/null     # fast alive-host list

# Core port scans
nmap -sS -T4 -p- 10.10.10.5               # all 65535 TCP ports (SYN)
nmap -sV -sC -p 22,80,443 10.10.10.5      # version detect + default NSE scripts
nmap -sU --top-ports 50 10.10.10.5        # top UDP ports (DNS/SNMP/TFTP...)

# Fast then deep (the practical workflow)
nmap -sS -T4 --min-rate 1000 -p- 10.10.10.5 -oG open.txt   # 1) find open ports fast
nmap -sV -sC -p22,80,139,445 10.10.10.5 -oA host5          # 2) deep-scan only those

# Handy NSE scripts
nmap --script vuln 10.10.10.5             # known-vuln checks
nmap --script smb-os-discovery -p445 10.10.10.5

# Save everything (always)
nmap -sV -oA scan_%date%  10.10.10.5      # -oN/-oX/-oG at once
```

> Learn to read the output, not just run it: an `open|filtered` UDP port and a `filtered` TCP port are telling you about the firewall in front of the host.

## Walk it in your lab

Target: **Metasploitable 2** (loaded with open services).

1. Discover live hosts on your host-only network: `nmap -sn 10.10.10.0/24`.
2. Full TCP port sweep of the target: `nmap -sS -T4 -p- <ip> -oG ports.txt`.
3. Deep-scan the ports you found: `nmap -sV -sC -p<list> <ip> -oA metasploitable`.
4. Add a UDP top-ports scan and note what appears (often SNMP/TFTP).
5. Build a service table: `port → service → version → notes`. That table drives Module 04.

## What good looks like

A per-host service inventory with **versions**, saved to disk (`-oA`), and a short note on the firewall behaviour you observed. Versions are gold — they map straight to Module 05 (vuln assessment).

## Detection & defence

| Attacker signal | Defender response |
|---|---|
| SYN sweep across many ports | IDS port-scan signatures; rate-limit / drop |
| Version-probe banners requested | Strip/spoof banners; patch to remove version leaks |
| UDP scans of SNMP/TFTP | Disable unused services; firewall egress/ingress |

## Common junior mistakes

- Scanning only the top 1000 ports and missing a service on a high port.
- Forgetting UDP entirely (DNS, SNMP, TFTP, IKE all hide there).
- Not saving output — then re-scanning a production host unnecessarily. Scan once, save with `-oA`, work from the file.

## Go deeper

- [Nmap reference guide](https://nmap.org/book/man.html) · [NSE scripts](https://nmap.org/nsedoc/)
- NIST SP 800-115 §4 (technical testing)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
