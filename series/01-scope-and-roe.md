# Lesson 01: Permission is more specific than a hostname

**Goal:** decide whether a proposed action fits the agreed scope before opening a scanner.

**Prerequisites:** none. This is a desk exercise. Names, addresses, people and decisions below are fictional. No requests should be sent.

## The scenario

A fictional company, Cedar Learning, asks you to assess a staging application. The message says, "Please test the portal." The login page redirects to an external identity provider, and the application sends email through another supplier.

You have an invitation, but not yet enough information to conduct the assessment safely. The application owner might control the portal without controlling the identity provider. A successful login does not expand the permission you were given.

Before testing, turn the request into an explicit agreement: who can authorize each asset, which environment and accounts are included, what actions are allowed, when work can occur, who can stop it, and how evidence is handled. Record approval through the organization's agreed process. This educational worksheet is not a legal authorization document.

## A fictional scope decision

| Question | Agreed example |
|---|---|
| Target | `portal.cedar.example`, staging only, HTTPS port 443 |
| Included functionality | `/api/` and the browser workflows using those APIs; separate written confirmation of ownership |
| Accounts | Two disposable learner accounts and one disposable administrator account |
| Allowed methods | Manual navigation, proxy inspection and individually planned access-control tests using synthetic records |
| Excluded assets | Production, identity-provider infrastructure, payment and email suppliers, other hostnames |
| Excluded actions | Load testing, credential guessing, social engineering, destructive operations and persistence |
| Test window | An explicitly dated two-hour window, stated in UTC in the approved plan |
| Stop conditions | Unexpected personal data, service degradation, account lockout or unexpected external requests |
| Response | Stop the affected activity, preserve minimal evidence, contact the named owner and wait for revised approval |
| Evidence | Synthetic records only; access-controlled storage; retention period and deletion responsibility agreed before testing |

An allowlisted hostname alone does not express all these conditions. Nor does an automatic "in scope" result replace the approved plan.

## The redirect trap

During ordinary navigation, the browser moves to a supplier's sign-in page. That page being part of the user journey does not authorize active testing of the supplier. Review redirect behavior and scope settings before using automation; do not assume a proxy's target filter prevents every extension, browser or scanner from sending requests elsewhere.

Distinguish the activities precisely. Inspecting an already captured response is different from replaying it. A passive-analysis feature may inspect traffic without adding probes, while the browser used to generate the traffic still makes requests. Describe the actual network behavior, not just the product's label.

## Your exercise

Decide **proceed**, **stop**, or **clarify first**, and explain why:

1. Read the response from an approved manual login using a disposable account.
2. Test the same endpoint on the production hostname because staging lacks data.
3. Turn on a password-guessing extension against the supplier's login page.
4. Replay one planned access-control request between the two disposable learner accounts during the test window.
5. Continue after the application returns unexpected personal information.
6. Run a crawler whose form submissions and redirect destinations have not been reviewed.

### Reasoned answers

1. **Proceed**, within the fictional approved workflow and evidence limits.
2. **Stop.** Production is excluded; new written approval and scope are needed.
3. **Stop.** Both the method and asset are excluded.
4. **Proceed**, only when that specific operation fits the approved plan and uses synthetic records. "Replay" is an active request, not passive observation.
5. **Stop the affected activity** and follow the escalation procedure. Do not gather extra records to make the report more persuasive.
6. **Clarify first.** Review behavior and obtain approval for the methods and destinations before running it.

## Completion check

You should be able to explain the distinction between authorization, asset scope, method restrictions and evidence handling without mentioning a tool. Write one paragraph describing why two requests to the same host can have different authorization outcomes.

## Further reading

- [NIST SP 800-115](https://csrc.nist.gov/pubs/sp/800/115/final): technical security assessment planning and execution guidance.
- [OWASP Web Security Testing Guide](https://owasp.org/projects/web-security-testing-guide): testing methodology and versioned references. Use a specified release when documenting individual test cases.

These sources provide background; the Cedar scenario and worksheet are original teaching examples, not copied organizational templates. Reviewed: 25 September 2026.
