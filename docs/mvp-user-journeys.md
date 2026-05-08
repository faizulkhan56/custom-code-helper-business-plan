# MVP User Journeys

This document is the detailed product journey artifact for the MVP of Custom Knowledge-Based Code Helper. It expands the summary in `docs/custom-knowledge-code-helper-business-plan.md` and must stay aligned with the MVP feature scope.

## 1. MVP Feature Scope Used by These Journeys

| Feature | Description |
| --- | --- |
| User signup/login | Email/password or OAuth |
| Workspace/tenant | Each client has its own workspace |
| Chat interface | Streaming AI answers |
| Code-aware Markdown | Syntax-highlighted code blocks |
| MVP language coverage | Python, PHP, and .NET code assistance through prompts, parsing, and RAG evaluation |
| RAG ingestion | Upload documents and repository files |
| Qdrant search | Retrieve universal coding knowledge plus tenant-specific private chunks |
| Source citation | Show where answer came from |
| Token tracking | Track input and output tokens |
| Admin panel | View sources, package limits, tenant usage, and individual user-wise usage |
| User usage view | Each user can view only their own token and conversation usage |
| Central SaaS admin console | Monitor client workspaces separately, review package usage, and support billing operations |
| Basic billing-ready data | Store usage for future billing |
| Guardrails | Prevent cross-tenant leakage |
| Logs/monitoring | Basic observability |

## 2. Persona Summary

| User Type | Primary Goal | MVP Features Used |
| --- | --- | --- |
| Client developer user | Ask coding and internal knowledge questions through chat | User signup/login, workspace/tenant, chat interface, code-aware Markdown, MVP language coverage, Qdrant search, source citation, token tracking, user usage view, guardrails |
| Client admin user | Manage the client workspace, uploaded knowledge, users, package limits, and team usage | User signup/login, workspace/tenant, RAG ingestion, admin panel, token tracking, basic billing-ready data, logs/monitoring, guardrails |
| Central SaaS admin | Monitor and support all client workspaces separately without cross-tenant data leakage | Central SaaS admin console, workspace/tenant, token tracking, basic billing-ready data, logs/monitoring, guardrails |
| Founder/product operator | Run demos, inspect adoption, and validate which teams receive value | Chat interface, source citation, token tracking, central SaaS admin console, logs/monitoring, basic billing-ready data |
| Support/customer success user | Help client admins troubleshoot onboarding, uploads, and usage questions | Central SaaS admin console, logs/monitoring, workspace/tenant, admin panel, source management metadata |
| Finance/billing operator | Prepare package usage reports and billing reconciliation | Token tracking, basic billing-ready data, central SaaS admin console, admin panel |
| Investor/design partner reviewer | Validate that the product is a real SaaS workflow | Workspace/tenant, chat interface, RAG ingestion, source citation, token tracking, admin panel |

## 3. Client Developer Journey

The client developer is an employee of a customer software firm. The developer uses the product to get coding help that understands both universal coding knowledge and the client's private repositories or documentation.

End-to-end journey:

1. The developer signs in through email/password or OAuth.
2. The system places the developer inside the correct client workspace based on tenant membership.
3. The developer opens the chat interface and asks a Python, PHP, .NET, architecture, debugging, testing, or DevOps question.
4. The backend applies tenant guardrails and retrieves only shared universal coding knowledge plus private chunks belonging to that client workspace.
5. The hosted LLM receives the retrieved context and generates a streaming answer with code-aware Markdown.
6. The answer shows source citations so the developer can verify which file, document, or knowledge source was used.
7. Input and output tokens are recorded against the developer, conversation, model, and tenant.
8. The developer can later view personal usage and conversation history, but cannot see other users' usage or other tenants' data.

Success outcome: the developer receives source-backed coding help without exposing client data outside the allowed tenant boundary.

## 4. Client Admin Journey

The client admin is responsible for setting up and managing the customer's workspace. This user may be an engineering manager, tech lead, CTO, or appointed workspace administrator.

End-to-end journey:

1. The client admin signs in and enters the client workspace.
2. The admin uploads internal documentation, repository ZIP files, coding standards, runbooks, and source files.
3. The ingestion worker stores files in object storage, chunks code/documents, creates embeddings, stores vectors in Qdrant with tenant metadata, and stores source metadata in PostgreSQL.
4. The admin reviews uploaded sources, confirms that knowledge is available, and removes stale or incorrect sources when needed.
5. The admin invites or manages developer users if team management is enabled in the MVP implementation.
6. The admin views tenant usage, package limits, and individual user-wise token usage.
7. The admin uses billing-ready data to understand whether the client is staying within the purchased package.
8. The admin relies on logs and source metadata for basic support and audit review.

Success outcome: the client can safely manage its private knowledge base and understand team usage without needing direct provider intervention for every operational task.

## 5. Central SaaS Admin Journey

The central SaaS admin is an internal operator from the provider company. This user supports the SaaS business across all clients while respecting tenant separation.

End-to-end journey:

