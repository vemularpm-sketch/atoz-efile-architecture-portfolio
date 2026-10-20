<div align="center">

# ATOZ E-File
### Secure Tax Operations · AI-Assisted Workflows · Human Oversight

**A full-stack and platform architecture case study for modern tax preparation**

![Status](https://img.shields.io/badge/Status-Active_Development-2563eb?style=for-the-badge) ![Architecture](https://img.shields.io/badge/Architecture-API_First-0f766e?style=for-the-badge) ![Security](https://img.shields.io/badge/Design-Security_First-334155?style=for-the-badge)

[Project Website](https://atozefile.com) · [Architecture](docs/ARCHITECTURE.md) · [Security & Quality](docs/SECURITY_AND_TESTING.md) · [Engineering Case Study](docs/ENGINEERING_CASE_STUDY.md) · [AI & Mobile Roadmap](docs/ROADMAP.md)

</div>

---

> **Problem:** Tax firms coordinate sensitive documents, client onboarding, preparer/reviewer assignments, and repetitive information entry across disconnected workflows.
>
> **Approach:** Establish a secure, auditable workflow foundation, then introduce grounded AI and tax-software integrations with human review.

## At a glance

| | |
|---|---|
| **Contribution** | Solution architecture, full-stack implementation, database authorization, AI design, testing and release controls |
| **Technology** | Next.js 16 · React · TypeScript · PostgreSQL · Supabase Auth / Storage · OpenAI Responses API |
| **Users** | Tax clients · Preparers · Reviewers · Administrators |
| **Current delivery** | Active development; some features locally implemented and tested, others pending release |
| **Future expansion** | Document intelligence · Drake Tax integration · iOS and Android |

## Platform architecture

```mermaid
flowchart TB
  subgraph Clients[Experience]
    WEB[Responsive client portal]
    STAFF[Staff and admin portal]
    MOBILE[iOS / Android - planned]
  end
  subgraph App[Application layer]
    API[Next.js server APIs]
    AUTH[Session and role checks]
    FLOW[Intake / document / return workflows]
    AI[AI orchestration]
  end
  subgraph Data[Protected data]
    DB[(PostgreSQL + RLS)]
    STORAGE[(Private object storage)]
    AUDIT[Audit logs]
  end
  subgraph External[Knowledge and integration]
    IRS[Approved IRS / state sources]
    DOC[Document extraction - planned]
    DRAKE[Drake integration - planned]
  end
  WEB --> API
  STAFF --> API
  MOBILE -.-> API
  API --> AUTH
  API --> FLOW
  API --> AI
  FLOW --> DB
  FLOW --> STORAGE
  FLOW --> AUDIT
  AI --> IRS
  AI -.-> DOC
  API -.-> DRAKE
```

*Conceptual diagram. Dashed edges indicate planned work, not shipped integrations.*

## Phase 1 — Secure operations foundation

<table><tr><td width="50%" valign="top">

### Client experience
- Five-step guided tax intake
- Draft save/resume and validation
- Spouse and dependent information
- Annual agreement workflow
- Private document upload
- Return progress and support

</td><td width="50%" valign="top">

### Staff operations
- Preparer/reviewer/admin roles
- Assignment-aware authorization
- Review and prepared-return release design
- Client/source vs. prepared document separation
- Audit evidence and status tracking

</td></tr></table>

**Delivery qualification:** Some core features are implemented and locally tested. Hosted intake draft saving is blocked pending a reviewed database fix; annual agreement templates are not yet published; certain release controls depend on unapplied migrations. This is not represented as a fully released production platform.

## Phase 2 — AI-assisted tax operations

- **Public assistant:** General tax-process answers grounded in approved official knowledge.
- **Authenticated client assistant:** Client-specific status and checklist guidance behind authorization boundaries.
- **Planned document intelligence:** W-2 / 1099 extraction, classification, prior-year comparisons, and missing-document detection.
- **Planned Drake Tax integration:** Reduce duplicate entry without replacing established preparation and e-file workflows.
- **Human-in-the-loop design:** Preparers review consequential outputs; AI does not independently finalize or file returns.

## Mobile strategy — iOS and Android

**Responsive web exists; dedicated native mobile apps are planned, not built.** Both future apps would reuse central APIs, authentication, authorization, and workflow logic. Planned features include camera document capture, intake progress, notifications, return tracking, and authorized AI support.

## Featured engineering decision

### Allowing draft intake without weakening agreement requirements

**Problem:** The database blocked early draft saves because annual authorization checks were applied before the client reached the agreement step.

**Design:** Allow a live authenticated client to save *their own draft* while keeping final onboarding and staff processing agreement-gated. Preserve RLS, ownership, revoked-session protections, and audit events.

**Validation:** Local security regression and workflow tests passed. The candidate fix has **not** been applied remotely; deployed baseline and recovery checks remain pending.

[Read the full case study →](docs/ENGINEERING_CASE_STUDY.md)

## Engineering depth

| Engineering concern | Design response |
|---|---|
| Client isolation | PostgreSQL Row Level Security and owner-aware server checks |
| Staff permissions | Role and assignment-based authorization; review boundaries |
| Document security | Private storage, separated source/prepared artifacts |
| AI access | Public vs. authenticated assistants; source-grounded responses |
| Quality | Focused automated tests, browser workflows, TypeScript, lint, production build |
| Change management | Candidate migrations, read-only preflight, explicit deployment authorization |
| Build vs. integrate | Integrate with Drake Tax rather than rebuilding tax filing infrastructure |

## Portfolio guide

| Document | What it demonstrates |
|---|---|
| [Architecture](docs/ARCHITECTURE.md) | Components, data boundaries, workflow and API patterns |
| [Security & Testing](docs/SECURITY_AND_TESTING.md) | Defense in depth, quality gates, test evidence |
| [Engineering Case Study](docs/ENGINEERING_CASE_STUDY.md) | Debugging, trade-offs, rollout discipline |
| [Delivery Roadmap](docs/ROADMAP.md) | Implemented vs. pending vs. planned features |
| [Application Summary](docs/APPLICATION_SUMMARY.md) | Recruiter-ready overview and interview talking points |

---

<div align="center">

**Build secure foundations. Automate responsibly. Keep professionals in control.**

*Architecture portfolio only. No taxpayer information, production secrets, or internal deployment artifacts are included.*

</div>
