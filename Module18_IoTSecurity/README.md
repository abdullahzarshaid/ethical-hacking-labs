# Module 18 — IoT & OT Security

> *Cheap connected devices and decades-old industrial gear share one trait: security was an afterthought.*

**The goal.** Understand the IoT/OT attack surface — default credentials, exposed management, insecure firmware, weak or absent protocol security — and how assessing these devices differs from IT, especially the safety constraints in Operational Technology.

**Where it fits.** IoT devices are small networked computers (revisit Modules 03–06). OT (ICS/SCADA) adds physical processes where a careless test can cause real-world harm — so methodology and caution change.

## Concepts that matter

- **IoT weaknesses.** Default/hardcoded credentials, exposed Telnet/UART/SSH, unsigned/unencrypted firmware, plaintext protocols (MQTT, CoAP), no update mechanism.
- **OT/ICS reality.** Protocols like **Modbus** and **DNP3** were designed with *no authentication*. Devices are fragile and can't be casually scanned — availability and safety outrank confidentiality.
- **Firmware is a filesystem.** Extract it and you often find hardcoded keys, credentials and backdoors.
- **The golden OT rule.** Passive first. Never actively scan production OT without explicit sign-off and a maintenance window — a stray packet can stop a process.

## Command / tool cheat-sheet

```bash
# --- Discover devices & services ---
nmap -sV --script banner 10.10.10.0/24        # IoT often shows Telnet/UART/HTTP
# Shodan (browser): search device banners / exposed ICS

# --- Firmware analysis (the high-value skill) ---
binwalk -e firmware.bin                        # identify & extract embedded filesystems
firmwalker ./extracted                         # hunt for creds/keys/URLs
grep -riE "password|admin|api_key|private" ./extracted/

# --- Protocol interaction (LAB / authorised only) ---
# MQTT
mosquitto_sub -h 10.10.10.5 -t '#' -v          # subscribe to ALL topics (often open)
# Modbus (ICS) — READ-ONLY, isolated lab simconly
#   pymodbus / modbus-cli to read registers on a simulator (never live plant gear)
```

> ⚠️ **OT/ICS:** practise only against **simulators** (e.g. Conpot honeypot, a PLC sim) or gear on an isolated bench — never production plant equipment.

## Walk it in your lab

1. Analyse a router/IoT **firmware image**: `binwalk -e`, then grep the extracted filesystem for hardcoded credentials, keys and backdoor URLs.
2. Stand up an open **MQTT** broker in your lab; subscribe to `#` and watch how a misconfigured broker leaks every message.
3. Run **Conpot** (an ICS honeypot) and interact with a simulated Modbus device read-only to learn the protocol's lack of auth.
4. Note the pattern: default creds + exposed management + no crypto = the IoT story, over and over.

## What good looks like

For an IoT device: a findings list (default creds, exposed services, firmware secrets) with evidence. For OT: an assessment that respects safety — passive observation and configuration review, with any active testing gated behind explicit authorisation.

## Detection & defence

| Weakness | Defence |
|---|---|
| Default/hardcoded creds | Force credential change; unique per-device secrets |
| Exposed management (Telnet/UART) | Disable; segment; VPN-only management |
| Insecure firmware | Signed, encrypted firmware + secure update |
| Unauthenticated OT protocols | Network segmentation (Purdue model); OT-aware IDS; never expose to the internet |

## Common junior mistakes

- Actively scanning live OT equipment — the fastest way to cause a real outage.
- Stopping at the web UI and never extracting the firmware, where the secrets are.
- Treating IoT as exotic — it's mostly IT fundamentals on weaker hardware.

## Go deeper

- [OWASP IoT Top 10](https://owasp.org/www-project-internet-of-things/) · [binwalk](https://github.com/ReFirmLabs/binwalk) · [Conpot](https://github.com/mushorg/conpot) · MITRE ATT&CK for ICS

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
