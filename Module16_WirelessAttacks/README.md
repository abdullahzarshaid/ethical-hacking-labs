# Module 16 — Wireless Attacks

> *Radio has no cable to unplug — anyone in range is on your attack surface.*

**The goal.** Understand Wi-Fi security (WEP/WPA/WPA2/WPA3), how handshakes are captured and cracked, how rogue APs and evil-twin attacks work, and how to secure a wireless network.

> ⚠️ Only test **your own** access point. Capturing or attacking any other network is illegal in most jurisdictions. You need a Wi-Fi adapter that supports **monitor mode** and packet injection.

## Concepts that matter

- **The encryption ladder.** WEP (broken), WPA/WPA2-PSK (crackable via handshake + weak passphrase), WPA2-Enterprise (802.1X), WPA3 (SAE — resists offline cracking).
- **The WPA2 attack.** You don't crack the encryption; you capture the 4-way **handshake** and brute-force the passphrase offline. Strong passphrases defeat it.
- **Rogue AP / evil twin.** Stand up a look-alike SSID; clients auto-associate; you MITM or capture credentials via a fake captive portal.
- **Deauthentication.** Forcing a client off (to capture a fresh handshake) — and why management-frame protection (802.11w) stops it.

## Command cheat-sheet

```bash
# --- Put the adapter into monitor mode ---
sudo airmon-ng start wlan0                 # creates wlan0mon
iw dev                                     # confirm mode

# --- Discover networks & clients ---
sudo airodump-ng wlan0mon                  # survey: BSSID, channel, clients
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w cap wlan0mon   # target one AP

# --- Capture the WPA2 handshake ---
sudo aireplay-ng --deauth 5 -a AA:BB:CC:DD:EE:FF wlan0mon   # nudge a client to reconnect
#   watch for "WPA handshake:" in the airodump header

# --- Crack the passphrase offline ---
aircrack-ng -w rockyou.txt -b AA:BB:CC:DD:EE:FF cap-01.cap
hashcat -m 22000 cap.hc22000 rockyou.txt   # modern WPA cracking

# --- WPS (if enabled) ---
sudo reaver -i wlan0mon -b AA:BB:CC:DD:EE:FF -vv
```

## Walk it in your lab

Target: **your own AP**, ideally a spare router you set with a known weak passphrase.

1. Enable monitor mode; survey the band with `airodump-ng` and identify your AP's BSSID/channel.
2. Lock onto your AP and capture; use a single `aireplay-ng` deauth to force a reconnect and grab the handshake.
3. Crack the (deliberately weak) passphrase from your own wordlist with `aircrack-ng`/`hashcat`.
4. Change the passphrase to a long random one and repeat — watch the crack fail. That contrast is the whole point.
5. Read up on evil-twin/captive-portal mechanics conceptually (don't run against real clients).

## What good looks like

A captured handshake, a successful crack of your *own* weak passphrase, and a demonstration that a strong passphrase (and WPA3/802.11w) defeats the same attack.

## Detection & defence

| Attack | Defence |
|---|---|
| Handshake capture + offline crack | Long random passphrase; WPA3-SAE; WPA2-Enterprise |
| Deauth flood | 802.11w (protected management frames) |
| Rogue AP / evil twin | Wireless IDS; 802.1X; client config that won't auto-join open SSIDs |
| WPS PIN brute (Reaver) | Disable WPS entirely |

## Common junior mistakes

- Testing against a neighbour's or public network — that's a crime, not a lab.
- Using an adapter that can't do monitor mode/injection and blaming the tools.
- Thinking you "crack WPA2" — you crack a weak *passphrase*; a strong one is safe.

## Go deeper

- [Aircrack-ng docs](https://www.aircrack-ng.org/documentation.html) · [hashcat WPA mode 22000](https://hashcat.net/wiki/) · Wi-Fi Alliance — WPA3

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
