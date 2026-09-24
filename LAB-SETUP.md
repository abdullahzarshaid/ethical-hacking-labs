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

Use a dedicated **Internal Network** in VirtualBox (or equivalent VM-only network). Connect attacker
and targets to the same named network. Do not attach NAT, bridged or second internet-connected
interfaces during exercises. **NAT normally permits outbound internet access; it is not isolation.**
Host-only networking exposes the host to guests; use it only when that access is intentional and
restricted. Disable routing/connection sharing and never forward ports to vulnerable targets.

- Confirm your attacker and targets can ping each other.
- Inspect adapters and routing tables; targets should have no external default route.
- Verify external traffic cannot leave the lab. A failed ping alone does not prove isolation.
- Install updates during a separate maintenance phase, then remove external connectivity before labs.

A simple layout is a VM-only `10.10.10.0/24` network with static addresses and no gateway or external
DNS server. Check that it does not overlap networks otherwise routed by your machine.

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
