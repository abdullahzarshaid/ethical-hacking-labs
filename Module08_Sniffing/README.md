# Module 08 — Sniffing

> *Read the traffic on the wire — and understand why a switch doesn't protect you.*

**The goal.** Capture and interpret network traffic, understand the difference between passive sniffing and active man-in-the-middle attacks, and see first-hand why cleartext protocols and unauthenticated ARP are dangerous.

**Where it fits.** Sniffing supports many phases — harvesting credentials during system hacking, capturing tokens for session hijacking (Module 11), and understanding the network for lateral movement.

## Concepts that matter

- **Passive vs active.** On a hub or your own host, you just listen. On a switch, you must actively poison (ARP spoofing) to see others' traffic — that's a MITM attack.
- **ARP has no authentication.** That single fact makes ARP spoofing possible on most flat LANs.
- **Cleartext protocols leak.** HTTP, FTP, Telnet, POP3, SNMPv1/2 send credentials in the clear.
- **MAC flooding vs ARP spoofing.** Flooding overwhelms the CAM table (fail-open); spoofing redirects specific hosts.

## Command cheat-sheet

```bash
# --- Passive capture ---
sudo tcpdump -i eth0 -w capture.pcap                 # capture to file
sudo tcpdump -i eth0 port 80 -A                      # HTTP payloads, live
wireshark capture.pcap &                             # analyse in GUI
#   Wireshark display filters:
#     http.request      ftp        tcp.port==23      http.authbasic

# --- Active MITM (ARP spoofing) — lab only ---
sudo sysctl -w net.ipv4.ip_forward=1                 # forward so victim stays online
sudo ettercap -T -M arp:remote /10.10.10.5// /10.10.10.1//   # victim <-> gateway
sudo bettercap -iface eth0                           # then: net.probe on; arp.spoof on

# --- Pull credentials from a capture ---
#   Wireshark → File → Export Objects, or
tshark -r capture.pcap -Y "http.authorization" -T fields -e http.authorization
```

## Walk it in your lab

Target: two lab VMs (a "victim" and the gateway) on your host-only network.

1. Start a passive capture on your own host with `tcpdump`/Wireshark; generate some HTTP/FTP traffic and find the credentials in cleartext.
2. Enable IP forwarding, then ARP-spoof the victim ↔ gateway with `ettercap` or `bettercap`.
3. Watch the victim's traffic flow through you; capture a cleartext login.
4. Repeat over HTTPS and observe that you get ciphertext — see *why* TLS matters.
5. Restore ARP (stop the attack) and confirm connectivity returns.

## What good looks like

A capture file plus a short write-up: which protocols leaked, the exact credentials/tokens recovered, and a clear before/after showing that TLS defeated the same attack.

## Detection & defence

| Attacker signal | Defender response |
|---|---|
| ARP table churn / duplicate MACs | Dynamic ARP Inspection (DAI); static ARP for critical hosts |
| CAM-table flooding | Port security (limit MACs per port) |
| Cleartext creds on the wire | Enforce TLS/SSH everywhere; disable Telnet/FTP |
| Rogue MITM device | 802.1X; network access control; segmentation |

## Common junior mistakes

- Forgetting to enable IP forwarding — you black-hole the victim and tip them off.
- Not restoring ARP afterward, leaving the lab broken.
- Expecting to read HTTPS in cleartext — sniffing shows you exactly why you can't.

## Go deeper

- [Wireshark User Guide](https://www.wireshark.org/docs/wsug_html_chunked/) · [bettercap docs](https://www.bettercap.org)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
