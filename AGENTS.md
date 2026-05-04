# AGENTS.md

## Project Purpose

This repository contains an investor-ready business and technical plan for a SaaS product named **Custom Knowledge-Based Code Helper**.

The product is a SaaS AI coding assistant that helps software teams ask questions about Python code, private repositories, internal documentation, architecture, debugging, and DevOps workflows using:

- LLM-powered chat
- RAG with Qdrant/vector database
- Tenant-specific knowledge bases
- Optional MCP/tool integrations
- Token-based SaaS billing
- MVP, Phase 1, and Phase 2 product roadmap

## Main Output Files

The main deliverable should be:

```text
docs/custom-knowledge-code-helper-business-plan.md


## All Mermaid diagram source files should be stored separately under:

diagrams/

Use clear diagram filenames such as:

- diagrams/01-product-architecture.mmd
- diagrams/02-mvp-deployment-architecture.mmd
- diagrams/03-mvp-to-market-flow.mmd
- diagrams/04-phase1-architecture.mmd
- diagrams/05-phase2-architecture.mmd
- diagrams/06-team-org-chart.mmd


Diagram source file: ../diagrams/01-product-architecture.mmd


## Mermaid Diagram Rules

When creating Mermaid diagrams:

Use GitHub-compatible Mermaid syntax.
Prefer simple, readable diagrams over overly complex diagrams.
Keep diagram node names short but meaningful.
Avoid special characters that may break Mermaid rendering.
Save each diagram in a separate .mmd file.
Keep the same diagram content inline in the Markdown document.
Keep diagram filenames lowercase and descriptive.
Do not create duplicate diagrams unless the content is meaningfully different.


##Business Writing Rules

When improving the business plan:

Do not invent unrealistic claims.
Mark budgets, timelines, and costs as estimates.
Keep investor language clear and credible.
Explain technical terms in a business-friendly way.
Make the document understandable to both technical and non-technical investors.
Keep the product positioning focused on:
Custom knowledge-based code helper
Python/code assistant
Private repository and internal documentation Q&A
RAG-based answer accuracy
Token-based SaaS billing
Enterprise expansion potential

## Technical Architecture Rules
LLM = reasoning and answer generation engine
Qdrant/vector DB = RAG knowledge retrieval layer
PostgreSQL = users, tenants, billing, metadata, conversations
Object storage = uploaded docs, repo snapshots, source files
Redis/queue = async ingestion and background jobs
MCP/tools = controlled external integrations
GPU = required only for self-hosted LLM inference or fine-tuning
CPU = enough for MVP backend, RAG orchestration, ingestion, database, and API services






MVP Scope Rules

For MVP, focus on:

Chat UI
User login
Workspace/tenant support
LLM integration
File/document upload
Qdrant-based RAG
Basic source citation
Token usage tracking
Admin dashboard
Basic deployment
Investor demo readiness

Do not include advanced Phase 2 features in MVP unless explicitly requested.

Avoid adding these to MVP:

Training a 70B/79B model
Full self-hosted LLM infrastructure
Enterprise SSO
Air-gapped deployment
IDE extension
Full PR review automation
Complex fine-tuning pipeline
Phase Planning Rules

Use this structure:

MVP: 10–12 weeks
Phase 1: 4–6 months after MVP
Phase 2: 6–12 months after Phase 1

For each phase, clearly describe:

Features
Timeline
Team size
Cost estimate
Infrastructure requirement
Go-to-market activity
Investor milestone
Recommended Document Structure

Use this general structure for the main Markdown document:

# Custom Knowledge-Based Code Helper: Business and Technical Plan

## Table of Contents

## 1. Executive Summary
## 2. Problem Statement
## 3. Solution Overview
## 4. Business Case and Feasibility
## 5. Target Customers
## 6. Product Footprint
## 7. Technical Architecture
## 8. CPU, GPU, and RAG Database Logic
## 9. MVP Plan
## 10. Phase 1 Plan
## 11. Phase 2 Plan
## 12. Budget, CapEx, and OpEx
## 13. Team Structure and Organization Chart
## 14. Sprint Plan
## 15. User Stories
## 16. Go-to-Market Plan
## 17. Risk and Mitigation
## 18. Investor Ask
## 19. Final Recommendation
File and Folder Structure

Maintain this recommended repository structure:

.
├── AGENTS.md
├── README.md
├── docs/
│   └── custom-knowledge-code-helper-business-plan.md
└── diagrams/
    ├── 01-product-architecture.mmd
    ├── 02-ingestion-pipeline.mmd
    ├── 03-mvp-deployment-architecture.mmd
    ├── 04-mvp-to-market-flow.mmd
    ├── 05-phase1-architecture.mmd
    ├── 06-phase2-architecture.mmd
    └── 07-team-org-chart.mmd
Editing Behavior

When asked to modify the document:

Update the relevant Markdown section.
Keep formatting consistent.
Keep diagrams synchronized between:
Inline Mermaid blocks in the Markdown file
Separate .mmd files inside diagrams/
Do not delete existing sections unless explicitly instructed.
Prefer small, clean edits instead of rewriting the whole document unnecessarily.
After changes, summarize what files were changed.
Do not alter budget or timeline estimates unless explicitly asked.
Do not add fake citations, fake customer names, or fake investor claims.
Quality Standard

The final document should be suitable for:

Investor discussion
Founder planning
Technical architecture review
MVP team alignment
Future pitch deck creation
Future product roadmap planning
Output Expectation

When generating the main business plan:

Create or update:
docs/custom-knowledge-code-helper-business-plan.md
Create or update all relevant diagram files under:
diagrams/
Ensure all Mermaid diagrams render correctly on GitHub.
Ensure the business plan reads like a professional investor-facing document.
Ensure all technical architecture decisions are realistic for MVP, Phase 1, and Phase 2.
