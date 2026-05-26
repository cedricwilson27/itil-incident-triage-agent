# ITIL Incident Triage Agent

> An AI agent that classifies, prioritizes, and drafts initial response actions for IT incidents — aligned with ITIL V5 incident management practices. Built on Microsoft Foundry with Azure OpenAI.

![Status](https://img.shields.io/badge/status-in_development-yellow)
![Python](https://img.shields.io/badge/python-3.12+-blue)
![Platform](https://img.shields.io/badge/platform-Microsoft_Foundry-0078D4)
![License](https://img.shields.io/badge/license-MIT-green)

## The problem

Enterprise IT teams handle thousands of incident tickets per week. Initial triage — classification, prioritization, runbook lookup, and first-response drafting — is repetitive, time-consuming, and prone to inconsistency. A skilled L1 analyst spends 5-10 minutes per ticket on triage alone.

This project demonstrates how an AI agent can compress that work to seconds while maintaining the structure, auditability, and governance that ITIL V5 incident management requires.

## What it does

Given an incoming incident ticket, the agent:

1. **Classifies** the incident by category (infrastructure, application, security, access, other)
2. **Prioritizes** it using the ITIL V5 priority matrix (impact × urgency)
3. **Scores confidence** in its classification (0.0 to 1.0)
4. **Flags for human review** when confidence falls below threshold
5. **Returns structured JSON** with full reasoning trace for audit

## Current status

The classifier component is validated in Microsoft Foundry playground against multiple test scenarios. Python implementation in progress.

### Test scenarios validated

| Scenario | Expected | Result | Confidence |
|----------|----------|--------|------------|
| Single user SAP access issue after laptop setup | access / P3 | ✓ access / P3 | 0.85 |
| Total network outage affecting 800 users | infrastructure / P1 | ✓ infrastructure / P1 | 1.0 |
| Vague ticket: "things are slow today" | flagged for review | ✓ human_review_required: true | 0.5 |

The third case is the most important — the agent correctly recognized insufficient information and flagged for human review rather than guessing. This is the ITIL governance behavior that separates enterprise-ready AI from demo-ware.

## Tech stack

- **Microsoft Foundry** — AI platform and orchestration
- **Azure OpenAI Service** — LLM backend
- **gpt-4o** (version 2024-11-20) — classification model
- **Python 3.12+** — implementation language
- **FastAPI** — REST API framework (in progress)
- **Pydantic** — structured input/output validation
- **ChromaDB** — vector store for runbook RAG (planned)
- **Streamlit** — demo UI (planned)

## ITIL V5 alignment

This agent implements the following practices from the ITIL V5 service value chain:

- **Incident management** — automated classification and prioritization per the standard ITIL priority matrix
- **Knowledge management** — runbook retrieval grounded in vector-indexed knowledge base (planned)
- **Monitoring and event management** — structured logging of every triage decision for audit
- **Change enablement** — drafted responses include escalation paths consistent with change advisory board patterns (planned)

The AI Governance principles from ITIL V5's AI extension are addressed through:

- Human-in-the-loop final approval (agent drafts, humans send)
- Full reasoning trace logged with every decision
- Confidence scoring with automatic human-review flagging below 0.75 threshold
- Configurable guardrails on response drafting

## Architecture

┌──────────────────┐      ┌─────────────────────┐      ┌──────────────────┐
│  Incident input  │─────▶│   FastAPI service   │─────▶│  Triage response │
│  (text/JSON)     │      │                     │      │  (structured)    │
└──────────────────┘      └─────────┬───────────┘      └──────────────────┘
│
┌─────────┼─────────┐
▼         ▼         ▼
┌──────────┐ ┌──────┐ ┌──────────┐
│Classifier│ │ RAG  │ │ Drafter  │
│  agent   │ │ over │ │  agent   │
│          │ │runbooks│        │
└────┬─────┘ └──┬───┘ └────┬─────┘
│          │          │
└──────────┼──────────┘
▼
┌──────────────────────┐
│  Microsoft Foundry   │
│  Azure OpenAI gpt-4o │
└──────────────────────┘

## Roadmap

- [x] Microsoft Foundry project provisioning
- [x] Azure OpenAI gpt-4o deployment
- [x] Classifier system prompt validated in Foundry playground
- [x] Confidence-based human-review flagging working correctly
- [ ] Python implementation with FastAPI
- [ ] Pydantic models for type-safe inputs and outputs
- [ ] Runbook library (10 sample runbooks)
- [ ] Vector store indexing with ChromaDB
- [ ] RAG-based runbook retrieval
- [ ] Response drafter agent
- [ ] Streamlit demo UI
- [ ] Deployment to Azure Container Apps
- [ ] Loom demo video

## What I'd do differently at scale

- **Multi-tenancy** — current design assumes single organization; production would need tenant isolation in the vector store and audit logs
- **Streaming responses** — for the drafter agent, streaming would improve perceived latency in the UI
- **Eval harness** — would add a labeled test set of ~500 historical tickets to measure classification accuracy, priority accuracy, and runbook retrieval recall@k over time
- **Cost controls** — would add per-tenant token quotas and a fallback to a smaller model for high-confidence classifications
- **Integration** — production version would webhook directly into ServiceNow or Jira Service Management rather than expose a standalone API

## About this project

Built as part of my transition back to hands-on technical work after 20+ years in enterprise software engineering. I'm particularly interested in the intersection of AI agents and enterprise governance frameworks like ITIL — most AI engineering content treats governance as an afterthought, but it's where enterprise AI actually lives or dies.

Connect with me on [LinkedIn](https://www.linkedin.com/in/cedricwilson) if you're working on similar problems.

## License

MIT
