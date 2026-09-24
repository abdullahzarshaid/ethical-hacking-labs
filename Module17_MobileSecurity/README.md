# Module 17 — Mobile Security

> *A phone is a computer, a sensor and a wallet — its app store is your attack surface.*

**The goal.** Understand the Android/iOS security models and the mobile attack surface (the app, its local storage, its network traffic, its backend API), and learn to assess an app against the OWASP Mobile Top 10.

**Where it fits.** Mobile apps are thick clients over the web APIs you learned to test in Modules 13–15. Much of mobile testing is *still* API testing — plus device-specific storage and platform issues.

## Concepts that matter

- **The platform model.** App sandboxing, permission models, code signing, and why a *rooted/jailbroken* device is needed for deep testing.
- **Where the bugs live.** Insecure local storage (secrets in SharedPreferences/plist/SQLite), weak transport (no TLS/no pinning), hardcoded secrets in the APK, and — most often — the **backend API**.
- **Static vs dynamic.** Static: decompile and read the app. Dynamic: run it and watch traffic/behaviour, often with an intercepting proxy and a hooking framework.
- **OWASP MASVS/MASTG** is the standard methodology.

## Command / tool cheat-sheet

```bash
# --- Get & inspect the app (Android) ---
adb devices                                  # connect a test device/emulator
adb shell pm list packages                   # installed packages
adb pull /data/app/<pkg>/base.apk            # get the APK
apktool d base.apk                            # decode resources & smali
jadx-gui base.apk                             # decompile to readable Java

# --- Static hunt for secrets ---
grep -riE "api[_-]?key|password|secret|token|http://" base.apk-decoded/
strings base.apk | grep -iE "AKIA|firebaseio|http"

# --- All-in-one static analysis ---
# MobSF (Mobile Security Framework) — upload the APK/IPA, get a full report

# --- Dynamic: intercept the API traffic (the money layer) ---
# 1) Route the device proxy → Burp   2) install Burp CA
# 3) if cert-pinned:  objection -g <pkg> explore  →  android sslpinning disable
frida-ps -U                                   # list processes for hooking
```

## Walk it in your lab

Target: an **intentionally vulnerable app** (e.g. DIVA, InsecureBankv2, OWASP MASTG crackmes) on an emulator.

1. Pull and decompile the APK with `apktool`/`jadx`; hunt for hardcoded secrets and endpoints.
2. Run **MobSF** for a fast static overview; confirm its findings in the code.
3. Proxy the app's traffic through Burp; if pinned, disable pinning with `objection`/`frida`.
4. Test the **backend API** exactly as in Module 14 (authz, IDOR, injection).
5. Inspect local storage (`/data/data/<pkg>/`) for secrets written in the clear.

## What good looks like

Findings split cleanly into *client-side* (storage, pinning, hardcoded secrets) and *server-side* (the API), each with evidence — decompiled snippet, stored-secret path, or intercepted request.

## Detection & defence

| Weakness | Defence |
|---|---|
| Secrets in the APK | Never ship secrets client-side; use short-lived server-issued tokens |
| Insecure local storage | Encrypt at rest; use Keystore/Keychain |
| No TLS / no pinning | Enforce TLS; certificate pinning |
| Weak backend API | Apply full API security (authz on every call) |

## Common junior mistakes

- Testing only the app UI and ignoring the backend API, where most real impact is.
- Concluding "secure" from a non-rooted device that blocks half the tests.
- Reporting a hardcoded string without confirming it's a live, sensitive secret.

## Go deeper

- [OWASP MASTG/MASVS](https://mas.owasp.org) · [MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF) · [objection](https://github.com/sensepost/objection) · [Frida](https://frida.re)

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
