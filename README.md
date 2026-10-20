<div align="center">

ATOZ E-File
Secure Tax Operations · AI-Assisted Workflows · Human Oversight
A full-stack and platform architecture case study for modern tax preparation
  
Project Website · [Architecture](docs/ARCHITECTURE.md) · [Security & Quality](docs/SECURITY_AND_TESTING.md) · [Engineering Case Study](docs/ENGINEERING_CASE_STUDY.md) · [AI & Mobile Roadmap](docs/ROADMAP.md)
</div>

Problem: Tax firms coordinate sensitive documents, client onboarding, preparer/reviewer assignments, and repetitive information entry across disconnected workflows.

Approach: Establish a secure, auditable workflow foundation, then introduce grounded AI and tax-software integrations with human review.

At a glance
	
Contribution	Solution architecture, full-stack implementation, database authorization, AI design, testing and release controls
Technology	Next.js 16 · React · TypeScript · PostgreSQL · Supabase Auth / Storage · OpenAI Responses API
Users	Tax clients · Preparers · Reviewers · Administrators
Current delivery	Active development; some features locally implemented and tested, others pending release
Future expansion	Document intelligence · Drake Tax integration · iOS and Android


Platform architecture
```mermaid
flowchart TB
    subgraph UX["Experience"]
        WEB["Responsive client portal"]
        STAFF["Staff and admin portal"]
        MOBILE["iOS / Android — planned"]
    end
    subgraph APP["Application layer"]
        API["Next.js server APIs"]
        ACCESS["Session and role checks"]
        WORK["Intake / document / return workflows"]
        AI["AI orchestration"]
    end
    subgraph DATA["Protected data"]
        DB[("PostgreSQL + RLS")]
        FILES[("Private object storage")]
        AUDIT["Audit logs"]
    end
    subgraph EXT["Knowledge and integration"]
        KNOW["Approved IRS / state sources"]
        EXTRACT["Document extraction — planned"]
        DRAKE["Drake integration — planned"]
    end
    WEB --> API
    STAFF --> API
    MOBILE -.-> API
    API --> ACCESS
    API --> WORK
    API --> AI
    WORK --> DB
    WORK --> FILES
    WORK --> AUDIT
    AI --> KNOW
    AI -.-> EXTRACT
    WORK -.-> DRAKE
```
Conceptual architecture. Dashed edges indicate planned work, not shipped integrations.
End-to-end tax preparation data flow
```mermaid
flowchart TD
    START["Client opens web portal"] --> LOGIN["Supabase authentication"]
    LOGIN --> INTAKE["Guided tax intake and draft save"]
    INTAKE --> AGREEMENTS{"Annual agreements completed?"}
    AGREEMENTS -->|No| DRAFT["Remain in client-owned draft state"]
    DRAFT --> INTAKE
    AGREEMENTS -->|Yes| UPLOAD["Upload W-2 / 1099 / supporting files"]
    UPLOAD --> STORE[("Private Supabase Storage")]
    STORE --> PREP["Authorized preparer reviews source documents"]
    STORE -.-> EXTRACT["AI extraction and validation — planned"]
    EXTRACT -.-> PREP
    PREP -.-> IMPORT["Supported Drake import / entry — planned"]
    IMPORT -.-> RETURN["Prepare federal and state returns in Drake"]
    PREP --> RETURN
    RETURN --> REVIEW["Reviewer quality check"]
    REVIEW --> CLIENT["Release prepared return for client review"]
    CLIENT --> AUTH["Obtain valid filing authorization using approved process"]
    AUTH --> EFILE["Authorized preparer e-files through Drake"]
    EFILE --> RESULT{"IRS / state acknowledgment"}
    RESULT -->|Accepted| STATUS["Record status and notify client"]
    RESULT -->|Rejected| FIX["Investigate, correct and resubmit as appropriate"]
    FIX --> RETURN
```
Important: This diagram combines implemented components and a target-state workflow. Drake import, AI extraction, and e-file integration are planned. Remote Form 8879 signing is not currently enabled; a compliant, approved authorization process is required before e-filing. IRS/state acknowledgments are received through the authorized filing workflow, not inferred by AI.
How the ATOZ AI assistants work
```mermaid
flowchart TD
    USER["Visitor or client question"] --> ROUTE{"Authenticated client session?"}
    ROUTE -->|No| PUBLIC["Public assistant"]
    PUBLIC --> SOURCES["Retrieve approved IRS / state knowledge"]
    ROUTE -->|Yes| AUTH["Verify live session and access rights"]
    AUTH -->|Authorized| CLIENT["Client assistant"]
    AUTH -->|Not authorized| DENY["No private data disclosure"]
    CLIENT --> DATA["Retrieve only permitted status / checklist information"]
    SOURCES --> MODEL["OpenAI Responses API"]
    DATA --> MODEL
    MODEL --> CHECK["Apply grounding, access and response checks"]
    CHECK --> ANSWER["Answer with appropriate sources / context"]
    CHECK -->|Needs professional judgment| HUMAN["Refer to tax professional"]
```
AI design: Public tax information and private client-specific facts have separate access paths. The authenticated assistant must not use a public identity match alone as permission to disclose sensitive records. Document-grounded historical Q&A and automated extraction remain expansion work.
Phase 1 — Secure operations foundation
Client experience
- Five-step guided tax intake, draft save/resume and validation
- Spouse and dependent information, annual agreement workflow
- Private document upload, return progress and support
Staff operations
- Preparer/reviewer/admin roles and assignment-aware authorization
- Review and prepared-return release design
- Client/source vs. prepared document separation
- Audit evidence and status tracking
Delivery qualification: Some core features are implemented and locally tested. Hosted intake draft saving is blocked pending a reviewed database fix; annual agreement templates are not yet published; certain release controls depend on unapplied migrations. This is not represented as a fully released production platform.
Phase 2 — AI-assisted tax operations
- Public assistant: General tax-process answers grounded in approved official knowledge.
- Authenticated client assistant: Client-specific status and checklist guidance behind authorization boundaries.
- Planned document intelligence: W-2 / 1099 extraction, classification, prior-year comparisons and missing-document detection.
- Planned Drake Tax integration: Reduce duplicate entry without replacing established preparation and e-file workflows.
- Human-in-the-loop design: Preparers review consequential outputs; AI does not independently finalize or file returns.
Drake Tax and IRS filing lifecycle — target state
▶ Watch the animated filing workflow

