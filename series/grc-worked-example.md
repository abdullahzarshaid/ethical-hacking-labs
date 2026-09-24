# From a technical observation to a risk decision

**Fictional teaching example.** No client finding, real audit outcome or certification claim is represented here. This is not an ISO control crosswalk or a substitute for organization-specific risk criteria.

Cedar Learning's fictional staging API lets disposable learner A read a synthetic record assigned to disposable learner B. The approved permission matrix says only the owner and administrator should read that record. An authorized controlled comparison supports an access-control finding. A lone HTTP 200 response would not establish the same conclusion.

## Keep the decisions separate

| Field | Worked example |
|---|---|
| Observation | Account A obtained B's synthetic record in the approved staging exercise |
| Expected behavior | Ownership/role policy prevents that cross-account read |
| Evidence reference | `LAB-AC-01`: fictional request/response pair, account-role mapping and control requests |
| Technical interpretation | Server-side authorization did not enforce the stated object ownership rule in this tested operation |
| Business risk hypothesis | If equivalent behavior exists where sensitive records are held, cross-account disclosure could occur |
| Unknowns | Production behavior, affected record population, other endpoints and existing compensating controls |
| Risk rating | Not assigned until impact and likelihood are assessed against agreed organizational criteria |
| Control objective | Enforce authorization for each protected object and operation on the server |
| Treatment | Correct authorization logic; add positive and negative regression tests |
| Accountable owner | Fictional application owner; engineering implements the change |
| Due date | Agreed according to the risk decision; not invented by the tester |
| Exception path | Documented approval by the designated risk owner, with rationale, scope, expiry and review |
| Verification | Repeat the original negative test and legitimate-user controls on the identified fixed build |
| Closure | Record the tested build, results and untested scope; do not call every access-control path secure |

## Why this is GRC rather than another scanner result

The technical result answers what happened in a controlled test. Governance identifies who is accountable. Risk assessment considers consequences and uncertainty. Treatment selects a response. Verification checks whether the agreed change works. None of those decisions follows automatically from the name of a vulnerability or a tool's READY badge.

A useful risk record preserves the evidence link without copying sensitive payloads into every spreadsheet or report. A developer closing a ticket is not the same as a tester verifying the change. An exception is a recorded acceptance decision, not a technical pass.

## Practice

Change the scenario so production uses a different authorization service. Which statements remain supported? Keep the staging observation, but do not claim a production vulnerability without separate authorization and evidence. List the additional information needed by the risk owner.

For general assessment and mitigation background, see [NIST SP 800-115](https://csrc.nist.gov/pubs/sp/800/115/final). For testing-method references, see [OWASP WSTG](https://owasp.org/projects/web-security-testing-guide). No copyrighted standard text is reproduced. Reviewed: 25 September 2026.
