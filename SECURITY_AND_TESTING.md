# Security and Quality Engineering

## Security model
- Supabase Auth for sessions and identity.
- PostgreSQL RLS and server-side authorization to protect client records.
- Staff permissions constrained by role and assignment.
- Private object storage for sensitive tax documents.
- Separation of client-provided source documents from staff-prepared return artifacts.
- Audit logging for important workflow changes.
- Distinct public and authenticated AI access levels; client-specific facts are not exposed to anonymous visitors.

## Quality approach
- Authorization regression cases: owner vs. other client, revoked session, staff gates, audit preservation.
- Intake workflow cases: incremental draft save, resume, Step 3 submission, agreement-gated completion.
- Browser workflow tests across desktop and mobile viewport sizes.
- TypeScript checks, lint, and production build checks.
- Read-only deployed-schema preflight before consequential database migrations.

## Evidence and limitations
A recent intake/release workstream reported 63 focused tests and three desktop/mobile browser tests passing locally, together with TypeScript, lint and build checks. A separate draft-intake authorization candidate passed local SQL security regression and agreement-completion tests. These are **local validation reports**, not claims of a completed production release or an independent security certification.

## Release discipline
Sensitive schema changes remain candidates until deployed prerequisites, backup/recovery readiness, and explicit deployment approval are established. Current hosted draft-intake and agreement-publication blockers remain open.

## Privacy note
No real client data, auth tokens, environment files, private SQL policy definitions, signed forms or storage objects belong in this public portfolio.
