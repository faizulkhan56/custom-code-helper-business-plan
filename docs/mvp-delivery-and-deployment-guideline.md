# MVP Delivery and Deployment Guideline

This document converts the business plan, MVP feature scope, and user journeys into a step-by-step delivery plan for the Custom Knowledge-Based Code Helper MVP.

The goal is to help the product, engineering, AI/RAG, DevOps, QA, and founder/GTM team work together toward a demo-ready SaaS MVP in an estimated **10-12 weeks**.

## 1. MVP Delivery Objective

Build a secure client-wise SaaS coding assistant where software firms can:

- Sign in to a tenant-specific workspace.
- Use chat for Python, PHP, .NET, backend, architecture, debugging, testing, and DevOps questions.
- Upload private documents and repository files.
- Retrieve answers from shared universal coding knowledge plus tenant-specific private knowledge.
- See source citations.
- Track token usage by tenant and individual user.
- Allow client admins to manage sources, package limits, and usage.
- Allow central SaaS admins to monitor client workspaces separately.
- Store billing-ready usage data for future Stripe integration.

The MVP is **not** an autonomous file-modifying agent. It can suggest code, explain changes, and generate snippets or tests, but developers apply changes manually. MCP-ready architecture can be considered, but actual agentic file modification, IDE automation, and PR automation belong to later phases.

## 2. MVP Feature Scope Baseline

The delivery plan must stay aligned with this MVP feature list.

| Feature | Delivery Meaning |
| --- | --- |
| User signup/login | Users can securely access the SaaS application. |
| Workspace/tenant | Each client has a separate workspace and tenant boundary. |
| Chat interface | Developers can ask questions and receive streaming AI answers. |
| Code-aware Markdown | Answers render code blocks clearly. |
| MVP language coverage | Python, PHP, and .NET are covered through prompts, parsing, RAG examples, and evaluation questions. |
| RAG ingestion | Client admins can upload docs and repository files. |
| Qdrant search | Retrieval uses universal coding knowledge plus tenant-specific private chunks. |
| Source citation | Answers show source files or document references. |
| Token tracking | Input/output tokens are tracked by user, tenant, model, and conversation. |
| Admin panel | Client admins view sources, package limits, tenant usage, and user-wise usage. |
| User usage view | Developers see only personal usage. |
| Central SaaS admin console | Provider admins monitor client workspaces, package usage, and billing operations. |
| Basic billing-ready data | Usage records are stored for manual billing and future Stripe integration. |
| Guardrails | Cross-tenant leakage is prevented by backend filters and role permissions. |
| Logs/monitoring | Basic observability exists for support, demos, and incident review. |

## 3. Team Structure and Ownership

### MVP Team

| Role | Primary Ownership | Secondary Responsibility |
| --- | --- | --- |
| Founder/Product Owner | Vision, scope, customer discovery, investor demo, pricing assumptions | Acceptance review, design partner coordination |
| Tech Lead | Architecture, delivery sequencing, code review, model strategy, technical decisions | Cross-team coordination, release readiness |
| Backend Engineer | Auth, tenant model, APIs, conversations, usage tracking, admin APIs | Security enforcement, data model, billing-ready records |
| Frontend Engineer | Chat UI, developer portal, client admin dashboard, central admin views | Responsive UX, error states, demo polish |
| AI/RAG Engineer | Ingestion, chunking, embeddings, Qdrant schema, retrieval, prompt templates, evaluation | Source citation quality, hallucination reduction |
| DevOps Engineer | Docker, environments, CI/CD, cloud deployment, monitoring, backups | Release automation, rollback, secrets handling |
| QA/Product Analyst | Test cases, user journey validation, regression checklist, demo validation | Acceptance criteria, bug triage, documentation checks |

### Decision Ownership

| Decision Area | Owner | Required Review |
| --- | --- | --- |
| MVP scope changes | Founder/Product Owner | Tech Lead |
| Architecture | Tech Lead | Backend, AI/RAG, DevOps |
| Tenant isolation | Tech Lead | Backend, DevOps, QA |
| RAG retrieval quality | AI/RAG Engineer | Product, QA |
| Deployment readiness | DevOps Engineer | Tech Lead, QA |
| Investor demo readiness | Founder/Product Owner | Tech Lead, QA |

