# Architecture and Engineering Approach

## Purpose
ATOZ E-File coordinates taxpayer onboarding, private document collection, preparer/reviewer work, and AI-assisted support. The current implementation uses Next.js/TypeScript and Supabase Postgres, Auth and private Storage. This document is a **conceptual portfolio view**, not a production network diagram.

## Trust boundaries
1. **Anonymous public visitor:** May receive general tax guidance; must not retrieve private taxpayer facts.
2. **Authenticated client:** May work on their own intake, documents and authorized return status.
3. **Assigned staff:** Access is role- and assignment-aware, with additional restrictions for sensitive actions.
4. **Admin:** Administrative operations remain authenticated and permission checked.
5. **AI service:** Server-side orchestration, scoped tools, source grounding and no direct unrestricted database access.

## Primary workflows
- **Client:** Sign in → basic information → spouse/dependents → tax questionnaire → agreements → document upload → status and support.
- **Staff:** Assignment → document review → preparation → reviewer workflow → explicit release of approved return artifacts.
- **AI:** Request → identify public or authenticated context → apply authorization → retrieve approved knowledge or permitted workflow data → respond with appropriate boundaries.

## Data model concepts
Profiles, tax intakes, dependents, documents, tax returns, assignment data, knowledge sources/chunks and audit logs. Private document storage is partitioned by client and tax year. Exact schema, internal object names, and access policies are intentionally omitted from this public portfolio.

## Integration approach
Keep application APIs and business rules reusable. Future Drake Tax integration should use supported import/export pathways, idempotent processing, retries, validation and human review. No production Drake integration is claimed.

## Nonfunctional priorities
Least privilege, traceability, safe migration rollout, privacy, accessible responsive UX, failure recovery and maintainability.
