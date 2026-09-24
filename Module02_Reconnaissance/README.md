# Module 02 — Reconnaissance

> *Learn everything you can about a target before you send it a single packet.*

**The goal.** Build a map of the target's attack surface — domains, subdomains, hosts, people, technologies, exposed services — using public information first. Good recon decides how efficient the rest of the engagement is.

**Where it fits.** This is the first phase. Everything downstream (scanning, enumeration, exploitation) is only as good as the surface you found here. Passive recon touches no target system; active recon starts sending light probes.

## Concepts that matter

- **Passive vs active.** Passive uses third-party data (DNS records, search engines, certificate logs) and is invisible to the target. Active sends traffic to the target and can be logged.
- **Footprinting.** Turning scattered public data into a structured picture: name servers, mail servers, IP ranges, ASNs, tech stack, employees, email format.
- **Attack surface.** Every host, subdomain, port, login page and third-party service that could be a way in.
- **OSINT discipline.** Record the *source* of every fact so you can trust it later and hand it to a client.

## Command cheat-sheet

```bash
# WHOIS + DNS basics
whois example.com                       # registrar, org, name servers, dates
dig example.com ANY +noall +answer      # A, MX, NS, TXT in one shot
dig +short txt example.com              # SPF/DMARC hints, verification records
host -t mx example.com                  # mail servers

# Subdomain discovery (passive)
subfinder -d example.com -silent        # fast passive subdomain enum
amass enum -passive -d example.com      # broader passive sources
curl -s "https://crt.sh/?q=%25.example.com&output=json" | jq -r '.[].name_value' | sort -u   # cert transparency

# People / emails / exposure
theHarvester -d example.com -b bing,crtsh   # emails, hosts, names
# Google dorks (run in a browser):
#   site:example.com -www           inurl:admin           filetype:pdf site:example.com
#   intitle:"index of" site:example.com

# Live-host / tech fingerprint (light active)
httpx -silent -title -tech-detect -status-code -l subdomains.txt
whatweb https://example.com
```

> **Shodan / Censys** (browser or API) round this out — search an org or IP range for internet-exposed services without touching the target yourself.

## Walk it in your lab

Recon is best practised against a domain **you own** or an authorised training target. Then:

1. Pick your own domain (or a bug-bounty scope that permits recon).
2. Run WHOIS + `dig ANY`; write down name servers, mail servers and any TXT records.
3. Enumerate subdomains with `subfinder` and `crt.sh`; merge and de-duplicate the list.
4. Probe the live ones with `httpx`; note titles, technologies and status codes.
5. Draw the surface: a simple list of `subdomain → IP → service → tech`. That table *is* your recon deliverable.

## What good looks like

A single, sourced inventory: hostnames, resolved IPs, open web services, detected technologies, and any interesting exposures (old subdomains, staging sites, exposed panels) — each with the tool/source that found it.

## Detection & defence

| Attacker signal | Defender response |
|---|---|
| Subdomain/cert-log mining | Minimise public DNS/cert exposure; retire stale subdomains |
| Employee/email harvesting | Awareness training; limit what's public on the org site |
| Tech fingerprinting | Remove version banners; standardise error pages |

## Common junior mistakes

- Jumping straight to active scanning and skipping the free, quiet, high-value passive phase.
- Not recording where a "fact" came from — then trusting a stale DNS record.
- Treating every discovered subdomain as in-scope. **Recon respects the scope, too.**

## Go deeper

- OWASP WSTG — [Information Gathering](https://owasp.org/www-project-web-security-testing-guide/)
- [crt.sh](https://crt.sh) · [Shodan](https://www.shodan.io) · [amass](https://github.com/owasp-amass/amass)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