## 4. Delivery Governance

### Weekly Operating Rhythm

| Meeting | Cadence | Owner | Output |
| --- | --- | --- | --- |
| Sprint planning | Every 2 weeks | Tech Lead + Product Owner | Sprint backlog and acceptance criteria |
| Daily standup | Daily | Tech Lead | Blockers, progress, next steps |
| Architecture sync | Weekly | Tech Lead | Technical decisions and dependency resolution |
| RAG quality review | Weekly from Week 4 | AI/RAG Engineer | Retrieval and answer-quality findings |
| QA triage | Twice weekly from Week 6 | QA/Product Analyst | Bug priority and release risks |
| Demo review | Weekly from Week 8 | Founder/Product Owner | Demo script and readiness gaps |
| Release readiness review | Week 10 onward | DevOps Engineer | Staging/prod checklist and go/no-go |

### Definition of Ready

A task should enter a sprint only when:

- The user role is clear.
- The tenant boundary requirement is known.
- Acceptance criteria are written.
- Required API/data dependencies are identified.
- Security or billing impact is understood.
- Test expectation is defined.

### Definition of Done

A task is done only when:

- Code is merged or document/artifact is approved.
- Basic tests or validation are complete.
- Tenant isolation is not weakened.
- Logs or error states exist where needed.
- The feature works in at least local or staging environment.
- User-facing behavior is reflected in demo notes or product documentation if relevant.

## 5. Environment Strategy

| Environment | Purpose | Owner | Timing |
| --- | --- | --- | --- |
| Local development | Individual developer work and integration testing | Each engineer | Week 1 |
| Shared development | Early API/UI integration | DevOps + Tech Lead | Week 2-3 |
| Staging | End-to-end QA, demo rehearsal, release validation | DevOps + QA | Week 6-8 |
| Production MVP | Investor/design partner demo and controlled beta | DevOps + Tech Lead | Week 10-12 |

### MVP Deployment Architecture

Recommended MVP deployment:

- Frontend: Vercel or Cloudflare Pages.
- Backend: Dockerized API container on ECS/Fargate, Render, Railway, or similar.
- PostgreSQL: managed database.
- Redis/queue: managed Redis or lightweight managed queue.
- Object storage: S3-compatible bucket.
- Vector DB: shared Qdrant Cloud or small managed cluster with tenant filters.
- LLM: hosted LLM API first, such as GPT-5.4 mini or equivalent.
- Monitoring: cloud logs plus basic metrics and alerts.

## 6. Week-by-Week MVP Delivery Plan

### Week 0-1: Product Discovery and Technical Foundation

Goal: lock scope, define roles, and prepare the engineering foundation.

| Team | Step-by-Step Work | Output |
| --- | --- | --- |
| Founder/Product Owner | Confirm target customer, package assumptions, MVP user roles, demo goal, and out-of-scope items. | Approved MVP scope and demo target |
| Tech Lead | Finalize high-level architecture, data boundaries, model strategy, and delivery sequence. | Architecture decision notes |
| Backend Engineer | Draft data model for users, tenants, roles, conversations, usage, sources, and billing records. | Initial schema proposal |
| Frontend Engineer | Wireframe developer chat, client admin, central SaaS admin, and usage screens. | Clickable or static wireframes |
| AI/RAG Engineer | Define ingestion formats, chunking strategy, metadata schema, and evaluation question categories. | RAG design note |
| DevOps Engineer | Create repo structure, Docker baseline, environment variable policy, CI skeleton, and deployment target plan. | DevOps foundation checklist |
| QA/Product Analyst | Convert MVP user stories and journeys into acceptance criteria and test scenarios. | MVP test plan draft |

Exit criteria:

- MVP feature list is frozen for the first build cycle.
- Team agrees that agentic file modification is out of MVP.
- Security principle is clear: no cross-tenant retrieval.

### Week 2-3: Auth, Tenant Model, Base UI, and Core APIs

Goal: establish the SaaS shell and tenant-aware application base.

