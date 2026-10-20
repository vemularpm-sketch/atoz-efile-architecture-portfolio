# Engineering Case Study: Secure Draft Intake Before Agreement Signing

## Context
The five-step client intake wizard collects basic details, spouse/dependent information, a tax questionnaire, agreement review and uploads. Clients need to save partial work before they reach the signing step.

## Observed failure
A hosted request to save intake returned a database authorization error indicating annual authorizations were incomplete or tax-data access was denied. The problem was traced to an overly early authorization gate, not a visual form issue.

## Options considered
| Option | Benefit | Risk |
|---|---|---|
| Remove annual authorization checks entirely | Fastest apparent fix | Weakens staff-processing and completion boundaries |
| Require agreements before any draft save | Simplifies enforcement | Blocks the intended onboarding experience |
| Distinguish owner draft saves from final completion | Preserves usability and security | Requires more precise policies and regression tests |

## Chosen design
Permit a live, authenticated client to save **only their own draft** before signing. Keep tax questionnaire submission available so the client can reach agreements. Require agreements for final onboarding completion and staff processing. Continue rejecting cross-client access and revoked sessions; preserve audit events.

## Verification
Candidate SQL migration and tests were prepared locally. Owner draft/resume, questionnaire progression, cross-client denial, revoked-session denial, staff-processing gate and audit checks passed local review.

## Deployment state
**Not deployed.** Remote migration prerequisites, RLS/function/trigger baseline and recovery readiness must be verified before authorized execution. This case study demonstrates an engineering decision and local validation, not a production fix.

## Takeaway
Security and usability should not be treated as opposing goals. Explicit workflow states and trust boundaries make it possible to preserve both, at the cost of more thorough testing and rollout discipline.
