# Module 11 - Session Hijacking

**In one line.** Steal or fix a user's session, and understand why tokens must expire and rotate.

**Why it matters.** Sessions are the keys to authenticated access - and this ties directly to real web-security research (see CWE-613).

**Key concepts.** Session tokens and cookies, fixation, sidejacking, XSS-driven theft, token predictability, Secure/HttpOnly/SameSite flags.

**Core tools.** Burp Suite, browser developer tools, Wireshark.

**Practise in your lab.** Capture and replay a session cookie in your lab, then add HttpOnly/Secure and expiration and see what breaks the attack.

**Defender's view.** Short-lived rotating tokens, secure cookie flags, TLS, and re-authentication for sensitive actions.

> New here? Start with the [lab setup guide](../LAB-SETUP.md) and work the modules in order.

---

*Below: the CY201 class lab submission for this module, produced by its student authors and credited to them.*

# Module11_SessionHijacking\n\n## Instructions\n\nEach group assigned this module must create a folder like this:\n\n- GroupXX/\n  - CEH_ModuleXX_Report_GroupXX.docx\n  - screenshots/\n  - commands.txt\n  - tools-used.txt\n\n📌 Deadline: 15 May 2025\n📌 Submit via Pull Request ONLY\n\nInstructor: Mr. Abdullah Bin Zarshaid\nCourse: CY201 – Spring 2025