| Team | Step-by-Step Work | Output |
| --- | --- | --- |
| Backend Engineer | Implement user signup/login integration, tenant/workspace model, role model, session/JWT handling, and base API structure. | Auth and tenant APIs |
| Frontend Engineer | Build login, workspace selector if needed, base layout, navigation, empty chat state, and role-aware menus. | Usable app shell |
| Tech Lead | Review auth and tenant logic, enforce role naming, and confirm database constraints. | Approved tenant model |
| DevOps Engineer | Set up CI checks, container build, development environment, and initial staging infrastructure plan. | CI and local deployment flow |
| QA/Product Analyst | Test login, role access, and tenant assignment cases. | Auth/tenant test report |
| Founder/Product Owner | Review UX flow and confirm that it matches investor/demo narrative. | UX feedback |

Exit criteria:

- Developer, client admin, and central SaaS admin can be represented in the system.
- Tenant ID is available to backend services for all future data access.
- Basic UI is ready for chat and admin modules.

### Week 4-5: Chat, Hosted LLM Integration, Usage Tracking, and Language Support

Goal: deliver the first working chat experience.

| Team | Step-by-Step Work | Output |
| --- | --- | --- |
| Backend Engineer | Build conversation API, message storage, streaming response endpoint, model call wrapper, and token logging. | Chat backend |
| Frontend Engineer | Build chat composer, streaming answer display, code-aware Markdown rendering, conversation history, and error states. | Developer chat UI |
| AI/RAG Engineer | Create prompt templates for Python, PHP, .NET, source-grounded answers, and refusal/uncertainty behavior. | MVP prompt pack |
| Tech Lead | Review model API abstraction so future model routing or self-hosting remains possible. | Model integration approval |
| DevOps Engineer | Add secrets handling for LLM API keys and basic runtime logging. | Secure config setup |
| QA/Product Analyst | Test chat flow, streaming behavior, code rendering, token logging, and basic language examples. | Chat validation report |

Exit criteria:

- A developer can ask a question and receive a streamed answer.
- Token usage is recorded by user, tenant, model, and conversation.
- Python, PHP, and .NET basic examples render correctly.

### Week 6-7: RAG Ingestion, Qdrant Retrieval, and Source Citations

Goal: connect uploaded knowledge to source-backed answers.

| Team | Step-by-Step Work | Output |
| --- | --- | --- |
| Backend Engineer | Build file upload APIs, source metadata APIs, ingestion job records, and source deletion endpoint. | Source management APIs |
| AI/RAG Engineer | Implement parsing, file filtering, code-aware chunking, embedding generation, Qdrant upsert, tenant metadata, and top-k retrieval. | Working RAG pipeline |
| Frontend Engineer | Build client admin source upload screen, source list, ingestion status, and delete action. | Source management UI |
| DevOps Engineer | Configure object storage, queue/worker runtime, Qdrant environment, and ingestion logs. | Ingestion infrastructure |
| Tech Lead | Review retrieval filters to ensure universal knowledge and tenant-specific knowledge are separated correctly. | RAG security review |
| QA/Product Analyst | Test upload, ingestion, retrieval, citation display, deletion, and no-cross-tenant retrieval. | RAG QA report |

Exit criteria:

- Client admin can upload docs/repo files.
- Assistant retrieves tenant-specific chunks plus universal coding knowledge.
- Answers include citations.
- Deleted sources no longer appear in retrieval.
- Cross-tenant retrieval tests pass.

### Week 8: Admin Panels, Usage Views, and Billing-Ready Records

Goal: make the SaaS workflow visible to client admins and provider admins.

| Team | Step-by-Step Work | Output |
| --- | --- | --- |
| Backend Engineer | Build tenant usage, user-wise usage, package limit fields, billing-ready usage queries, and central tenant monitoring APIs. | Admin/usage APIs |
| Frontend Engineer | Build client admin dashboard, user usage table, source summary, package usage card, personal usage view, and central SaaS admin overview. | Admin dashboards |
| DevOps Engineer | Add log aggregation and basic metrics for API, worker, ingestion status, and LLM errors. | Basic observability |
| QA/Product Analyst | Validate role access: developer personal usage, client admin own-tenant usage, central admin all-tenant summary. | Role validation report |
| Founder/Product Owner | Review admin screens for investor demo clarity and business narrative. | Demo feedback |

