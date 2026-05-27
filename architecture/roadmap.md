# Roadmap

## Overview

Synapse Optical is an AI-assisted network automation and operational troubleshooting platform
built around a deterministic-first architecture. The platform combines structured validation
logic, vendor-aware operational workflows, human-approval-gated execution, and AI-assisted
reasoning to improve the speed, consistency, safety, and auditability of enterprise network
and security operations.

This roadmap reflects architectural direction, development focus, and platform maturity
progression. It does not represent committed release timelines.

---

## Platform Maturity Model

Synapse Optical is designed to evolve across a defined operational maturity arc. Each stage
builds on the foundation of the previous one. No stage introduces autonomous execution
without the deterministic validation and human approval controls established in earlier stages.

```
Stage 1 — Read-only diagnostics and operational visibility
Stage 2 — Deterministic validation and change preview
Stage 3 — Human-approval-gated controlled execution
Stage 4 — Post-change verification and audit
Stage 5 — Multi-vendor workflow normalization
Stage 6 — Enterprise governance and multi-tenant controls
Stage 7 — Operational intelligence and cross-platform analysis
```

The current proof-of-concept has progressed through Stages 1–5 for the primary vendor
platforms. Stages 6 and 7 represent the near-term and longer-term development direction.

---

## Phase 0 — Foundation (Current PoC)

### Fortinet FortiGate

- IPsec VPN troubleshooting workflows (Phase 1 / Phase 2, DPD, NAT-T, selectors)
- Firewall policy analysis and validation
- Address object management workflows
- Static route management
- Interface configuration workflows
- NAT rule management
- User, admin, RADIUS, and user group management
- Configuration snapshot and change history
- REST API and SSH dual-transport execution

### Palo Alto Networks PAN-OS

- Configuration snapshot, parse, and normalization
- Security policy analysis and validation
- Address object, static route, NAT rule, and service object workflows
- Interface configuration workflows
- IPsec VPN commissioning workflows (IKE/IPsec profiles, IKE gateway, tunnel interface,
  security policies, static route — full commit lifecycle)
- VPN diagnostics (Phase 1 / Phase 2, IKE gateway, tunnel state)
- Log retrieval and analysis
- XML API change lifecycle with lock, commit, and discard controls

### Multi-Vendor Operational Capabilities

- Cross-device VPN diagnostics (FortiGate ↔ PAN-OS and same-vendor pairs)
- Vendor-agnostic routing protocol template support (BGP and OSPF across both platforms,
  with unsupported-feature reporting per vendor)
- Device relationship detection and topology awareness
- Vendor plugin architecture designed for zero-core-change expansion

### Change Lifecycle and Operational Safety

- Five-state change lifecycle: pending review → approved → executing → verifying → completed
- Human approval gate before any production-impacting execution
- Post-change verification with per-step validation
- Rollback-capable audit records with full operator attribution
- Deterministic command generation — AI never outputs device commands directly

### AI Assistance Layer

- Structured natural language to intent translation (advisory, not execution authority)
- AI-assisted log analysis and correlation with engineer-review-only change hints
- AI-assisted workflow prerequisite checks (advisory warnings, never execution blockers)
- AI positioned strictly as an operational reasoning layer above a deterministic core

### Audit and Observability

- Full execution tracking with workflow history
- Operator attribution on all change records
- Validation summaries and verification results persisted per change
- Audit log designed for compliance and operational traceability

---

## Phase 1 — Multi-Vendor Expansion

### Cisco Platform Integration

- Cisco ASA security platform workflows
- Cisco IOS / IOS-XE routing and switching workflows
- Cisco NX-OS data center switching workflows
- VPN, policy, and routing workflow parity with existing vendor support

### Vendor Plugin Maturation

- Expanded BAU template coverage across all supported vendors
- Routing protocol adapter expansion for Cisco platforms
- Improved structured data retrieval and operational telemetry collection
- Expanded REST API integrations where vendor platforms support them

### Workflow Enhancements

- Guided troubleshooting workflows with structured evidence collection
- Policy consistency analysis across devices
- VPN commissioning workflow expansion and hardening
- Expanded validation logic for edge cases and platform-specific constraints

---

## Phase 2 — Platform Hardening and Production Readiness

This phase addresses the progression from proof-of-concept to production-grade platform.

### Operational Hardening