Open the interactive data-flow animation →

Explore the proposed sequence from client document submission to preparer review, Drake Tax, IRS/state acknowledgment and client status updates. The interactive demo includes colored animated arrows, moving indicators, and Play / Pause / Next / Reset / Speed controls.
Publishing note: The animation opens on GitHub Pages after you enable Pages for this repository. GitHub's README cannot run JavaScript, so the static diagram stays visible below while the button opens the interactive version. This is a proposed workflow, not a live Drake connector or automatic filing system.

```mermaid
sequenceDiagram
    autonumber
    participant Client
    participant Portal as ATOZ Portal
    participant Preparer
    participant Drake as Drake Tax
    participant IRS as IRS / State
    Client->>Portal: Submit intake and documents
    Portal->>Preparer: Make authorized case available
    opt Planned extraction / integration
        Portal-->>Preparer: Structured data and validation flags
        Preparer-->>Drake: Import or enter reviewed data
    end
    Preparer->>Drake: Prepare federal / state returns
    Drake-->>Preparer: Return calculations and diagnostics
    Preparer->>Portal: Publish reviewed return for client
    Portal-->>Client: Return available for review
    Client->>Preparer: Provide filing authorization via approved process
    Preparer->>Drake: Authorize and submit e-file
    Drake->>IRS: Transmit return
    IRS-->>Drake: Acceptance or rejection acknowledgment
    Drake-->>Preparer: Filing outcome
    Preparer->>Portal: Update verified filing status
    Portal-->>Client: Display status / next action
```
This is a proposed operational workflow, not a claim of an active automated Drake connector. Drake-supported import/export mechanisms and filing-status interfaces must be verified before implementation.
Mobile strategy — iOS and Android
Responsive web exists; dedicated native mobile apps are planned, not built. Both future apps would reuse central APIs, authentication, authorization and workflow logic. Planned features include camera document capture, intake progress, notifications, return tracking and authorized AI support.
Featured engineering decision
Allowing draft intake without weakening agreement requirements
Problem: The database blocked early draft saves because annual authorization checks were applied before the client reached the agreement step.

Design: Allow a live authenticated client to save their own draft while keeping final onboarding and staff processing agreement-gated. Preserve RLS, ownership, revoked-session protections and audit events.

Validation: Local security regression and workflow tests passed. The candidate fix has not been applied remotely; deployed baseline and recovery checks remain pending.

[Read the full case study →](docs/ENGINEERING_CASE_STUDY.md)
Engineering depth
Engineering concern	Design response
Client isolation	PostgreSQL Row Level Security and owner-aware server checks
Staff permissions	Role and assignment-based authorization; review boundaries
Document security	Private storage, separated source/prepared artifacts
AI access	Public vs. authenticated assistants; source-grounded responses
Quality	Focused automated tests, browser workflows, TypeScript, lint, production build
Change management	Candidate migrations, read-only preflight, explicit deployment authorization
Build vs. integrate	Plan to integrate with Drake Tax rather than rebuilding filing infrastructure


Portfolio guide
Document	What it demonstrates
[Architecture](docs/ARCHITECTURE.md)	Components, data boundaries, workflow and API patterns
[Security & Testing](docs/SECURITY_AND_TESTING.md)	Defense in depth, quality gates, test evidence
[Engineering Case Study](docs/ENGINEERING_CASE_STUDY.md)	Debugging, trade-offs, rollout discipline
[Delivery Roadmap](docs/ROADMAP.md)	Implemented vs. pending vs. planned features
[Application Summary](docs/APPLICATION_SUMMARY.md)	Recruiter-ready overview and interview talking points


<div align="center">

Build secure foundations. Automate responsibly. Keep professionals in control.

Architecture portfolio only. No taxpayer information, production secrets or internal deployment artifacts are included.
</div>