Exit criteria:

- Client admin can see tenant and user-wise usage.
- Developer can see only personal usage.
- Central SaaS admin can monitor clients separately.
- Billing-ready records can support manual invoice preparation.

### Week 9: Security, Guardrails, Evaluation, and Hardening

Goal: reduce demo risk and protect tenant boundaries.

| Team | Step-by-Step Work | Output |
| --- | --- | --- |
| Tech Lead | Run architecture review covering tenant isolation, data access, API permissions, secrets, and logging. | Security review notes |
| Backend Engineer | Add missing permission checks, rate/usage guardrails, validation, and error handling. | Hardened backend |
| AI/RAG Engineer | Build 50-100 question evaluation set across Python, PHP, .NET, private docs, and DevOps. Test answer quality and citations. | RAG evaluation report |
| Frontend Engineer | Improve empty states, loading states, failed upload handling, and admin error messages. | Polished MVP UI |
| DevOps Engineer | Configure staging backups, alert thresholds, environment separation, and release rollback notes. | Staging readiness |
| QA/Product Analyst | Run regression tests across user journeys and document critical bugs. | Regression report |

Exit criteria:

- Tenant isolation tests pass.
- RAG answer quality is acceptable for demo scenarios.
- Critical bugs are fixed or explicitly deferred.
- Staging environment is stable for demo rehearsal.

### Week 10: End-to-End QA, Demo Script, and Staging Release

Goal: make the product demo-ready.

| Team | Step-by-Step Work | Output |
| --- | --- | --- |
| Founder/Product Owner | Prepare 5-minute and 15-minute demo scripts for investor and design partner use. | Demo scripts |
| QA/Product Analyst | Execute end-to-end journeys for developer, client admin, central SaaS admin, support, and billing operator. | E2E test evidence |
| Frontend Engineer | Fix UI defects, responsive layout issues, and demo polish gaps. | Demo-ready UI |
| Backend Engineer | Fix API bugs, usage reporting issues, and edge cases from QA. | Stable backend |
| AI/RAG Engineer | Tune prompts, retrieval limits, citation formatting, and fallback behavior. | Improved answer quality |
| DevOps Engineer | Deploy staging release, document deployment steps, and verify logs/metrics. | Staging release candidate |
| Tech Lead | Run go/no-go review for production MVP deployment. | Release decision |

Exit criteria:

- Staging release passes all P0 journey tests.
- Demo data and demo workspace are prepared.
- Known limitations are documented.

### Week 11: Production MVP Deployment and Controlled Demo Readiness

Goal: deploy the MVP for investor/demo use and selected design partner testing.

| Team | Step-by-Step Work | Output |
| --- | --- | --- |
| DevOps Engineer | Deploy production MVP, configure DNS/domain if needed, verify environment variables, backups, logs, and rollback path. | Production MVP |
| Backend Engineer | Verify production database migrations, tenant seed data, central admin account, and usage tracking. | Production backend validation |
| Frontend Engineer | Verify production frontend routes, auth, chat, admin dashboards, and responsive behavior. | Production frontend validation |
| AI/RAG Engineer | Upload demo knowledge base, verify embeddings, citations, and retrieval quality in production. | Production RAG validation |
| QA/Product Analyst | Run smoke tests across all critical journeys. | Production smoke test report |
| Founder/Product Owner | Run investor demo rehearsal and design partner onboarding rehearsal. | Demo readiness signoff |

Exit criteria:

- Production MVP is usable for controlled demos.
- Critical monitoring exists.
- Rollback path is documented.
- Demo script works against production or staging reliably.

### Week 12: Buffer, Pilot Preparation, and Investor Package

Goal: stabilize, document, and prepare for private beta.