- Multi-tenancy activation (foundation scaffolded in current PoC)
- RBAC hardening with role-based workflow access controls
- Session security and authentication hardening
- API rate limiting, input validation, and operational resilience improvements
- Secure connector architecture for device access

### Integration Surface

- ITSM integration concepts (ServiceNow, Jira Service Management)
- SIEM forwarding for audit and change events
- CMDB synchronization for device inventory
- Webhook and event notification framework

### Operational Visibility

- Platform health and observability telemetry
- Workflow execution metrics and reliability tracking
- Validation engine performance monitoring

---

## Phase 3 — Enterprise Governance

### Multi-Tenant Operational Model

- Tenant isolation for devices, workflows, changes, and audit records
- Tenant-scoped RBAC with delegated administration
- Cross-tenant operational visibility controls for platform operators

### Approval Workflow Enhancements

- Multi-stage approval chains for high-risk changes
- Approval delegation and escalation controls
- Scheduled execution with pre-approved maintenance windows
- Emergency change bypass with mandatory post-hoc audit

### Compliance and Audit Controls

- Immutable audit log hardening
- Compliance-oriented reporting concepts
- Change evidence packaging for audit and review
- Retention and archival controls

### Enterprise Identity Integration

- SSO / SAML integration
- Identity-provider-based role mapping
- Session and access policy controls

---

## Phase 4 — Operational Intelligence

### Topology-Aware Operations

- Topology-aware workflow context (path-aware change impact assessment)
- Traffic path reasoning across multi-vendor environments
- Dependency analysis for configuration changes

### Configuration Intelligence

- Configuration drift detection and alerting
- Cross-platform policy consistency analysis
- Operational dependency mapping

### Advanced Diagnostics

- Correlation of symptoms across multiple devices and platforms
- Root-cause analysis workflow assistance
- Proactive operational health checks

### Hybrid Cloud Networking

- AWS hybrid networking connectivity validation
- Cloud-to-on-premises VPN workflow support
- Routing and segmentation analysis for hybrid environments

---

## Known Constraints (Current PoC)

The current proof-of-concept reflects deliberate scope decisions appropriate for a
single-tenant, single-operator development environment.

Known constraints that will be addressed in Phase 2:

- Single-tenant architecture — multi-tenancy scaffolded but not yet activated
- Single-operator workflow model — no multi-user approval chains yet
- PoC-grade authentication — production session security hardening planned
- Some device interaction paths use SSH fallback where REST API coverage is incomplete
- No external integrations (ITSM, SIEM, CMDB) in current PoC scope

These are intentional PoC boundaries, not architectural limitations. The platform is
designed for clean promotion to a production SaaS model.

---

## Multi-Vendor Platform Status

| Platform | Status |
|---|---|
| Fortinet FortiGate | ✅ Current focus |
| Palo Alto Networks PAN-OS | ✅ Current focus |
| Cisco ASA | 📋 Planned |
| Check Point Gaia | 📋 Planned |
| Juniper Junos OS | 📋 Planned |
| FortiAnalyzer | 📋 Planned |
| Cisco IOS / IOS-XE / NX-OS | 📋 Planned |
| Hybrid cloud networking (AWS) | 📋 Planned |

---

## Development Philosophy

Synapse Optical prioritizes:

- **Operational safety** — deterministic validation and human approval before execution
- **Vendor-aware workflows** — behavior adapts to platform capabilities and constraints
- **Auditability** — every change is tracked, attributed, and verifiable
- **Deterministic command generation** — AI translates intent; templates generate commands
- **Engineering practicality** — incremental capability growth grounded in operational realism
- **Enterprise credibility** — platform design reflects the realities of production infrastructure

Synapse Optical is not an autonomous infrastructure agent. It does not generate device
commands from AI output, bypass human approval for production changes, or position
probabilistic reasoning as a substitute for deterministic operational control.

AI accelerates operational reasoning. Deterministic systems control operational execution.

---

## Public Repository Scope

This repository intentionally excludes proprietary implementation details, including:

- Internal orchestration logic and change pipeline implementation
- AI prompt systems and intent parsing boundaries
- Vendor command generation templates
- Backend execution systems and rollback implementation
- Production deployment architecture
- Implementation-specific intellectual property

Public documentation focuses on architectural direction, workflow concepts, operational
methodology, and platform maturity progression.
