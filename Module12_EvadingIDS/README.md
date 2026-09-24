# Module 12 — Evading IDS, Firewalls & Honeypots

> *Know how detection works, and you'll understand both how attackers slip past it and how to close the gaps.*

**The goal.** Understand the defensive stack — firewalls, IDS/IPS, WAFs, honeypots — how each inspects traffic, the techniques used to evade them, and (the point for a defender) how to detect and harden against those techniques.

**Where it fits.** This ties the offensive phases to the blue-team reality: in a real environment, your scans and exploits meet controls. Understanding evasion makes you a better tester *and* a better defender.

## Concepts that matter

- **The defensive layers.** Packet-filter vs stateful firewall; signature vs anomaly IDS; inline IPS (blocks) vs passive IDS (alerts); WAF (HTTP layer); honeypot (a decoy that turns your recon into their alert).
- **Evasion families.** Fragmentation, timing/slow scans, source spoofing/decoys, payload encoding/obfuscation, protocol tunnelling.
- **Detection is probabilistic.** Signatures catch the known; anomaly detection catches the unusual. Both have blind spots — and both generate the logs a defender lives on.
- **Honeypots invert the game.** Any interaction with a decoy is, by definition, suspicious.

## Command cheat-sheet

```bash
# --- Firewall/IDS-aware scanning (Nmap) ---
nmap -f 10.10.10.5                         # fragment packets
nmap -D RND:10 10.10.10.5                  # decoys: hide among fake sources
nmap -sS -T1 10.10.10.5                    # very slow, low-signature timing
nmap --source-port 53 10.10.10.5           # masquerade as DNS
nmap -sA 10.10.10.5                        # ACK scan: map firewall rules (filtered vs not)
nmap --data-length 25 10.10.10.5           # pad packets to dodge signatures

# --- WAF detection & payload obfuscation ---
wafw00f https://10.10.10.5                 # identify the WAF
# obfuscate a payload (concept): case, comments, encoding
#   UNION SELECT  →  UnIoN/**/SeLeCt   |   URL/hex/unicode encoding

# --- Run detection yourself to SEE the alerts ---
sudo snort -A console -c /etc/snort/snort.conf -i eth0   # watch your own scans light it up
```

> The most instructive lab here is running the IDS yourself: launch a scan, watch which evasions do and don't trigger Snort/Suricata.

## Walk it in your lab

1. Stand up **Snort** or **Suricata** on a lab sensor between attacker and target.
2. Run a normal `nmap -sV` scan and watch it generate alerts.
3. Re-run with evasions (`-f`, `-D`, `-T1`, `--source-port 53`) and compare what still fires.
4. Put a simple WAF (ModSecurity + OWASP CRS) in front of a web app; send a plain SQLi payload (blocked), then an obfuscated one, and observe.
5. Deploy a small honeypot (e.g. **Cowrie**) and watch a scan against it become a high-fidelity alert.

## What good looks like

A comparison table: technique → did the IDS/WAF detect it? → why. The insight is that evasion is real but noisy, and layered detection closes most gaps.

## Detection & defence

| Evasion | Defence |
|---|---|
| Fragmentation | Reassembly at the IDS; drop tiny fragments |
| Decoys / spoofing | Egress filtering (BCP 38); correlate with netflow |
| Slow scans | Long-window anomaly detection; SIEM correlation |
| WAF payload obfuscation | Normalisation before matching; positive-security models |
| Recon | Honeypots/honeytokens as tripwires |

## Common junior mistakes

- Believing evasion makes you invisible — it changes signatures, it doesn't erase logs.
- Testing evasion with no detection running, so you learn nothing about what worked.
- Ignoring the ACK scan — it's the quiet way to map a firewall's rule set.

## Go deeper

- [Snort](https://www.snort.org) · [Suricata](https://suricata.io) · [OWASP ModSecurity CRS](https://coreruleset.org) · [Cowrie honeypot](https://github.com/cowrie/cowrie)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