| Team | Step-by-Step Work | Output |
| --- | --- | --- |
| Founder/Product Owner | Finalize investor narrative, pricing assumptions, pilot offer, and design partner list. | Investor/demo package |
| Tech Lead | Document architecture, technical limitations, and Phase 1 recommendations. | Technical handoff note |
| Backend Engineer | Close remaining P0/P1 bugs and document API/data model decisions. | Backend handoff |
| Frontend Engineer | Close UI polish issues and prepare demo screenshots if needed. | UI handoff |
| AI/RAG Engineer | Document evaluation results, prompt versions, known retrieval limits, and next improvements. | RAG handoff |
| DevOps Engineer | Finalize runbook, backup notes, monitoring guide, and deployment checklist. | Operations runbook |
| QA/Product Analyst | Create final MVP test report and known issue list. | QA signoff package |

Exit criteria:

- MVP is ready for investor demo and 3-5 controlled test teams.
- Known risks and limitations are documented.
- Phase 1 backlog is ready to refine.

## 7. Cross-Team Dependency Map

| Dependency | Owner | Needed By | Timing |
| --- | --- | --- | --- |
| Tenant model | Backend Engineer | Frontend, AI/RAG, QA, central admin | Week 2 |
| Role permissions | Backend Engineer | Frontend, QA, security review | Week 3 |
| Chat API | Backend Engineer | Frontend, AI/RAG, QA | Week 4 |
| Model wrapper | Backend Engineer + AI/RAG Engineer | Chat, token tracking, prompt testing | Week 4 |
| Upload/source schema | Backend Engineer | Frontend, AI/RAG, DevOps | Week 6 |
| Qdrant metadata schema | AI/RAG Engineer | Backend, QA, security review | Week 6 |
| Object storage and worker | DevOps Engineer | AI/RAG ingestion, backend APIs | Week 6 |
| Usage schema | Backend Engineer | Admin dashboards, billing operator journey | Week 8 |
| Central admin APIs | Backend Engineer | Frontend, support/billing journeys | Week 8 |
| Staging deployment | DevOps Engineer | QA, demo rehearsal | Week 8-10 |
| Evaluation dataset | AI/RAG Engineer + QA | Demo readiness, hallucination risk control | Week 9 |

## 8. Workstream Detail

### Product and UX Workstream

Step-by-step process:

1. Confirm MVP personas: developer, client admin, central SaaS admin, support, billing operator, founder/demo user, investor reviewer.
2. Confirm primary journeys from `docs/mvp-user-journeys.md`.
3. Translate journeys into screens: login, chat, source management, admin usage, central admin overview, personal usage.
4. Define package limit display and usage terminology.
5. Prepare demo scripts for investor and design partner versions.
6. Review every sprint output against user journey success outcomes.

Key deliverables:

- MVP journey map.
- Screen wireframes.
- Demo script.
- Acceptance criteria.
- Investor demo data plan.

### Backend Workstream

Step-by-step process:

1. Design core schema: users, tenants, memberships, roles, conversations, messages, sources, ingestion jobs, usage records, package limits.
2. Implement authentication and tenant resolution.
3. Enforce backend tenant filters on every tenant-owned resource.
4. Build chat and conversation APIs.
5. Integrate hosted LLM API through an abstraction layer.
6. Record usage for every LLM request.
7. Build file/source APIs and ingestion job records.
8. Build admin APIs for client admins and central SaaS admins.
9. Build billing-ready usage queries.
10. Add validation, error handling, and audit-friendly logs.

Key deliverables:

- Tenant-safe API.
- Chat/conversation service.
- Usage tracking service.
- Admin and central admin APIs.
- Billing-ready records.

### Frontend Workstream

Step-by-step process:

1. Build base app layout and role-aware navigation.
2. Build login and workspace entry experience.
3. Build developer chat UI with streaming responses.
4. Render code-aware Markdown and citations.
5. Build user personal usage view.
6. Build client admin source upload and source list screens.
7. Build client admin tenant usage and user-wise usage dashboard.
8. Build central SaaS admin overview for separate client monitoring.
9. Add loading, empty, error, and retry states.
10. Polish UI for investor and design partner demo.

Key deliverables:

- Developer portal.
- Client admin dashboard.
- Central SaaS admin console.
- Demo-ready responsive UI.

### AI/RAG Workstream

Step-by-step process:

