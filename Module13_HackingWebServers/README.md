# Module 13 — Hacking Web Servers

> *Attack the server that hosts the app — its version, its config, its files — before you even touch the app logic.*

**The goal.** Assess the web-server layer (Apache, Nginx, IIS): identify the software and version, find misconfigurations and known CVEs, and understand the difference between attacking the *server* and attacking the *application* (Module 14).

**Where it fits.** The server is the platform the app runs on. A default install, an exposed admin panel, directory listing, or an unpatched server version can be the way in — independent of the app's own code.

## Concepts that matter

- **Server vs application.** Here you target the HTTP server, its modules, TLS config, default files and management interfaces — not the app's business logic.
- **Fingerprinting.** Server header, error-page style, supported methods, default paths → software + version.
- **Common server-layer flaws.** Outdated version with a public CVE, directory listing enabled, dangerous HTTP methods (PUT/DELETE), exposed `.git`/backup files, default credentials on a management console, weak TLS.
- **Config beats code.** Many server compromises are misconfiguration, not a memory-corruption exploit.

## Command cheat-sheet

```bash
# --- Fingerprint the server ---
curl -sI https://10.10.10.5                     # Server:, X-Powered-By, headers
whatweb -a 3 https://10.10.10.5
nmap -sV -p80,443 --script http-headers,http-methods 10.10.10.5

# --- Misconfiguration & known-issue scan ---
nikto -h https://10.10.10.5                      # classic server-misconfig scanner
nmap --script http-enum -p80 10.10.10.5          # common paths/panels

# --- Content & hidden files ---
gobuster dir -u http://10.10.10.5 -w /usr/share/wordlists/dirb/common.txt -x php,txt,bak
feroxbuster -u http://10.10.10.5 -w wordlist.txt
curl -s http://10.10.10.5/.git/HEAD              # exposed VCS metadata?

# --- TLS & methods ---
testssl.sh https://10.10.10.5                    # protocols, ciphers, cert
curl -s -X OPTIONS http://10.10.10.5 -i | grep -i allow    # dangerous methods?

# --- Map version → exploit ---
searchsploit apache 2.4.49
```

## Walk it in your lab

Target: **Metasploitable 2** (Apache), a vulnerable IIS/Apache VM, or a Docker image of an old server.

1. Fingerprint the server and pin the exact version (`curl -I`, `whatweb`).
2. Run `nikto` and `gobuster`; note misconfigs, listable directories, and interesting files.
3. Check HTTP methods and TLS config; flag anything dangerous.
4. Map the version to CVEs with `searchsploit`; verify one candidate actually applies.
5. If a safe, well-understood exploit exists for your lab target (e.g. a path-traversal/RCE CVE), demonstrate it — with evidence.

## What good looks like

A server-layer findings list: software + exact version, each misconfiguration with evidence, TLS/method issues, and any confirmed CVE — clearly separated from application-logic findings.

## Detection & defence

| Weakness | Defence |
|---|---|
| Outdated server version | Patch cadence; remove version banners |
| Directory listing / exposed files | Disable autoindex; block `.git`/backups; deny by default |
| Dangerous HTTP methods | Allow only needed methods |
| Weak TLS | Modern cipher suites; HSTS; disable old protocols |
| Default admin consoles | Change defaults; restrict by IP/VPN; MFA |

## Common junior mistakes

- Blurring server and app findings — they have different owners and fixes.
- Reporting a version-based CVE without confirming the pre-conditions hold.
- Missing the free wins: directory listing, exposed `.git`, default consoles.

## Go deeper

- OWASP WSTG — Configuration & Deployment Management · [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks) (Apache/Nginx/IIS)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
