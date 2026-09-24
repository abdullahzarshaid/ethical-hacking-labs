# Module 09 — Social Engineering

> *The most reliable way into an organisation is still a person, not a port.*

**The goal.** Understand how attackers manipulate people — phishing, pretexting, baiting, tailgating — and how to run an **authorised** awareness exercise and build human-layer defences. This is high-impact and high-responsibility work.

> ⚠️ Social engineering targets real people. Run it **only** with explicit written authorisation, a defined scope, and a plan that protects the humans involved. Never test individuals without consent.

## Concepts that matter

- **The levers.** Authority, urgency, fear, curiosity, reciprocity, social proof — the psychology every pretext leans on.
- **Vectors.** Phishing (email), spear-phishing (targeted), vishing (voice), smishing (SMS), pretexting, baiting (USB drop), tailgating (physical).
- **The kill chain is human.** Recon (OSINT from Module 02) → pretext → delivery → the click → credential capture or payload.
- **Measurement, not blame.** An awareness exercise reports rates and lessons, never punishes named individuals.

## Toolkit & workflow

```text
# Frameworks (authorised engagements only)
GoPhish            # open-source phishing-campaign platform (templates, landing pages, tracking)
SET (Social-Engineer Toolkit)   # credential-harvester & attack templates
evilginx / Modlishka            # advanced (MFA-relay) — study to defend against

# The workflow
1. Scope & authorisation   — who, what, the rules, the escalation contact
2. OSINT                   — email format, org chart, tone (from Module 02)
3. Pretext design          — a believable, specific scenario
4. Build                   — lookalike domain, template, tracked landing page
5. Send + track            — opens, clicks, submissions (aggregate metrics)
6. Report + train          — rates, lessons, targeted awareness; never name-and-shame
```

## Walk it in your lab

Practise the **mechanics** safely against your own test accounts — never real people without consent.

1. Stand up **GoPhish** in your lab.
2. Build a phishing email + a cloned login landing page for a fake internal service you control.
3. Send it to your own test mailbox; watch the open/click/submit tracking work end to end.
4. Note exactly what data the landing page could capture — and how a defender would spot the lookalike domain.
5. Write the exercise up as *rates and lessons*, the way a real awareness report reads.

## What good looks like

An exercise report with aggregate metrics (send/open/click/submit rates), the pretext that worked and why, and concrete, kind remediation: training, reporting buttons, technical controls — no individual named.

## Detection & defence

| Attack | Defence |
|---|---|
| Phishing email | SPF/DKIM/DMARC, mail filtering, a one-click "Report Phish" button |
| Credential-harvest page | MFA (phishing-resistant/FIDO2), password managers that won't autofill lookalikes |
| Vishing / pretext calls | Call-back verification; never share secrets on inbound calls |
| Tailgating / USB drops | Physical access control; disable USB autorun; awareness culture |

## Common junior mistakes

- Running anything against real people without written authorisation and a safety plan.
- Designing a "gotcha" instead of a measurement — it destroys trust and teaches nothing.
- Forgetting MFA and reporting culture are the controls that actually move the numbers.

## Go deeper

- [GoPhish](https://getgophish.com) · [DMARC guide](https://dmarc.org) · CISA phishing guidance

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