1. Define supported file types for MVP.
2. Build file parsing and code-aware chunking for Python, PHP, .NET, Markdown, text, and common docs.
3. Add metadata tagging: tenant, workspace, source, file path, language, user, chunk ID.
4. Generate embeddings and store vectors in shared Qdrant with tenant filters.
5. Create universal coding knowledge collection.
6. Implement retrieval over universal knowledge plus tenant-specific private chunks.
7. Build prompt template for source-grounded answers.
8. Add citation formatting.
9. Create evaluation set of 50-100 questions.
10. Tune retrieval count, prompt instructions, and fallback behavior.

Key deliverables:

- Ingestion pipeline.
- Qdrant schema and retrieval logic.
- Prompt templates.
- Evaluation report.
- Citation quality review.

### DevOps Workstream

Step-by-step process:

1. Define local development setup.
2. Dockerize backend and worker services.
3. Configure frontend deployment.
4. Configure managed PostgreSQL, Redis/queue, object storage, and Qdrant.
5. Set up CI/CD checks and deployment path.
6. Create development, staging, and production environments.
7. Set secrets policy and environment variable handling.
8. Add logging, metrics, and alert basics.
9. Configure backups for PostgreSQL and object storage.
10. Document rollback and incident response basics.

Key deliverables:

- CI/CD pipeline.
- Staging environment.
- Production MVP deployment.
- Monitoring and backup baseline.
- Deployment runbook.

### QA and Product Analysis Workstream

Step-by-step process:

1. Convert MVP features into acceptance criteria.
2. Build test cases by persona and journey.
3. Test role permissions and tenant isolation.
4. Test chat, streaming, citations, and usage tracking.
5. Test upload, ingestion, deletion, and retrieval.
6. Test admin dashboards and central admin console.
7. Run cross-browser and responsive UI checks.
8. Run staging regression before production deployment.
9. Maintain known issue list and release risk summary.
10. Validate investor demo script against actual product behavior.

Key deliverables:

- Test plan.
- Regression checklist.
- Tenant isolation test report.
- Demo validation report.
- MVP QA signoff.

## 9. Deployment Runbook

### Pre-Deployment Checklist

Before deploying staging or production:

- Code merged and reviewed.
- Database migrations reviewed.
- Environment variables configured.
- LLM API key stored securely.
- Qdrant endpoint and collection names configured.
- Object storage bucket configured.
- Redis/queue configured.
- Seed admin account or admin creation process ready.
- Logging enabled.
- Backup policy confirmed.
- Smoke test checklist prepared.

### Staging Deployment Steps

1. Build backend container.
2. Build ingestion worker container.
3. Deploy database migrations to staging.
4. Deploy backend API to staging.
5. Deploy worker to staging.
6. Deploy frontend to staging.
7. Configure staging environment variables.
8. Upload sample knowledge base.
9. Run smoke tests.
10. Run role and tenant-isolation tests.
11. Run demo script against staging.

### Production MVP Deployment Steps

1. Confirm go/no-go approval from Product Owner, Tech Lead, QA, and DevOps.
2. Freeze release candidate.
3. Backup current production database if production already exists.
4. Apply production database migrations.
5. Deploy backend API.
6. Deploy ingestion worker.
7. Deploy frontend.
8. Verify LLM, Qdrant, PostgreSQL, Redis, and object storage connectivity.
9. Create or verify central SaaS admin account.
10. Create demo tenant and demo users.
11. Upload demo knowledge base.
12. Run production smoke test.
13. Verify logs, metrics, and usage tracking.
14. Mark release ready for controlled demo.

### Rollback Guidelines

Rollback should be possible when:

- The frontend deploy breaks primary navigation or chat.
- Backend deploy breaks auth, tenant access, chat, or admin APIs.
- Migration causes data access failure.
- Ingestion worker corrupts source metadata or retrieval.

Minimum rollback preparation:

- Keep previous frontend build available.
- Keep previous backend container image available.
- Run reversible or backup-protected database migrations.
- Keep backup before risky migrations.
- Document manual disable switches for ingestion or external LLM calls if needed.

## 10. Security and Tenant-Isolation Checklist

