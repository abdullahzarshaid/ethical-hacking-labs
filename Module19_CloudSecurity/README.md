# Module 19 — Cloud Security

> *In the cloud, most breaches aren't exploits — they're misconfigurations and leaked keys.*

**The goal.** Understand the shared-responsibility model, the cloud attack surface (IAM, storage, compute, serverless), and how to find the misconfigurations that cause the overwhelming majority of real cloud incidents.

**Where it fits.** Cloud is where the apps, APIs and data from earlier modules actually live now. The mindset shifts from "exploit a service" to "abuse an identity or a permission."

## Concepts that matter

- **Shared responsibility.** The provider secures *of* the cloud (hardware, hypervisor); *you* secure *in* the cloud (IAM, data, config). Most breaches are on the customer side.
- **IAM is the new perimeter.** Over-permissive roles, long-lived access keys, and privilege-escalation paths between roles are the crown-jewel risks.
- **Public storage.** Misconfigured S3/blob/GCS buckets leak data constantly — the signature cloud mistake.
- **The metadata service.** SSRF (Module 14) that reaches `169.254.169.254` can steal instance credentials — the classic cloud pivot.
- **Serverless & containers.** Functions with too many permissions; exposed container registries and Kubernetes dashboards.

## Command / tool cheat-sheet

```bash
# --- Enumerate what a set of credentials/role can do (AWS example) ---
aws sts get-caller-identity                    # who am I / which account?
aws s3 ls                                       # buckets I can see
aws iam list-attached-user-policies --user-name X
enumerate-iam --access-key ... --secret-key ... # what actions are allowed?

# --- Storage exposure ---
aws s3 ls s3://company-backups --no-sign-request   # public bucket?
# cloud_enum / S3Scanner — hunt public buckets across providers by keyword

# --- Posture / misconfig scanners (point at an account you own) ---
prowler aws                                     # CIS-benchmark posture scan (AWS)
scoutsuite aws                                  # multi-cloud posture report
# kube-hunter / kube-bench — Kubernetes

# --- The metadata pivot (from an SSRF on a cloud host) ---
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/   # AWS IMDSv1
```

## Walk it in your lab

Use a **free-tier account you own** — never someone else's tenant.

1. Create a deliberately misconfigured S3 bucket (public read); find it with `cloud_enum`/`--no-sign-request`. Then lock it down and confirm it's gone.
2. Create an over-permissive IAM user; run `enumerate-iam`/`prowler` and see the risky permissions flagged.
3. Run **Prowler** or **ScoutSuite** against your account for a full CIS-benchmark posture report; read the top findings.
4. Understand the IMDS/SSRF pivot conceptually; enforce **IMDSv2** and see why it breaks the classic attack.

## What good looks like

A posture report tied to CIS benchmarks: public storage, over-permissive identities, missing logging/encryption, and internet-exposed services — each with the concrete fix. Cloud assessment is configuration review at its core.

## Detection & defence

| Weakness | Defence |
|---|---|
| Public storage buckets | Block-public-access by default; access logging |
| Over-permissive IAM | Least privilege; no long-lived keys; short-lived roles |
| Metadata/SSRF credential theft | IMDSv2; egress controls; block link-local from apps |
| No visibility | CloudTrail/Activity logs → SIEM; posture management (CSPM) |

## Common junior mistakes

- Testing against a tenant you don't own — cloud scope and authorisation are strict.
- Looking for "exploits" when the finding is a checkbox in IAM.
- Ignoring logging findings — an undetected breach is the worst kind.

## Go deeper

- [Prowler](https://github.com/prowler-cloud/prowler) · [ScoutSuite](https://github.com/nccgroup/ScoutSuite) · CIS Benchmarks · provider Well-Architected security pillars

---

> New here? Start with the **[lab setup guide](../LAB-SETUP.md)** and work the modules in order.