1. The central admin signs in to the provider admin console.
2. The console lists client workspaces separately with tenant ID, package, usage status, ingestion status, and system health indicators.
3. The central admin can monitor token usage by client and identify accounts approaching package limits.
4. The central admin can inspect operational metadata such as ingestion job status, error logs, billing-ready usage records, and source counts.
5. The central admin cannot use normal retrieval to read another client's private knowledge unless a support workflow and permission policy explicitly allow it.
6. The central admin can help resolve failed uploads, excessive usage, blocked ingestion, or package-limit questions.
7. The central admin exports or reconciles billing-ready data for invoice preparation or Stripe integration.
8. Logs and monitoring help the central admin identify incidents, cost spikes, and tenant-isolation problems.

Success outcome: the provider can operate the multi-tenant SaaS business, support clients separately, and protect client data boundaries.

## 6. Support and Customer Success Journey

Support and customer success users help client admins complete onboarding and troubleshoot operational issues.

End-to-end journey:

1. A client admin reports an upload, retrieval, usage, or onboarding problem.
2. The support user selects the correct tenant in the central SaaS admin console.
3. The support user reviews non-sensitive operational metadata such as ingestion status, source count, job errors, and log events.
4. The support user guides the client admin to retry upload, delete stale sources, reduce file size, or check package limits.
5. The support user confirms that developers can receive cited answers after the fix.
6. The support user records the issue for future product improvement.

Success outcome: client onboarding friction is reduced without exposing private customer code unnecessarily.

## 7. Finance and Billing Journey

The finance or billing operator prepares usage-based billing reports before full Stripe usage billing is automated.

End-to-end journey:

1. The operator opens the central SaaS admin console.
2. The operator selects a client workspace.
3. The operator reviews package level, tenant token usage, user-wise usage, and billing-ready records.
4. The operator identifies whether the client is inside included usage or has overage.
5. The operator exports usage data for manual invoice preparation or Stripe reconciliation.
6. The operator flags unusual usage spikes for support or product review.

Success outcome: billing stays explainable and defensible while the company validates package pricing.

## 8. Founder or Product Operator Journey

The founder or product operator uses the MVP to run investor demos, design partner walkthroughs, and product adoption reviews.

End-to-end journey:

1. The operator selects a safe demo workspace or design partner workspace.
2. The operator uploads representative sample docs and code.
3. The operator asks Python, PHP, .NET, debugging, test generation, and architecture questions.
4. The operator shows source citations, tenant isolation, usage tracking, and admin reporting.
5. The operator reviews logs and usage data to understand adoption and demo reliability.
6. The operator converts product evidence into investor updates, roadmap priorities, or sales material.

Success outcome: the MVP can demonstrate a measurable SaaS workflow, not only a chatbot interface.

## 9. Investor or Design Partner Reviewer Journey

An investor or design partner reviewer validates whether the product solves a real workflow for software firms.

End-to-end journey:

1. The reviewer sees a controlled workspace with sample or approved customer data.
2. The reviewer observes login, workspace isolation, document upload, and chat.
3. The reviewer asks or reviews representative coding questions.
4. The reviewer sees source citations and usage metering.
5. The reviewer sees client admin reporting and central SaaS monitoring.
6. The reviewer understands how the system can become a paid SaaS package with usage-based expansion.

Success outcome: the reviewer can connect the technical workflow to a credible business model.

## 10. Role Permission Matrix

| Capability | Developer | Client Admin | Central SaaS Admin | Support/CS | Finance/Billing |
| --- | --- | --- | --- | --- | --- |
| Sign in | Yes | Yes | Yes | Yes | Yes |
| Use chat | Yes | Yes | No by default | No by default | No |
| View own usage | Yes | Yes | Yes | Yes | Yes |
| View tenant usage | No | Own tenant only | All tenants summary | Assigned tenants summary | All tenants billing summary |
| View user-wise tenant usage | No | Own tenant only | Summary/metadata | Assigned tenants metadata | Billing records |
| Upload sources | No by default | Yes | No by default | No by default | No |
| Delete tenant sources | No | Yes | Support workflow only | Support workflow only | No |
| View private retrieved content | Own authorized tenant only | Own tenant only | No by default | No by default | No |
| Review operational logs | No | Limited own tenant | Yes | Assigned tenants | Billing events only |
| Export billing-ready data | No | Own tenant summary | Yes | No by default | Yes |

## 11. Feature-to-Journey Traceability

| MVP Feature | Journeys That Depend on It |
| --- | --- |
| User signup/login | Developer, client admin, central SaaS admin, support, finance |
| Workspace/tenant | All journeys |
| Chat interface | Developer, client admin, founder/product operator, investor/design partner |
| Code-aware Markdown | Developer, founder/product operator, investor/design partner |
| MVP language coverage | Developer, founder/product operator, investor/design partner |
| RAG ingestion | Client admin, support, founder/product operator, investor/design partner |
| Qdrant search | Developer, client admin validation, founder/product operator |
| Source citation | Developer, founder/product operator, investor/design partner |
| Token tracking | Developer, client admin, central SaaS admin, finance/billing |
| Admin panel | Client admin, support, finance/billing |
| User usage view | Developer, client admin |
| Central SaaS admin console | Central SaaS admin, support, finance/billing, founder/product operator |
| Basic billing-ready data | Client admin, central SaaS admin, finance/billing |
| Guardrails | All journeys |
| Logs/monitoring | Central SaaS admin, support, founder/product operator |
