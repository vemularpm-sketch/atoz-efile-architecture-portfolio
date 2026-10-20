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
[index.html](https://github.com/user-attachments/files/33743383/index.html)
<!DOCTYPE html><html lang="en"><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>ATOZ E-File | Animated Tax Filing Workflow</title><style>
:root{color-scheme:dark;--bg:#0b1220;--panel:#111c30;--muted:#91a5bf;--text:#f1f6ff;--line:#344660;--blue:#4f9cff;--teal:#23c8b2;--amber:#f8b84b;--purple:#a98cff;--green:#4bd39b}*{box-sizing:border-box}body{margin:0;background:radial-gradient(circle at 20% 0%,#1b3150,var(--bg) 48%);font:15px system-ui,-apple-system,Segoe UI,sans-serif;color:var(--text)}main{max-width:1240px;margin:auto;padding:26px 18px}h1{margin:0;font-size:clamp(24px,4vw,38px)}p{color:var(--muted);line-height:1.55}.head{display:flex;justify-content:space-between;gap:18px;flex-wrap:wrap;align-items:center}.tag{border:1px solid #35547c;color:#b8d7ff;border-radius:20px;padding:6px 12px;font-size:12px}.stage{background:var(--panel);border:1px solid #2d3e58;border-radius:18px;margin-top:20px;padding:14px;box-shadow:0 12px 30px #0004;overflow-x:auto}svg{width:100%;min-width:760px;height:auto;display:block}.lane{stroke:var(--line);stroke-width:1.5;stroke-dasharray:5 8}.node{fill:#182941;stroke:#466183;stroke-width:1.5}.nodeText{fill:#eaf4ff;font-size:14px;font-weight:650}.stepLine{stroke:#445b77;stroke-width:2.5;opacity:.45}.stepLine.done{opacity:.95}.stepLine.active{opacity:1;stroke-width:4;filter:drop-shadow(0 0 5px currentColor)}.stepText{fill:#a7b9d0;font-size:12px}.stepText.active{fill:#fff;font-weight:700}.num{fill:#213450;stroke:#547197;stroke-width:1}.numText{fill:#d9e9ff;font-size:11px}.packet{filter:drop-shadow(0 0 8px currentColor)}.controls{display:flex;align-items:center;gap:9px;flex-wrap:wrap;margin:14px 0}button,select{border:1px solid #436083;border-radius:9px;background:#203650;color:white;padding:10px 15px;font-weight:650;cursor:pointer}button:hover{background:#2c4b71}button:focus-visible,select:focus-visible{outline:3px solid var(--teal)}.status{border-left:4px solid var(--teal);padding:10px 14px;background:#15283d;border-radius:6px;flex:1;min-width:240px}.status small{display:block;color:#b7c8dc;margin-top:4px}.bar{height:7px;background:#263c57;border-radius:12px;overflow:hidden;margin:12px 0}.fill{height:100%;width:0;background:linear-gradient(90deg,var(--blue),var(--teal),var(--amber));transition:width .08s linear}.legend{display:flex;flex-wrap:wrap;gap:18px;font-size:13px;color:#b8cbe3}.swatch{display:inline-block;width:10px;height:10px;border-radius:50%;margin-right:6px}.note{font-size:12px}.sr{position:absolute;left:-9999px}@media(prefers-reduced-motion:reduce){.fill{transition:none}}
</style></head><body><main><div class="head"><div><h1>ATOZ E-File <span style="color:var(--teal)">· Animated Filing Flow</span></h1><p>Follow the proposed path from client intake to Drake Tax, IRS acknowledgement, and client notification.</p></div><span class="tag">Interactive architecture concept · Not a live filing integration</span></div><section class="stage" aria-label="Animated sequence diagram"><svg id="chart" viewBox="0 0 1100 1020" role="img" aria-label="Sequence flow between Client, ATOZ Portal, Preparer, Drake Tax and IRS or State"><defs><marker id="arrow" markerWidth="7" markerHeight="7" refX="6" refY="3.5" orient="auto"><path d="M0 0 L7 3.5 L0 7Z" fill="context-stroke"/></marker></defs><g id="lanes"></g><g id="steps"></g><g id="moving"></g></svg></section><div class="controls"><button id="play">▶ Play</button><button id="pause">Ⅱ Pause</button><button id="next">→ Next step</button><button id="reset">↺ Reset</button><label>Speed <select id="speed"><option value="0.5">0.5×</option><option value="1" selected>1×</option><option value="2">2×</option><option value="3">3×</option></select></label><div class="status" aria-live="polite"><strong id="current">Ready to play</strong><small id="detail">Press Play to see data move between systems.</small></div></div><div class="bar"><div class="fill" id="fill"></div></div><div class="legend"><span><i class="swatch" style="background:var(--blue)"></i>Client and portal</span><span><i class="swatch" style="background:var(--purple)"></i>Preparer review</span><span><i class="swatch" style="background:var(--amber)"></i>Planned Drake exchange</span><span><i class="swatch" style="background:var(--green)"></i>Filing response</span></div><p class="note"><strong>Scope:</strong> This is a proposed operational workflow. Drake import automation, AI extraction, native mobile apps and filing orchestration are not represented as live. Filing authorization must be obtained using an approved process; remote Form 8879 signing is not enabled in ATOZ. An authorized preparer submits through supported Drake workflows. Acceptance or rejection must be verified before displaying a filing outcome.</p><p class="note">This standalone HTML can be hosted with GitHub Pages. GitHub README files cannot execute embedded JavaScript; link to the hosted animation from your README.</p></main><script>
(()=>{const ns='http://www.w3.org/2000/svg';const xs=[90,320,550,780,1010],names=['Client','ATOZ Portal','Preparer','Drake Tax','IRS / State'];const colors=['#4f9cff','#23c8b2','#a98cff','#f8b84b','#4bd39b'];const steps=[
{a:0,b:1,t:'Submit intake and documents',c:0,d:'Client completes intake and uploads source documents.'},
{a:1,b:2,t:'Make authorized case available',c:1,d:'ATOZ applies identity, role and assignment checks.'},
{a:1,b:2,t:'AI extraction / validation (planned)',c:3,d:'Future AI may suggest structured data; preparer verifies it.'},
{a:2,b:3,t:'Enter or import reviewed data',c:3,d:'Drake integration is planned; supported manual entry remains an option.'},
{a:2,b:3,t:'Prepare federal and state returns',c:2,d:'The preparer works in Drake Tax.'},
{a:3,b:2,t:'Calculations and diagnostics',c:3,d:'Preparer checks Drake output and resolves issues.'},
{a:2,b:1,t:'Release reviewed return',c:2,d:'Only authorized staff release reviewed documents to the client.'},
{a:1,b:0,t:'Return available for client review',c:0,d:'Client reviews the released return.'},
{a:0,b:2,t:'Provide approved filing authorization',c:0,d:'Use an approved authorization process; remote Form 8879 signing is not enabled.'},
{a:2,b:3,t:'Authorized preparer submits e-file',c:3,d:'The preparer submits through supported Drake Tax filing workflows.'},
{a:3,b:4,t:'Transmit e-file to tax agency',c:3,d:'Drake handles transmission through its supported e-file process.'},
{a:4,b:3,t:'Acceptance / rejection acknowledgment',c:4,d:'IRS or state response is received and checked.'},
{a:3,b:2,t:'Verify filing outcome',c:4,d:'Preparer confirms status; rejections require correction and resubmission.'},
{a:2,b:1,t:'Update verified filing status',c:2,d:'Authorized staff update the ATOZ workflow with verified information.'},
{a:1,b:0,t:'Display status and next action',c:1,d:'Client sees a verified outcome and any required next step.'}
];const el=(tag,attrs,parent)=>{const e=document.createElementNS(ns,tag);Object.entries(attrs).forEach(([k,v])=>e.setAttribute(k,v));parent.append(e);return e};const lanes=document.getElementById('lanes'),group=document.getElementById('steps'),moving=document.getElementById('moving');xs.forEach((x,i)=>{el('rect',{x:x-79,y:18,width:158,height:54,rx:10,fill:'#182941',stroke:colors[i],'stroke-width':2},lanes);const text=el('text',{x,y:50,'text-anchor':'middle',class:'nodeText'},lanes);text.textContent=names[i];el('line',{x1:x,y1:82,x2:x,y2:976,class:'lane'},lanes);el('rect',{x:x-79,y:978,width:158,height:32,rx:7,fill:'#182941',stroke:colors[i]},lanes);const b=el('text',{x,y:999,'text-anchor':'middle',class:'nodeText','font-size':12},lanes);b.textContent=names[i]});const yAt=i=>120+i*56;const lines=[];steps.forEach((s,i)=>{const y=yAt(i),line=el('line',{x1:xs[s.a],y1:y,x2:xs[s.b],y2:y,class:'stepLine','marker-end':'url(#arrow)',stroke:colors[s.c]},group);lines.push(line);const mid=(xs[s.a]+xs[s.b])/2;const tx=el('text',{x:mid,y:y-12,'text-anchor':'middle',class:'stepText'},group);tx.textContent=s.t;const n=el('circle',{cx:xs[s.a],cy:y,r:11,class:'num'},group);const nt=el('text',{x:xs[s.a],y:y+4,'text-anchor':'middle',class:'numText'},group);nt.textContent=i+1});const dot=el('circle',{cx:xs[0],cy:yAt(0),r:9,fill:colors[0],class:'packet'},moving);let index=0,p=0,playing=false,last=0,frame=0;const play=document.getElementById('play'),status=document.getElementById('current'),detail=document.getElementById('detail'),fill=document.getElementById('fill'),speed=document.getElementById('speed');function draw(){lines.forEach((l,i)=>{l.classList.toggle('active',i===index);l.classList.toggle('done',i<index);l.setAttribute('stroke',colors[steps[i].c]);l.style.color=colors[steps[i].c]});const s=steps[index];dot.setAttribute('cx',xs[s.a]+(xs[s.b]-xs[s.a])*p);dot.setAttribute('cy',yAt(index));dot.setAttribute('fill',colors[s.c]);dot.style.color=colors[s.c];status.textContent=`Step ${index+1} of ${steps.length}: ${s.t}`;detail.textContent=s.d;fill.style.width=((index+p)/steps.length*100)+'%'}function tick(t){if(!playing)return;if(last)p+=(t-last)*Number(speed.value)/1750;last=t;if(p>=1){index++;p=0;if(index>=steps.length){index=steps.length-1;p=1;playing=false;draw();return}}draw();frame=requestAnimationFrame(tick)}play.onclick=()=>{if(index===steps.length-1&&p===1){index=0;p=0}if(!playing){playing=true;last=0;frame=requestAnimationFrame(tick)}};document.getElementById('pause').onclick=()=>{playing=false;cancelAnimationFrame(frame);last=0};document.getElementById('reset').onclick=()=>{playing=false;cancelAnimationFrame(frame);index=0;p=0;last=0;draw()};document.getElementById('next').onclick=()=>{playing=false;cancelAnimationFrame(frame);index=Math.min(steps.length-1,index+1);p=0;last=0;draw()};draw()})();
</script></body></html>


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

[Read the full case study →](ENGINEERING_CASE_STUDY.md)
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
[Architecture](ARCHITECTURE.md)	Components, data boundaries, workflow and API patterns
[Security & Testing](SECURITY_AND_TESTING.md)	Defense in depth, quality gates, test evidence
[Engineering Case Study](ENGINEERING_CASE_STUDY.md)	Debugging, trade-offs, rollout discipline
[Delivery Roadmap](ROADMAP.md)	Implemented vs. pending vs. planned features


<div align="center">

Build secure foundations. Automate responsibly. Keep professionals in control.

Architecture portfolio only. No taxpayer information, production secrets or internal deployment artifacts are included.
</div>
