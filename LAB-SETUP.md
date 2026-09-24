# Build Your Safe Practice Lab

You cannot learn offensive security by reading. You need a lab you fully own and control, isolated from
the internet and from any real network. This guide gets you there in about an hour.

> ⚠️ Everything in this repository is practised **only** inside this isolated lab, or against systems you
> have explicit written permission to test. Attacking anything else is illegal.

## 1. The hypervisor

Install one virtualization tool:

- **VirtualBox** (free, all platforms) — fine for everything here.
- **VMware Workstation Player / Pro** — smoother on Windows.

## 2. The attacker machine

Download and import one attacker distribution — they come pre-loaded with the tools:

- **Kali Linux** — the standard. Grab the VirtualBox/VMware image from the official site and import it.
- **Parrot Security OS** — a lighter alternative.

Give it 2 CPUs and 4 GB RAM if you can.

## 3. The targets (deliberately vulnerable — by design)

Add one or two victims so you always have something legal to attack:

| Target | Good for |
|---|---|
| **Metasploitable 2/3** | Linux services, enumeration, system hacking |
| **OWASP Juice Shop** or **DVWA** | Web app attacks, XSS, SQL injection |
| **A Windows evaluation VM** | Windows enumeration, SMB, privilege escalation |
| **VulnHub / TryHackMe / HackTheBox** images | Endless extra practice boxes |

## 4. The network — keep it isolated

Set **every VM's network adapter** to **Host-Only** or an **internal NAT network**, not Bridged. This
keeps your lab traffic off your real LAN and off the internet.

- Confirm your attacker and targets can ping each other.
- Confirm the lab **cannot** reach the internet from the target side.

A common layout is a single `10.10.10.0/24` or `192.168.56.0/24` host-only network with the attacker and
targets on it.

## 5. Snapshot before you break things

Take a VM **snapshot** of each machine once it is set up and clean. When a lab leaves a box in a broken
state (it will), you roll back in seconds instead of rebuilding.

## 6. Work the modules in order

Now start at **[Module 02 — Reconnaissance](Module02_Reconnaissance/)** and move through the phases. Each
module's overview tells you what to do; the lab reports show it done end to end.

---

**Golden rules**

1. Only touch machines in this lab or ones you are authorised in writing to test.
2. Never point a tool at an address you did not intend — double-check the target every time.
3. Snapshot before, roll back after.
4. Learning to attack is how you learn to defend — keep it legal, keep notes, and capture your evidence.