| Area | Required Control |
| --- | --- |
| Tenant data access | Every tenant-owned API query must filter by tenant/workspace. |
| Qdrant retrieval | Retrieval must include tenant metadata filter for private chunks. |
| Universal knowledge | Shared universal knowledge can be retrieved by all tenants. |
| Client private knowledge | Private chunks cannot be retrieved by other tenants. |
| Client admin | Can view only own tenant sources and usage. |
| Developer | Can view only own usage and authorized tenant chat context. |
| Central SaaS admin | Can view operational metadata and usage summaries, not private retrieval by default. |
| Logs | Logs must avoid exposing secrets or unnecessary source content. |
| Uploads | Secret filtering should detect common secret patterns in uploaded files. |
| Deletion | Deleted sources must be removed from retrieval. |

## 11. MVP Testing Matrix

| Test Area | Minimum Test |
| --- | --- |
| Auth | Login, logout, invalid login, session expiry. |
| Tenant isolation | User from tenant A cannot access tenant B sources, conversations, usage, or retrieval chunks. |
| Chat | Ask question, stream answer, save conversation, render code. |
| Language support | Python, PHP, and .NET example questions. |
| RAG ingestion | Upload, parse, chunk, embed, store, retrieve. |
| Source citation | Answer includes source references. |
| Source deletion | Deleted source is no longer retrieved. |
| Usage tracking | Input/output tokens stored with user, tenant, model, conversation. |
| Client admin | Source list, usage summary, user-wise usage, package limit display. |
| Central admin | Tenant list, usage summary, ingestion status, billing-ready records. |
| Guardrails | Cross-tenant access blocked. |
| Monitoring | API error, worker error, and LLM error visible in logs. |

## 12. MVP Demo Readiness Checklist

The MVP is demo-ready when:

- A demo tenant exists.
- Demo developer, client admin, and central SaaS admin accounts exist.
- Demo knowledge base includes safe Python, PHP, .NET, architecture, and DevOps examples.
- Developer can ask questions and receive cited answers.
- Client admin can upload, view, and delete sources.
- Client admin can view tenant usage and user-wise usage.
- Developer can view only personal usage.
- Central SaaS admin can monitor client workspaces separately.
- Token usage appears in billing-ready records.
- Logs and monitoring show basic operational health.
- Demo script can be completed in 5 minutes and 15 minutes.
- Known limitations are documented.

## 13. Risks and Mitigation During Delivery

| Risk | Delivery Mitigation |
| --- | --- |
| MVP scope creep | Freeze P0 scope in Week 1 and defer Phase 1/2 items. |
| RAG quality is weak | Build evaluation questions early and tune retrieval weekly. |
| LLM cost surprise | Track token usage from first chat integration. |
| Tenant leakage | Write explicit isolation tests and review every data query. |
| Ingestion delays | Start with ZIP/file upload before live GitHub connector. |
| UI polish delays | Build simple workflows first, then polish demo paths. |
| Deployment instability | Use staging from Week 6-8 and production only after smoke tests. |
| Investor demo mismatch | Rehearse with actual product, not only slides. |

## 14. Phase 1 Handoff Backlog

These are important, but should not block MVP unless explicitly reprioritized:

- GitHub repository connector.
- Stripe subscription and usage billing.
- Team invite and improved role management.
- Advanced RAG with hybrid search and metadata filtering.
- Model routing across cheaper and stronger models.
- Feedback button and answer quality analytics.
- Secret detection improvements.
- Controlled read-only MCP integrations.
- Audit logs beyond basic operational logs.

Phase 2 candidates:

- IDE extension.
- PR review automation.
- Agentic patch generation or file modification.
- Self-hosted LLM serving.
- Fine-tuning.
- Enterprise SSO.
- Private deployment.

## 15. Final Delivery Recommendation

The team should treat the MVP as a **complete but narrow SaaS workflow**:

1. Client signs in.
2. Client admin uploads private knowledge.
3. Developer asks coding questions.
4. RAG retrieves universal plus tenant-specific context.
5. Hosted LLM generates source-backed answers.
6. Usage is tracked by user and tenant.
7. Client admin and central SaaS admin can monitor usage.
8. Provider can demo, support, and prepare billing.

This keeps the MVP realistic, investor-ready, and technically safe while leaving a clear upgrade path toward MCP integrations, model routing, self-hosting, IDE extensions, and agentic workflows in later phases.
