# Synapse Optical

> **Daniel Azgour** — Senior Network Security Engineer · Fortinet · Palo Alto Networks · Hybrid Cloud Networking · Network Automation

---

## The Problem This Solves

Enterprise network operations are routinely slower, less consistent, and more dependent on individual expertise than the underlying infrastructure complexity requires.

Troubleshooting workflows live in engineers' heads rather than in repeatable systems. Configuration changes lack structured validation steps. Vendor-specific quirks cause preventable outages. Audit trails are inconsistent or absent. And when something breaks at 2am, the quality of the response depends entirely on who is on call.

Synapse Optical is a direct response to those operational realities — a platform designed to improve the speed, consistency, safety, and auditability of enterprise network and security operations without removing the engineer from the equation.

> **Status:** Active proof-of-concept under ongoing development. This repository contains architecture documentation, design philosophy, workflow concepts, and sanitized examples. Core proprietary source code and backend systems are intentionally excluded.

---

## What This Demonstrates

This project showcases engineering capability across several disciplines that are difficult to demonstrate through code samples alone:

- **Systems-level architecture** — thin-client / controlled-backend design with clear separation of concerns across UI, API, validation, vendor abstraction, and persistence layers
- **Operational safety engineering** — human-in-the-loop workflow design, deterministic validation before execution, and audit-first operational controls
- **AI integration with appropriate boundaries** — AI positioned as an analysis and reasoning layer, not as an autonomous execution authority
- **Multi-vendor abstraction** — normalized operational workflows across heterogeneous infrastructure platforms with vendor-aware validation logic
- **Enterprise infrastructure domain depth** — IPsec VPN operations, firewall policy management, routing analysis, NAT troubleshooting, configuration migration, and security posture assessment across FortiOS and PAN-OS platforms

---

## Platform Architecture

![Synapse Optical — Platform Architecture](./diagrams/synapse_optical_platform_architecture_v2.svg)

*The platform is structured in five layers: a thin React/TypeScript web client, a controlled FastAPI backend responsible for all orchestration and validation logic, a vendor abstraction layer that normalizes operational workflows across platforms, vendor-specific integrations (currently FortiGate and PAN-OS), and a PostgreSQL/Redis persistence layer. AI assistance operates within the backend as a bounded reasoning layer — it does not generate device commands or control execution.*

---

## What Synapse Optical Is Not

This platform is **not** an autonomous AI agent that configures production infrastructure without oversight.

It is not a chatbot wrapper around a network device API. It is not a script runner with an LLM bolted on. It is not a traditional monitoring tool.

Every production-impacting action is gated behind deterministic validation, structured workflow execution, and explicit human approval. AI accelerates and assists — it does not replace engineering judgment or bypass operational controls.

---

## Why Deterministic Validation Matters

Modern AI systems are highly capable at interpretation, summarization, and operational reasoning. These capabilities create genuine opportunities in infrastructure operations.

However, production infrastructure environments are fundamentally different from conversational or creative contexts. A single misconfigured route, firewall policy ordering error, incompatible VPN proposal, or unsupported cryptographic setting can cause production outages, service instability, SLA violations, or security exposure. These are not theoretical concerns — they are common operational realities in enterprise environments.

Probabilistic systems can produce non-deterministic outputs, incorrect vendor-specific assumptions, and operational ambiguity. In infrastructure operations, those failure modes introduce real risk.

Synapse Optical is built around a **deterministic-first architecture** for this reason:

```
Deterministic Core
├── prerequisite validation
├── vendor compatibility checks
├── dependency analysis
├── configuration consistency validation
├── execution sequencing and controls
├── rollback planning
└── audit logging

AI Assistance Layer (bounded)
├── troubleshooting analysis and summarization
├── workflow interaction and interpretation
├── diagnostic output explanation
└── operational reasoning assistance
```

The deterministic core provides predictable, repeatable, auditable behavior. The AI layer improves engineer efficiency and accelerates operational reasoning. The separation is intentional and architecturally enforced — AI informs intent, never device execution.

---

## Architecture Philosophy

### Vendor-Aware Abstraction

Operational workflows are normalized across vendor platforms while preserving vendor-specific behavior where it matters. Workflow logic adapts to platform OS version, licensing constraints, supported cryptographic capabilities, and configuration dependencies. This reduces unsupported or unsafe configuration generation across heterogeneous environments.

### Human Approval and Operational Safety

Every production-impacting workflow follows a structured execution model:

1. Workflow request or operational analysis
2. Deterministic validation and prerequisite checks
3. Change preview and risk summary
4. **Human review and approval**
5. Controlled execution
6. Post-change verification
7. Audit record and rollback tracking

Operational safety is a primary architectural requirement, not a feature added after the fact.

