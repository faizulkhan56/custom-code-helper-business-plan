# Custom Knowledge-Based Code Helper: Business and Technical Plan

## Table of Contents

- [1. Executive Summary](#1-executive-summary)
- [2. Business Case and Feasibility](#2-business-case-and-feasibility)
- [3. Product Footprint](#3-product-footprint)
- [4. Technical Architecture](#4-technical-architecture)
- [5. Architecture Diagram](#5-architecture-diagram)
- [6. CPU, GPU, and RAG Database Logic](#6-cpu-gpu-and-rag-database-logic)
- [7. Hardware Resource Plan](#7-hardware-resource-plan)
- [8. Model Strategy](#8-model-strategy)
- [9. CapEx and OpEx](#9-capex-and-opex)
- [10. MVP Budget Estimate](#10-mvp-budget-estimate)
- [11. Phase 1 Budget Estimate](#11-phase-1-budget-estimate)
- [12. Phase 2 Budget Estimate](#12-phase-2-budget-estimate)
- [13. Revenue Model](#13-revenue-model)
- [14. MVP Feature Scope](#14-mvp-feature-scope)
- [15. MVP Deployment Plan](#15-mvp-deployment-plan)
- [16. MVP Timeline](#16-mvp-timeline)
- [17. MVP User Stories](#17-mvp-user-stories)
- [18. Phase 1 Plan](#18-phase-1-plan)
- [19. Phase 2 Plan](#19-phase-2-plan)
- [20. Go-To-Market Plan](#20-go-to-market-plan)
- [21. Team Structure](#21-team-structure)
- [22. Organization Chart for Phase 1](#22-organization-chart-for-phase-1)
- [23. Detailed Work Plan](#23-detailed-work-plan)
- [24. Investor-Focused Milestones](#24-investor-focused-milestones)
- [25. Key Risks and Mitigation](#25-key-risks-and-mitigation)
- [26. Recommended MVP Tech Stack](#26-recommended-mvp-tech-stack)
- [27. MVP Feature Backlog](#27-mvp-feature-backlog)
- [28. Suggested Investor Ask](#28-suggested-investor-ask)
- [29. Final Investor Narrative](#29-final-investor-narrative)
- [30. Final Recommendation](#30-final-recommendation)

## 1. Executive Summary

### Product Name Options

- **CodeMind SaaS**
- **DevAssist AI**

### One-Line Pitch

A secure AI coding assistant for engineering teams that understands their private codebase, official documentation, internal standards, and DevOps workflows, then provides accurate code help through chat.

### Investor Pitch

Modern engineering teams waste significant time searching documentation, debugging code, onboarding developers, reviewing pull requests, and understanding legacy systems. General AI chatbots are useful, but they do not deeply understand a company's private repositories, internal standards, framework versions, or operational context.

This product solves that gap by offering a custom knowledge-based code helper where each client can connect their own code repositories, documentation, runbooks, tickets, and engineering standards. The AI assistant answers from that private knowledge base using retrieval augmented generation (RAG), with optional Model Context Protocol (MCP) and tool integrations for GitHub, CI logs, documentation lookup, and controlled external access.

## 2. Business Case and Feasibility

### Problem

Engineering teams face recurring productivity and quality challenges:

| Pain Point | Business Impact |
| --- | --- |
| Developers spend time searching documentation | Productivity loss |
| New team members struggle with codebase onboarding | Longer ramp-up time |
| Senior engineers answer repeated questions | High-value engineering time wasted |
| Debugging repeated errors is slow | Delayed delivery |
| Internal code standards are not always followed | Quality and security risk |
| Generic AI chatbots do not know private repositories | Limited usefulness |
| Ungoverned AI tools may leak code or sensitive context | Security concern |

### Solution

The product is a private SaaS code assistant that can answer questions such as:

- How does this service work?
- Why is this Python error happening?
- Where is authentication handled in this repository?
- Generate a `pytest` test for this function.
- Review this FastAPI endpoint.
- Explain this Kubernetes deployment file.
- What changed in this pull request?
- How do we follow our internal coding standard here?

### Target Customers

| Segment | Need |
| --- | --- |
| Small software companies | Affordable AI code helper |
| DevOps and MLOps teams | Infrastructure, Python, and automation support |
| SaaS product teams | Private repository Q&A |
| Training companies | Student coding assistant |
| Enterprise engineering teams | Secure internal knowledge assistant |
| Government and private-sector organizations | Air-gapped or private deployment in later phases |

### Why It Is Feasible

The product does not need to start by training a 70B or 79B parameter model from scratch. The MVP can be built with:

- Existing hosted LLM APIs or hosted code models
- RAG using Qdrant
- Private repository and document ingestion
- Token metering
- SaaS dashboard and admin workflows

Usage-based billing is a common SaaS pricing pattern where customers are charged according to actual consumption. Stripe supports usage-based billing, which makes token-based SaaS packaging practical.

## 3. Product Footprint

### Core Product

**Custom Knowledge-Based Code Helper**

### Main Capabilities

| Area | Feature |
| --- | --- |
| Chat | Developers ask coding questions in a conversational interface |
| RAG | Answers are grounded in private repositories and documents |
| Code understanding | Explains files, functions, services, errors, and architecture |
| Debugging | Suggests fixes for tracebacks, logs, and failing workflows |
| Test generation | Generates `pytest` and unit test examples |
| Code review | Reviews snippets or pull request changes |
| Docs Q&A | Answers from official and client-specific documentation |
| Tenant isolation | Keeps each client knowledge base separate |
| Billing | Tracks token usage for SaaS billing |
| Admin portal | Manages users, repositories, sources, and usage |
| MCP/tools | Provides controlled GitHub, documentation, and CI access |

## 4. Technical Architecture

### Important Design Principle

The LLM should not be treated as the only knowledge source. The LLM is the reasoning and answer generation engine, while the customer's knowledge base is retrieved from dedicated storage and search systems.

### Correct System Structure

| Component | Purpose |
| --- | --- |
| LLM | Reasoning and answer generation engine |
| Qdrant/vector DB | RAG knowledge retrieval layer |
| PostgreSQL | Users, tenants, billing, metadata, and conversations |
| Object storage | Uploaded documents, repository snapshots, and source files |
| Redis/queue | Async ingestion and background jobs |
| MCP/tools | Controlled external integrations |
| Billing engine | Token usage, plan limits, and invoices |

Qdrant is suitable for vector similarity search and semantic retrieval. Qdrant Cloud can support early experimentation with a free tier before scaling to paid clusters. Actual production cost should be verified against the selected region, usage pattern, and service plan before investor submission.

## 5. Architecture Diagram

Diagram source file: `../diagrams/01-product-architecture.mmd`

```mermaid
flowchart LR
    User[Developer or Admin] --> UI[Web Chat UI]
    UI --> API[Backend API]
    API --> Auth[Auth and Tenant Guard]
    API --> Chat[Chat Orchestrator]
    Chat --> Retrieve[RAG Retrieval]
    Retrieve --> Qdrant[Qdrant Vector DB]
    Retrieve --> Postgres[PostgreSQL Metadata]
    Chat --> LLM[Hosted LLM API]
    API --> Usage[Token Usage Metering]
    API --> Storage[Object Storage]
    API --> Queue[Redis Queue]
    Queue --> Worker[Ingestion Worker]
    Worker --> Storage
    Worker --> Embed[Embedding Service]
    Embed --> Qdrant
    Chat --> Tools[MCP Tools]
    Tools --> GitHub[GitHub]
    Tools --> CI[CI Logs]
    Tools --> Docs[Docs Lookup]
```

### Ingestion Pipeline

Diagram source file: `../diagrams/02-ingestion-pipeline.mmd`

```mermaid
flowchart TD
    Upload[Upload Docs or Repo ZIP] --> Store[Object Storage]
    Store --> Queue[Redis Queue]
    Queue --> Parse[Parse Files]
    Parse --> Filter[Secret and File Filtering]
    Filter --> Chunk[Code Aware Chunking]
    Chunk --> Embed[Create Embeddings]
    Embed --> Index[Store Vectors in Qdrant]
    Chunk --> Meta[Store Metadata in PostgreSQL]
    Index --> Ready[Knowledge Ready for Chat]
    Meta --> Ready
```

## 6. CPU, GPU, and RAG Database Logic

### What Runs on CPU?

CPU resources are enough for:

- Frontend hosting
- Backend API
- Authentication
- Billing
- PostgreSQL
- Redis
- Qdrant small and medium workloads
- Document ingestion
- Chunking
- Metadata processing
- Admin dashboard

### What Needs GPU?

GPU is mainly needed if the company self-hosts the LLM or runs large-scale embedding workloads.

GPU is useful for:

- LLM inference
- Embedding generation at scale
- Batch processing large ingestion jobs
- Optional fine-tuning in later phases

For the MVP, GPU can be avoided by using a hosted LLM API. This keeps CapEx low and reduces infrastructure complexity.

### Best MVP Approach

For MVP:

- Use an API-based LLM
- Use Qdrant Cloud or a small self-hosted Qdrant deployment
- Use CPU servers for backend services
- Use managed PostgreSQL

This approach is cheaper, faster, and investor-friendly.

### Best Phase 1 Approach

For Phase 1, use a hybrid model:

- API LLM for best answer quality
- Optional self-hosted open-source code model for cost control
- Qdrant production cluster
- Separate ingestion workers

### Best Phase 2 Approach

For Phase 2:

- Self-host model serving using vLLM
- Add GPU autoscaling
- Support dedicated tenant isolation
- Offer enterprise or private deployment
- Consider optional fine-tuned models

vLLM is a strong option for self-hosted LLM serving because it supports OpenAI-compatible serving, automatic prefix caching, LoRA support, and online serving features. Its project documentation highlights PagedAttention and continuous batching for improved throughput and GPU utilization.

## 7. Hardware Resource Plan

### MVP Hardware

Recommended MVP architecture:

- Frontend: Vercel or Cloudflare Pages
- Backend: 1-2 CPU VMs or container service
- Database: Managed PostgreSQL
- Vector DB: Qdrant Cloud free or small paid tier
- LLM: Hosted API
- Object storage: S3-compatible bucket
- Queue: Redis or managed queue

### MVP Compute Requirement

| Component | Estimated Resource |
| --- | --- |
| Backend API | 2-4 vCPU, 8-16 GB RAM |
| Worker | 2-4 vCPU, 8-16 GB RAM |
| PostgreSQL | 2 vCPU, 4-8 GB RAM |
| Qdrant | Free or small managed cluster |
| Redis | 1-2 GB RAM |
| GPU | Not required |

### Phase 1 Hardware

| Component | Estimated Resource |
| --- | --- |
| Backend | 2-3 replicas, 4 vCPU and 16 GB RAM each |
| Workers | 2-4 workers, 4-8 vCPU each |
| PostgreSQL | Managed medium instance |
| Qdrant | Dedicated cluster |
| Redis | Managed Redis |
| LLM | API plus optional small self-hosted model |
| GPU | Optional NVIDIA L4 class |

AWS G6 instances use NVIDIA L4 GPUs. AWS states G6 can scale up to 8 NVIDIA L4 Tensor Core GPUs with 24 GB memory per GPU and are designed for machine learning and graphics workloads. NVIDIA lists the L4 GPU with 24 GB GPU memory and 300 GB/s memory bandwidth. These specifications should be verified before final infrastructure purchase.

### Phase 2 Hardware

| Component | Estimated Resource |
| --- | --- |
| LLM serving | GPU nodes with L4, A10, or H100 depending on model and workload |
| RAG DB | Highly available Qdrant cluster |
| Backend | Kubernetes autoscaling |
| Ingestion | Separate worker pool |
| Fine-tuning | Dedicated GPU jobs |
| Enterprise | Single-tenant deployment option |

## 8. Model Strategy

### MVP Model Strategy

Use a hosted model first.

Reasons:

- No GPU management
- Lower engineering complexity
- Faster MVP launch
- Better quality from day one
- Easier token metering

Example cost logic: public pricing for major LLM providers changes frequently and should be verified immediately before investor submission. For cost-sensitive workloads, model routing is important: simple questions can go to a cheaper model, while complex code debugging or architecture tasks can go to a stronger model.

### Phase 1 Model Strategy

Use a model router:

| Task Type | Model Strategy |
| --- | --- |
| Simple Q&A | Lower-cost model |
| Code explanation | Mid-tier model |
| Complex debugging | Advanced model |
| Enterprise private tenant | Optional self-hosted model |

### Phase 2 Model Strategy

Self-host selected open-source code models using:

- vLLM
- Kubernetes
- GPU autoscaling
- Quantization
- Prompt caching
- Batching

## 9. CapEx and OpEx

### Key Definitions

| Term | Meaning |
| --- | --- |
| CapEx | One-time setup or build investment |
| OpEx | Monthly running cost |

For a SaaS MVP, most costs are OpEx, not traditional CapEx, because the product uses cloud infrastructure and API-based services.

## 10. MVP Budget Estimate

### MVP Duration

**Estimated duration:** 10-12 weeks

### MVP Team Size

**Estimated team size:** 4-6 people

### MVP Build Cost Estimate

| Role | Count | Duration | Approximate Cost |
| --- | ---: | --- | ---: |
| Tech Lead/Architect | 1 | 3 months | $6k-$15k |
| Backend Engineer | 1 | 3 months | $4k-$12k |
| Frontend Engineer | 1 | 2-3 months | $3k-$9k |
| AI/RAG Engineer | 1 | 3 months | $5k-$15k |
| DevOps Engineer | 0.5-1 | 2 months | $3k-$8k |
| QA/Product Support | 0.5 | 1-2 months | $1k-$4k |

### MVP Human Cost Range

| Scenario | Estimated Cost |
| --- | ---: |
| Low-cost lean team | $20k-$35k |
| Stronger professional team | $40k-$70k |

### MVP Monthly OpEx

| Item | Monthly Cost Estimate |
| --- | ---: |
| Hosting backend/API | $100-$300 |
| PostgreSQL | $50-$200 |
| Qdrant | $0-$150 |
| Redis/queue | $20-$100 |
| Object storage | $10-$100 |
| LLM API usage | $200-$2,000 initially |
| Monitoring/logging | $50-$200 |
| Email/auth/miscellaneous | $30-$100 |
| Domain/security/tools | $20-$100 |

**Estimated MVP monthly OpEx range:** $500-$3,000/month

Qdrant Cloud's free tier can support early experiments, and paid usage scales based on cluster resources. Pricing should be verified before final budgeting.

## 11. Phase 1 Budget Estimate

### Phase 1 Duration

**Estimated duration:** 4-6 months after MVP

### Phase 1 Goal

Move from MVP to paid pilot customers.

### Phase 1 Team Size

**Estimated team size:** 8-12 people

### Phase 1 Monthly OpEx

| Item | Monthly Cost Estimate |
| --- | ---: |
| Production cloud infrastructure | $1k-$3k |
| LLM API usage | $2k-$10k |
| Qdrant production cluster | $200-$1k+ |
| Database/Redis/storage | $500-$2k |
| Monitoring/security | $300-$1k |
| Dev/staging/prod environments | $500-$2k |
| Support and customer success tools | $200-$1k |

**Estimated Phase 1 monthly OpEx range:** $5k-$20k/month

**Estimated Phase 1 build and operational investment:** $150k-$300k

## 12. Phase 2 Budget Estimate

### Phase 2 Duration

**Estimated duration:** 6-12 months after Phase 1

### Phase 2 Goal

Scale to enterprise customers, larger teams, private deployment, and optional self-hosted model serving.

### Phase 2 Team Size

**Estimated team size:** 15-25 people

### Phase 2 Monthly OpEx

| Item | Monthly Cost Estimate |
| --- | ---: |
| GPU serving | $5k-$50k+ |
| Kubernetes production infrastructure | $3k-$15k |
| LLM API fallback | $5k-$30k |
| Vector DB HA cluster | $1k-$5k |
| Observability/security/compliance | $2k-$10k |
| Support/customer success | $5k-$20k |
| Data ingestion jobs | $1k-$5k |

**Estimated Phase 2 monthly OpEx range:** $25k-$150k/month

GPU cost can become the biggest cost driver. Third-party cloud pricing trackers may show G6 instances near the low single-digit dollar-per-hour range and H100-class instances at much higher rates. These numbers should be verified in the target AWS region before final investor submission.

## 13. Revenue Model

### Suggested Pricing

| Plan | Monthly Price | Included Usage |
| --- | ---: | --- |
| Free/Trial | $0 | Limited tokens, public docs only |
| Developer Pro | $19-$49/user | Personal usage, small repository |
| Team | $99-$299/team | Shared workspace, repository ingestion |
| Business | $499-$1,999/month | More tokens, private knowledge base |
| Enterprise | Custom | SSO, audit logs, private deployment |

### Usage-Based Add-Ons

- Extra input and output tokens
- Extra repository storage
- Extra ingestion jobs
- Extra MCP tool calls
- Extra private workspaces

Stripe supports usage-based billing models where customers are charged based on actual product usage.

### Example Unit Economics

Assumptions:

- Average paying team: $499/month
- LLM and infrastructure cost per team: $100-$180/month
- Estimated gross margin: 60%-80%

| Customers | Estimated Monthly Revenue |
| ---: | ---: |
| 10 teams | ~$5k MRR |
| 50 teams | ~$25k MRR |
| 100 teams | ~$50k MRR |
| 500 teams | ~$250k MRR |

## 14. MVP Feature Scope

### MVP Must-Have Features

| Feature | Description |
| --- | --- |
| User signup/login | Email/password or OAuth |
| Workspace/tenant | Each client has its own workspace |
| Chat interface | Streaming AI answers |
| Code-aware Markdown | Syntax-highlighted code blocks |
| RAG ingestion | Upload documents and repository files |
| Qdrant search | Retrieve relevant chunks |
| Source citation | Show where answer came from |
| Token tracking | Track input and output tokens |
| Admin panel | View usage and sources |
| Basic billing-ready data | Store usage for future billing |
| Guardrails | Prevent cross-tenant leakage |
| Logs/monitoring | Basic observability |

### MVP Should Not Include

- Training a 70B or 79B model
- Complex fine-tuning
- Enterprise SSO
- Air-gapped deployment
- Full IDE extension
- Full pull request automation
- Unlimited external MCP tools

## 15. MVP Deployment Plan

### MVP Deployment Target

- Cloud-based SaaS
- Single region
- Multi-tenant but logically isolated
- API LLM-based
- Managed vector database and database where possible

### Recommended MVP Deployment

Diagram source file: `../diagrams/03-mvp-deployment-architecture.mmd`

```mermaid
flowchart LR
    Browser[Browser] --> Frontend[Vercel or Cloudflare Pages]
    Frontend --> API[Backend API Container]
    API --> PG[Managed PostgreSQL]
    API --> Redis[Managed Redis]
    API --> S3[S3 Compatible Storage]
    API --> Qdrant[Qdrant Cloud]
    API --> LLM[Hosted LLM API]
    API --> Monitor[Logs and Metrics]
    Worker[Ingestion Worker] --> Redis
    Worker --> S3
    Worker --> Qdrant
    Worker --> PG
```

### MVP Environments

- Development
- Staging
- Production

### MVP Deployment Tools

| Area | Tool |
| --- | --- |
| Frontend | Vercel or Cloudflare Pages |
| Backend | Docker with ECS/Fargate, Render, Railway, or Kubernetes later |
| Database | Managed PostgreSQL |
| Vector DB | Qdrant Cloud |
| Object storage | S3-compatible storage |
| CI/CD | GitHub Actions |
| Monitoring | Grafana/Prometheus or cloud monitoring |
| Billing later | Stripe |

## 16. MVP Timeline

### Total MVP Time

**Estimated duration:** 10-12 weeks

### Sprint Plan

Assumption: 2-week sprints.

| Sprint | Duration | Goal |
| --- | --- | --- |
| Sprint 0 | 1 week | Product discovery and architecture |
| Sprint 1 | 2 weeks | Auth, tenant, and base chat UI |
| Sprint 2 | 2 weeks | LLM integration and streaming |
| Sprint 3 | 2 weeks | RAG ingestion and Qdrant retrieval |
| Sprint 4 | 2 weeks | Admin, usage metering, and security |
| Sprint 5 | 2 weeks | Pilot readiness, QA, and deployment |
| Buffer | 1 week | Fixes and investor demo polish |

## 17. MVP User Stories

### User Story 1: Developer Chat

As a developer, I want to ask a Python question in chat so that I can get code help quickly.

Acceptance criteria:

- User can submit a question.
- Answer streams in the UI.
- Code blocks are formatted.
- Conversation history is saved.

### User Story 2: Upload Knowledge

As a team admin, I want to upload internal docs or repository files so that the assistant can answer from company knowledge.

Acceptance criteria:

- Admin uploads a file.
- System chunks the file.
- Embeddings are created.
- Chunks are stored in Qdrant.
- Assistant retrieves relevant chunks.

### User Story 3: Private Repository Q&A

As a developer, I want to ask questions about my project codebase so that I can understand internal services faster.

Acceptance criteria:

- Assistant retrieves only tenant-specific code.
- Answer cites matching files and chunks.
- No cross-tenant retrieval occurs.

### User Story 4: Token Usage

As a SaaS admin, I want to track token usage by tenant so that I can bill customers correctly.

Acceptance criteria:

- Input tokens are stored.
- Output tokens are stored.
- Model name is stored.
- Conversation ID is stored.
- Tenant usage report is available.

### User Story 5: Admin Knowledge Management

As an admin, I want to see uploaded sources so that I can manage the knowledge base.

Acceptance criteria:

- Admin can view sources.
- Admin can delete a source.
- Deleted source is removed from retrieval.

## 18. Phase 1 Plan

### Phase 1 Objective

Convert MVP into a paid pilot-ready product.

### Timeline

**Estimated duration:** 4-6 months after MVP

### Features

| Area | Phase 1 Feature |
| --- | --- |
| GitHub integration | Connect repositories and index selected branches |
| Advanced RAG | Hybrid search and metadata filtering |
| Billing | Stripe subscription plus token usage |
| Team management | Invite members and assign roles |
| Security | Audit logs and secret detection |
| Model routing | Lower-cost and stronger model selection |
| Admin analytics | Usage dashboard |
| Feedback loop | Thumbs up/down and answer rating |
| Prompt evaluation | Regression tests for answer quality |
| MCP beta | GitHub, documentation, and CI connector |

### Phase 1 Architecture Upgrade

Diagram source file: `../diagrams/05-phase1-architecture.mmd`

```mermaid
flowchart LR
    UI[Web App] --> Gateway[API Gateway]
    Gateway --> App[Backend Services]
    App --> Router[Model Router]
    Router --> Cheap[Lower Cost LLM]
    Router --> Strong[Advanced Code LLM]
    App --> RAG[Hybrid RAG Service]
    RAG --> Qdrant[Dedicated Qdrant Cluster]
    RAG --> PG[Managed PostgreSQL]
    App --> Billing[Stripe Billing]
    App --> Audit[Audit Logs]
    App --> MCP[MCP Connectors]
    MCP --> GitHub[GitHub Repos]
    MCP --> Docs[Documentation]
    MCP --> CI[CI Logs]
    Workers[Worker Pool] --> Qdrant
    Workers --> PG
    Workers --> Storage[Object Storage]
```

## 19. Phase 2 Plan

### Phase 2 Objective

Build an enterprise-scale product.

### Timeline

**Estimated duration:** 6-12 months after Phase 1

### Features

| Area | Phase 2 Feature |
| --- | --- |
| Enterprise SSO | SAML/OIDC |
| Private deployment | VPC, on-prem, or air-gapped option |
| Self-hosted LLM | vLLM GPU serving |
| Fine-tuning | Optional customer or domain fine-tuning |
| IDE plugin | VS Code and JetBrains extensions |
| PR review bot | GitHub/GitLab pull request review |
| Advanced MCP | Controlled tool marketplace |
| Compliance | SOC2-style controls |
| Data governance | Retention, deletion, and DLP |
| Enterprise analytics | Productivity reports |

### Phase 2 Architecture

Diagram source file: `../diagrams/06-phase2-architecture.mmd`

```mermaid
flowchart LR
    Tenant[Enterprise Tenant] --> App[Enterprise SaaS or Private Deployment]
    App --> SSO[SAML or OIDC SSO]
    App --> Policy[Policy and Audit Layer]
    App --> RAG[HA RAG Service]
    RAG --> Qdrant[HA Qdrant Cluster]
    RAG --> Storage[Private Object Storage]
    RAG --> PG[PostgreSQL HA]
    App --> Router[Model Router]
    Router --> Hosted[Hosted LLM Fallback]
    Router --> SelfHosted[vLLM Model Serving]
    SelfHosted --> GPU[GPU Autoscaling]
    App --> IDE[IDE Extensions]
    App --> PR[PR Review Bot]
    App --> Tools[Controlled MCP Marketplace]
```

## 20. Go-To-Market Plan

### MVP-to-Market Flow

Diagram source file: `../diagrams/04-mvp-to-market-flow.mmd`

```mermaid
flowchart TD
    Build[Build MVP] --> Demo[Investor and Design Partner Demo]
    Demo --> Beta[Private Beta with 5 to 10 Teams]
    Beta --> Pilot[Paid Pilot]
    Pilot --> Launch[Public SaaS Launch]
    Launch --> Expand[Team and Enterprise Expansion]
```

### GTM Timeline

| Period | Activity |
| --- | --- |
| Month 0-3 | Build MVP |
| Month 3 | Demo to investors and design partners |
| Month 4-5 | Private beta with 5-10 teams |
| Month 6 | Paid pilot |
| Month 7-9 | Public launch |
| Month 10-12 | Team and enterprise expansion |

### Initial Customer Acquisition Channels

- LinkedIn technical content
- DevOps and Python communities
- MLOps communities
- GitHub Marketplace later
- Partnerships with training centers
- Direct outreach to small software companies
- Product Hunt launch
- Technical blogs and demos

## 21. Team Structure

### MVP Team

| Role | Responsibility |
| --- | --- |
| Founder/Product Owner | Vision, customer discovery, investor pitch |
| Tech Lead | Architecture, code quality, model strategy |
| Backend Engineer | API, auth, usage, conversations |
| Frontend Engineer | Chat UI, dashboard, admin panel |
| AI/RAG Engineer | Ingestion, embeddings, Qdrant, prompt orchestration |
| DevOps Engineer | CI/CD, cloud deployment, monitoring |
| QA/Product Analyst | Test cases, user stories, demo validation |

### Phase 1 Team

**Estimated team size:** 8-12 people

| Role | Count |
| --- | ---: |
| Product Manager | 1 |
| Engineering Manager/Tech Lead | 1 |
| Backend Engineers | 2 |
| Frontend Engineers | 1-2 |
| AI/RAG Engineers | 2 |
| DevOps/SRE | 1 |
| QA Engineer | 1 |
| Customer Success/Support | 1 |
| Sales/Founder-led GTM | 1 |

### Phase 2 Team

**Estimated team size:** 15-25 people

| Department | Roles |
| --- | --- |
| Product | PM, UX Designer |
| Engineering | Backend, frontend, AI, platform |
| Infrastructure | SRE, security engineer |
| AI | Model engineer, evaluation engineer |
| GTM | Sales, customer success, solution engineer |
| Compliance | Security/compliance lead |

## 22. Organization Chart for Phase 1

Diagram source file: `../diagrams/07-team-org-chart.mmd`

```mermaid
flowchart TD
    Founder[Founder and Product Owner] --> PM[Product Manager]
    Founder --> Lead[Engineering Manager or Tech Lead]
    Founder --> GTM[Founder Led GTM]
    Lead --> Backend[Backend Engineers]
    Lead --> Frontend[Frontend Engineers]
    Lead --> AI[AI and RAG Engineers]
    Lead --> DevOps[DevOps or SRE]
    Lead --> QA[QA Engineer]
    PM --> CS[Customer Success]
    GTM --> Sales[Sales Support]
```

## 23. Detailed Work Plan

### Workstream 1: Product and UX

| Task | Output |
| --- | --- |
| Define personas | Developer, team admin, enterprise admin |
| Define MVP user journeys | Chat, upload docs, ask repository question |
| Wireframe UI | Chat, admin, and usage screens |
| Define pricing page | Plan structure |
| Investor demo script | 5-minute product demo |

### Workstream 2: Backend

| Task | Output |
| --- | --- |
| Auth | Login/signup |
| Tenant model | Workspace isolation |
| Conversation API | Chat sessions |
| Usage tracking | Token logs |
| Admin API | Sources, users, usage |
| Billing-ready schema | Stripe integration later |

### Workstream 3: AI/RAG

| Task | Output |
| --- | --- |
| Chunking strategy | Code-aware chunking |
| Embeddings | Stored vectors |
| Qdrant schema | Tenant-aware collections |
| Retrieval | Top-k with metadata filtering |
| Prompt template | RAG answer format |
| Evaluation set | 50-100 test questions |

### Workstream 4: Ingestion

| Task | Output |
| --- | --- |
| File upload | PDF, Markdown, text, and code |
| Repository ingestion | MVP can start with ZIP upload |
| Queue worker | Async indexing |
| Metadata tagging | Source, file, and tenant |
| Deletion | Remove stale chunks |

### Workstream 5: Infrastructure

| Task | Output |
| --- | --- |
| Dockerize services | Local development |
| CI/CD | GitHub Actions |
| Staging | Test deployment |
| Production | MVP SaaS deployment |
| Monitoring | Logs and metrics |
| Backup | Database and object storage backup |

### Workstream 6: Security

| Task | Output |
| --- | --- |
| Tenant isolation | No cross-client access |
| API auth | JWT or session authentication |
| Role-based access | Admin and member roles |
| Secret filtering | Detect secrets in uploads |
| Audit logs | Admin and security review |
| Data deletion | Client can remove sources |

## 24. Investor-Focused Milestones

| Milestone | Timeline | Evidence |
| --- | --- | --- |
| Prototype | Week 4 | Chat plus LLM response |
| RAG MVP | Week 8 | Upload docs and ask questions |
| Private beta | Week 12 | 3-5 test teams |
| Paid pilot | Month 5-6 | First revenue |
| Phase 1 launch | Month 7-9 | Public SaaS |
| Enterprise pilot | Month 10-12 | Larger customer |

## 25. Key Risks and Mitigation

| Risk | Mitigation |
| --- | --- |
| LLM cost too high | Model routing, caching, and usage caps |
| Hallucination | RAG citations and source-first answering |
| Data leakage | Tenant isolation and access control |
| Poor retrieval | Hybrid search and metadata filtering |
| Slow ingestion | Queue workers and batch embeddings |
| Weak differentiation | Focus on private codebase, DevOps, and Python workflows |
| Enterprise trust gap | Audit logs and private deployment roadmap |
| Competition | Niche focus and custom knowledge base |

## 26. Recommended MVP Tech Stack

| Layer | Recommendation |
| --- | --- |
| Frontend | Next.js / React |
| Backend | FastAPI or NestJS |
| Database | PostgreSQL |
| Vector DB | Qdrant |
| Cache/Queue | Redis |
| Object storage | S3-compatible storage |
| LLM | Hosted API first |
| Model serving later | vLLM |
| Billing | Stripe |
| Auth | Auth0, Clerk, or custom JWT |
| Deployment | Docker plus managed cloud |
| Monitoring | Grafana/Prometheus or cloud-native monitoring |

## 27. MVP Feature Backlog

### Priority P0

- Login/signup
- Tenant/workspace
- Chat UI
- LLM response
- File upload
- Embedding and Qdrant storage
- RAG response
- Usage tracking
- Basic admin dashboard

### Priority P1

- GitHub repository connector
- Stripe integration
- Team invite
- Source citation
- Feedback button
- Answer history
- Basic secret detection

### Priority P2

- MCP connector
- CI/CD logs connector
- PR review assistant
- VS Code extension
- Enterprise SSO
- Self-hosted model
- Fine-tuning

## 28. Suggested Investor Ask

For MVP plus Phase 1, the company can ask for an estimated **$250k-$500k seed or pre-seed round**.

### Use of Funds

| Category | Allocation |
| --- | ---: |
| Product engineering | 45% |
| AI/RAG development | 20% |
| Cloud/LLM infrastructure | 15% |
| GTM/sales | 10% |
| Security/compliance | 5% |
| Legal/admin | 5% |

### 12-Month Goal

- Build MVP
- Launch beta
- Acquire 10-25 paying teams
- Reach $10k-$25k MRR
- Prepare enterprise pilot

## 29. Final Investor Narrative

We are building a secure custom knowledge-based AI code assistant for Python, DevOps, and backend engineering teams. Unlike general chatbots, our platform connects to a company's private repositories, documentation, runbooks, and coding standards. It uses RAG, vector search, controlled tool access, and LLM routing to provide accurate code help, debugging support, test generation, and onboarding assistance.

The MVP can be built in an estimated 10-12 weeks using existing LLM APIs and Qdrant. Phase 1 will add GitHub integration, billing, team management, analytics, and paid pilots. Phase 2 will expand to enterprise features such as SSO, audit logs, private deployment, self-hosted model serving, and IDE integrations.

## 30. Final Recommendation

### Recommended Path

| Phase | Recommendation |
| --- | --- |
| MVP | Hosted LLM plus Qdrant RAG, SaaS chat, and usage metering |
| Phase 1 | Paid team product, GitHub integration, billing, and analytics |
| Phase 2 | Enterprise product, self-hosted LLM, private deployment, and IDE/PR automation |

Do not start by training a 70B or 79B model.

Start by proving:

- Can teams get useful, trusted coding answers from their own private knowledge?
- Will they pay monthly for it?
- Can the system deliver accurate answers at acceptable cost?

That is the core business feasibility point investors will care about most.
