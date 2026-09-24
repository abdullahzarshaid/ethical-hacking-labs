# Module 10 — Denial of Service

> *Understand how availability is attacked at the network and application layer — so you can absorb it.*

**The goal.** Learn how DoS and DDoS work (volumetric, protocol, and application-layer), how amplification multiplies an attacker's power, and how defenders detect and absorb these attacks. The emphasis is understanding and defence.

> ⚠️ DoS testing degrades or destroys availability. Run it **only** against your own isolated lab targets. Never against shared, production, or third-party systems — and only within a scope that explicitly permits it.

## Concepts that matter

- **Three layers of attack.**
  - *Volumetric* — flood the pipe (UDP/ICMP floods, amplification). Measured in bps.
  - *Protocol* — exhaust state (SYN flood, Smurf). Measured in pps.
  - *Application* — exhaust the app cheaply (Slowloris, HTTP floods, expensive queries). Low bandwidth, high impact.
- **Amplification/reflection.** Spoof the victim's IP to a service (DNS, NTP, memcached) that replies far larger than the request — huge force from a small attacker.
- **DoS vs DDoS.** One source vs a distributed botnet — the defence differs (rate-limit vs upstream scrubbing).

## Command cheat-sheet

```bash
# --- Lab-only, against your own target VM ---
# SYN flood (protocol-layer)
sudo hping3 -S --flood -p 80 10.10.10.5          # half-open connection exhaustion
sudo hping3 -S -p 80 --rand-source 10.10.10.5    # spoofed sources

# Application-layer (low-bandwidth, high-impact)
slowhttptest -c 1000 -H -i 10 -r 200 -u http://10.10.10.5   # Slowloris-style
# GoldenEye / hulk — HTTP request floods (study the mechanics)

# --- Observe the impact & defence side ---
watch -n1 'ss -s'                                 # socket/connection states on target
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'   # watch the SYN storm
```

## Walk it in your lab

Target: a lab web-server VM you can freely break.

1. Baseline the service: response time and connection count under normal load.
2. Launch a **Slowloris-style** app-layer attack with `slowhttptest`; watch the server stop responding while using almost no bandwidth — the key lesson.
3. On the target, observe connection-state exhaustion (`ss -s`).
4. Apply a defence (connection limits, a reverse proxy, `mod_reqtimeout`) and repeat — see it survive.
5. Restore the VM from snapshot.

## What good looks like

A before/after: baseline performance, the attack's effect, the specific control that mitigated it, and a note on which attack *layer* it addressed. Understanding that a 4 KB/s attack can take down a server is the takeaway.

## Detection & defence

| Attack type | Defence |
|---|---|
| Volumetric flood | Upstream scrubbing / CDN; ISP blackhole; anycast |
| SYN flood | SYN cookies; connection rate-limits; firewall state limits |
| Slow/app-layer | Reverse proxy timeouts; request-rate limits; WAF |
| Amplification | Disable open resolvers/NTP monlist; BCP 38 anti-spoofing |

## Common junior mistakes

- Pointing a flood tool at anything outside an isolated lab. This is where careless testing becomes a crime.
- Thinking DoS is only about bandwidth — the cheap, dangerous attacks are application-layer.
- Not measuring a baseline, so "it's down" can't be proven or compared.

## Go deeper

- [CISA DDoS guidance](https://www.cisa.gov) · [Cloudflare Learning — DDoS](https://www.cloudflare.com/learning/ddos/what-is-a-ddos-attack/) · BCP 38 (RFC 2827)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