### Audit-First Design

Operational workflows are designed with auditability built in from the start — execution tracking, workflow history, validation summaries, rollback tracking, and operator attribution. Not as a compliance afterthought, but as a core operational control.

---

## Operational Workflow

![Synapse Optical — Operational Workflow](./diagrams/synapse_optical_public_operational_workflow_v3_spacing_fixed.svg)

*The workflow model shows the flow from engineer request through the web UI and backend API, through workflow engine validation and AI-assisted analysis, to human approval, controlled device execution, audit logging, and outcome presentation. AI assists the analysis phase; it does not drive execution. Human approval is a non-optional gate before any production-impacting action.*

---

## Technical Architecture

Synapse Optical uses a **thin-client / controlled-backend architecture**:

- **Frontend:** React / TypeScript — dashboard, device management, guided workflows, change requests, troubleshooting views, health assessment, migration workspace, audit log
- **Backend API:** FastAPI — authentication, RBAC, session controls, workflow orchestration, change planning, deterministic validation, risk scoring, audit service
- **Vendor Abstraction Layer:** plugin registry, normalized configuration model, backend-owned command templates, rollback and verification logic
- **Device Interaction:** REST API (FortiGate), XML API (PAN-OS), SSH/CLI fallback
- **Migration Engine:** TypeScript sidecar — deterministic, pure config translation across vendor pairs (no device I/O)
- **Persistence:** PostgreSQL (devices, config snapshots, change records, audit logs, assessment runs), Redis (session tokens, cache, task state)

Full architecture documentation is available in the [`architecture/`](./architecture/) directory.

---

## Current PoC Focus Areas

FortiGate is the primary and most complete implementation. PAN-OS integration is fully operational across multiple workflow areas. Both vendors are supported by a shared vendor abstraction layer.

- **Fortinet FortiGate** operational workflows (FortiOS 7.2.x, 7.4.x, 7.6.x)
- **Palo Alto Networks PAN-OS 11.2.x** — VPN commissioning, interface management, policy analysis, routing, diagnostics
- **IPsec VPN** — site-to-site commissioning (FortiGate + PAN-OS), Phase 1/2 troubleshooting, DPD, NAT-T, traffic selector analysis
- **Remote Access VPN** — FortiGate IPsec Dialup and PAN-OS GlobalProtect commissioning workflows
- **Firewall Health Assessment** — read-only security and operational posture scoring across 10 assessment categories, per-finding review workflow, fleet overview, scheduled assessments, CSV and PDF export
- **Config Migration Workspace** — deterministic firewall config translation: Cisco ASA → FortiOS, FortiOS → PAN-OS 11.2; canonical intermediate model; per-object mapping status and readiness gate
- **Dynamic routing protocol templates** — OSPF and BGP configuration via vendor-normalized template layer (FortiGate + PAN-OS)
- **Guided BAU change templates** — interface configuration, NAT policy, address objects, static routes, security policy, VPN tunnel (deterministic path, no AI)
- **Device topology model** — VPN peer, HA pair, SD-WAN hub/spoke, and BGP neighbor relationship tracking; cross-device VPN diagnostics
- **Change lifecycle** — full pipeline from guided workflow through human approval, controlled execution, post-change verification, and rollback-capable audit record

---

## Feature Matrix

| Feature | Status |
|---|---|
| FortiGate operational workflows (FortiOS 7.2.x / 7.4.x / 7.6.x) | ✅ Implemented |
| PAN-OS 11.2.x integration | ✅ Implemented |
| Site-to-site IPsec VPN commissioning (FortiGate + PAN-OS) | ✅ Implemented |
| IPsec VPN troubleshooting (Phase 1/2, DPD, NAT-T) | ✅ Implemented |
| Remote Access VPN — FortiGate IPsec Dialup | ✅ Implemented |
| Remote Access VPN — PAN-OS GlobalProtect | ✅ Implemented |
| Firewall Health Assessment (10 categories, scoring, findings) | ✅ Implemented |
| Health Assessment — fleet overview | ✅ Implemented |
| Health Assessment — scheduled assessments | ✅ Implemented |
| Health Assessment — PDF and CSV export | ✅ Implemented |
| Config Migration Workspace — Cisco ASA → FortiOS | ✅ Implemented |
| Config Migration Workspace — FortiOS → PAN-OS | ✅ Implemented |
| Dynamic routing templates — OSPF + BGP (FortiGate + PAN-OS) | ✅ Implemented |
| Guided BAU change templates (interface, NAT, policy, route, objects) | ✅ Implemented |
| Device topology model and relationship tracking | ✅ Implemented |
| Cross-device VPN diagnostics | ✅ Implemented |
| Change lifecycle with post-change verification | ✅ Implemented |
| AI-assisted troubleshooting analysis | ✅ Implemented |
| AI-assisted health assessment executive summary | ✅ Implemented |
| Cisco IOS / IOS-XE / NX-OS support | 📋 Planned |
| Cisco Firepower / FTD support | 📋 Planned |
| Juniper SRX support | 📋 Planned |
| Check Point Gaia support | 📋 Planned |

