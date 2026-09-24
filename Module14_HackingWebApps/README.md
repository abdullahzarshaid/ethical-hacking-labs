# Module 14 — Hacking Web Applications

> *The app's own logic is the biggest attack surface most organisations have. This is where the OWASP Top 10 lives.*

**The goal.** Test the application layer — authentication, access control, input handling, business logic — using the OWASP Testing Guide as your map. This is the highest-demand skill set in the field.

**Where it fits.** Module 13 covered the server; this covers the code running on it. SQL injection (Module 15) is one class of web-app flaw important enough to get its own module.

## The map: OWASP Top 10 (2021)

| # | Category | What you're testing for |
|---|---|---|
| A01 | Broken Access Control | IDOR, missing authz, forced browsing, privilege escalation |
| A02 | Cryptographic Failures | Cleartext transport, weak hashing, exposed secrets |
| A03 | Injection | SQLi, command, LDAP, NoSQL (+ XSS) |
| A04 | Insecure Design | Missing controls by design; abuse cases |
| A05 | Security Misconfiguration | Defaults, verbose errors, open cloud storage |
| A06 | Vulnerable Components | Known-vulnerable libraries/frameworks |
| A07 | Auth Failures | Weak passwords, no lockout, broken session handling |
| A08 | Integrity Failures | Insecure deserialization, unsigned updates |
| A09 | Logging & Monitoring | Attacks that leave no trace |
| A10 | SSRF | Server tricked into making attacker-chosen requests |

## Command / tool cheat-sheet

```text
# --- The core toolkit ---
Burp Suite      # intercepting proxy — the center of all web testing
  Proxy         intercept & modify requests/responses
  Repeater      hand-craft and replay single requests
  Intruder      fuzz a parameter across a payload set
  Decoder       encode/decode base64, URL, hex

# --- Content & parameter discovery ---
gobuster dir -u http://TARGET -w wordlist.txt -x php,html
ffuf -u http://TARGET/FUZZ -w wordlist.txt                 # dir/file fuzz
ffuf -u http://TARGET/?id=FUZZ -w nums.txt -fc 404          # parameter fuzz

# --- Common manual test payloads ---
XSS      <script>alert(document.domain)</script>   "><img src=x onerror=alert(1)>
IDOR     change /account?id=1001 → 1002              # access-control test
Cmd inj  ; id    | id    `id`    $(id)
SSRF     url=http://169.254.169.254/latest/meta-data/   # cloud metadata
Path     ../../../../etc/passwd

# --- Automated crawl/scan (then verify by hand) ---
nikto -h http://TARGET
# Burp active scan (Pro) — always confirm findings manually
```

## Walk it in your lab

Target: **OWASP Juice Shop** (modern) and **DVWA** (classic).

1. Proxy the app through Burp; map every page, parameter and API call.
2. Work the Top 10 in order. Start with **Broken Access Control**: change an `id` and see if you read another user's data (IDOR).
3. Test **XSS**: inject a payload into each input and see where it reflects/stores unescaped.
4. Test **auth**: weak password policy? account lockout? does logout kill the session (link to Module 11)?
5. For each confirmed issue, capture the request/response in Burp — that's your proof.

## What good looks like

Per-finding: the category, the exact request that triggers it, the response proving impact, and a fix. A screenshot of an alert box is a start; the raw request/response is the evidence that survives review.

## Detection & defence

| Weakness | Defence |
|---|---|
| Broken access control | Server-side authz on every request; deny by default |
| XSS | Context-aware output encoding; CSP; framework auto-escaping |
| Injection | Parameterized queries; input validation; least privilege |
| Auth failures | MFA, lockout, strong session management |
| SSRF | Allow-list egress; block link-local/metadata IPs |

## Common junior mistakes

- Running an automated scan and reporting its output without manual confirmation.
- Testing only reflected XSS and missing stored/DOM variants.
- Ignoring access control because it's "boring" — it's #1 for a reason.

---

## Worked example — student lab (CY201)

This module includes a full class lab write-up by its student authors, kept as submitted:

- **[Module14_Group_8.docx](Module14_Group_8.docx)** — the group's report
- **[commands.txt](commands.txt)** · **[tools.txt](tools.txt)**
- Step screenshots under **[`cyber ss/`](cyber%20ss)** (by task)

*This work was produced by its student group and remains theirs, credited to them. It's here as a worked example of the phase above.*

## Go deeper

- [OWASP Top 10](https://owasp.org/Top10/) · [OWASP WSTG](https://owasp.org/www-project-web-security-testing-guide/) · [PortSwigger Web Security Academy](https://portswigger.net/web-security) (free, excellent)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
