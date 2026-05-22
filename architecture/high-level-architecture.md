# Synapse Optical — High-Level Architecture

This diagram represents the current proof-of-concept direction for Synapse Optical. It shows the intended platform structure for AI-assisted network automation, deterministic troubleshooting, vendor-aware validation, and controlled operational workflows.

```mermaid
flowchart TB
    User["Network Engineer / Operator"]

    subgraph Client["Thin Web Client"]
        UI["React / TypeScript UI"]
        Dashboard["Dashboard"]
        Devices["Devices"]
        Workflows["Guided Workflows"]
        Changes["Change Requests"]
        Troubleshoot["Troubleshooting"]
        Audit["Audit Log"]
    end

    subgraph Backend["Controlled Backend API Layer"]
        API["FastAPI REST API"]
        Auth["Auth / RBAC / Session Controls"]
        Workflow["Workflow Engine"]
        ChangePlan["Change Plan Builder"]
        Validation["Deterministic Validation Engine"]
        Risk["Risk & Safety Scoring"]
        AI["AI Assistance Layer"]
        AuditSvc["Audit Service"]
    end

    subgraph VendorCore["Vendor Abstraction Layer"]
        Registry["Vendor Plugin Registry"]
        Normalized["Normalized Config Model"]
        Templates["Backend-Owned Templates"]
        Rollback["Rollback / Verification Logic"]
    end

    subgraph Vendors["Current PoC Vendor Integrations"]
        FortiGate["Fortinet FortiGate"]
        PANOS["Palo Alto PAN-OS"]
    end

    subgraph DeviceAccess["Device Interaction Methods"]
        SSH["SSH / CLI"]
        REST["REST API"]
        XML["PAN-OS XML API"]
    end

    subgraph Data["Persistence Layer"]
        PG["PostgreSQL\nDevices / Snapshots / Changes / Audit"]
        Redis["Redis\nTokens / Cache / Task State"]
    end

    User --> UI
    UI --> API

    API --> Auth
    API --> Workflow
    API --> AuditSvc

    Workflow --> AI
    Workflow --> ChangePlan
    ChangePlan --> Validation
    Validation --> Risk
    Risk --> Registry

    Registry --> Normalized
    Registry --> Templates
    Registry --> Rollback

    Registry --> FortiGate
    Registry --> PANOS

    FortiGate --> SSH
    FortiGate --> REST
    PANOS --> XML
    PANOS --> SSH

    API --> PG
    API --> Redis
    AuditSvc --> PG

    AI -. "Structured intent / explanation only" .-> ChangePlan
    Templates -. "Commands generated deterministically server-side" .-> DeviceAccess
```

## Architecture Notes

Synapse Optical is designed around a **deterministic-first operational model**. Structured diagnostic and validation logic is intended to perform predictable checks before AI-generated explanation or assistance is applied.

The AI layer is positioned as an **assistance and explanation layer**, not as an unchecked decision-maker. Its role is to help summarize findings, explain diagnostic results, assist with workflow interaction, and improve engineer efficiency.

The vendor abstraction layer is intended to normalize operational workflows across different platforms while still respecting vendor-specific capabilities, syntax, APIs, licensing constraints, and platform behavior.

Current proof-of-concept focus is on:

- Fortinet FortiGate
- Palo Alto Networks PAN-OS
- SSH-based device interaction
- REST / XML API-based device interaction
- VPN diagnostics
- Configuration validation
- Change preview and approval concepts

## Current Scope

This diagram reflects both current proof-of-concept functionality and planned architectural direction. It is intentionally high-level and does not expose proprietary implementation logic, backend orchestration code, internal prompts, credentials, or production-sensitive design details.