---

## Vendor Support

| Platform | Status | Notes |
|---|---|---|
| Fortinet FortiGate (FortiOS 7.2.x, 7.4.x, 7.6.x) | ✅ Implemented | Primary implementation; REST API + SSH |
| Palo Alto Networks PAN-OS 11.2.x | ✅ Implemented | XML API; device-direct |
| Cisco ASA | ✅ Implemented | Migration source only (ASA → FortiOS config translation) |
| Cisco IOS / IOS-XE / NX-OS | 📋 Planned | — |
| Cisco Firepower / FTD | 📋 Planned | — |
| Juniper SRX (Junos OS) | 📋 Planned | — |
| Check Point Gaia | 📋 Planned | — |
| FortiManager / FortiAnalyzer | — | Not in current scope |

---

## Functional Areas

### VPN Operations
Site-to-site IPsec commissioning (FortiGate and PAN-OS), Phase 1 and Phase 2 analysis, DPD and NAT-T diagnostics, routing and traffic selector validation, authentication and peer identity troubleshooting, tunnel state verification, remote access VPN commissioning (FortiGate IPsec Dialup and PAN-OS GlobalProtect).

### Firewall Health Assessment
Read-only security and operational posture assessment against a structured set of categories — policy hygiene, VPN configuration, NAT consistency, object hygiene, admin identity controls, management exposure, logging coverage, segmentation, and platform posture. Produces a scored report (0–100, A–F grade) with per-finding review workflow. Supports single-device runs and fleet-wide overview. Assessments can be scheduled, run against live device state, stored snapshots, or manually uploaded config files.

### Configuration Management and Migration
Pre-change operational validation, post-change verification workflows, rollback planning, configuration snapshot management. Deterministic config migration workspace: paste or upload a source firewall config, select a target vendor, and receive a per-object translated output with confidence classification (auto-mapped, requires approval, requires remediation, blocked). Cisco ASA → FortiOS and FortiOS → PAN-OS 11.2 are supported. Migration output is a reviewable preview — no changes are pushed to devices.

### Firewall Operations
Policy analysis and consistency checks, address object management, interface configuration workflows, NAT validation, deterministic policy validation workflows across FortiGate and PAN-OS.

### Routing
BGP and OSPF configuration templates via a vendor-normalized layer. Protocol schemas are vendor-agnostic; rendering adapters produce FortiOS CLI or PAN-OS XML output. Unsupported features are reported explicitly rather than silently dropped.

### Device Topology
VPN peer, HA pair, SD-WAN hub/spoke, and BGP neighbor relationship tracking across registered devices. Relationships are auto-suggested from diagnostic results and confirmed by the operator. Cross-device concurrent VPN diagnostics correlate Phase 1/2 state and IKE proposals across a device pair.

---

## Roadmap

### Current PoC
All items in the Feature Matrix marked ✅ Implemented.

### Near-Term
- Extending multi-vendor coverage across existing operational workflow types
- Additional vendor support for migration (further source/target pairs)
- Firewall Health Assessment — expanded per-check detail reporting
- PoC-to-v1 hardening: production auth, TLS verification, multi-tenancy enforcement

### Long-Term Vision
- Commercial multi-tenant SaaS platform
- Expanded vendor support: Cisco IOS/IOS-XE/NX-OS, Juniper SRX, Check Point Gaia, Cisco Firepower/FTD
- Workflow marketplace for operational runbooks
- Distributed task execution and connection pooling at scale

---

## Repository Structure

```text
README.md

architecture/
├── high-level-architecture.md
├── operational-workflow.md
├── vendor-abstraction.md
├── safety-model.md
├── roadmap.md
├── why-deterministic-first-matters.md

workflows/
├── vpn-tunnel-failure-investigation.md
├── route-based-vpn-commissioning.md

diagrams/
screenshots/
```

> Core proprietary source code, orchestration logic, internal AI workflows, backend systems, vendor command templates, and execution pipelines are intentionally excluded from this public repository.

---

## Disclaimer

All configurations, examples, diagrams, screenshots, and workflows in this repository are sanitized and intended for demonstration, educational, and architectural showcase purposes only. No customer environments, credentials, proprietary client data, or production-sensitive information are included.

---

## Author

**Daniel Azgour**
Senior Network Security Engineer
Fortinet · Palo Alto Networks · Hybrid Cloud Networking · Network Automation · Operational Security Engineering
