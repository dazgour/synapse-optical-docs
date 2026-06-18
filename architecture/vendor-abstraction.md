# Vendor Abstraction Model

## Overview

Synapse Optical is designed around a vendor-aware abstraction architecture that supports operational workflows across multiple infrastructure platforms through a consistent plugin contract, a shared configuration model, and a template-based execution system.

The goal is to provide a consistent operational experience — from intent parsing and validation through to change execution and post-change verification — while fully encapsulating vendor-specific behavior within isolated plugin implementations.

The platform does not hide platform differences entirely. Vendor-specific operational behavior remains important for safe infrastructure management. The abstraction layer's role is to ensure that the platform's core logic — validation, change planning, approval, execution, and verification — operates against a shared interface contract rather than vendor-specific syntax.

---

## Design Goals

The vendor abstraction architecture is designed to:

- provide a shared operational model that allows workflows, validation logic, and change pipelines to operate without vendor-specific branching at the platform level
- fully encapsulate vendor-specific behavior within plugin implementations
- enforce a clean separation between deterministic execution logic and AI-assisted reasoning
- reduce vendor-specific operational friction for engineers
- support deterministic, repeatable validation across platforms
- enable reusable troubleshooting methodologies
- preserve vendor-aware behavior where required for operational safety
- surface vendor capability gaps explicitly rather than failing silently at runtime

---

## Abstraction Architecture

The vendor abstraction layer is built around three core components that work together to normalise operational behavior across platforms.

### VendorPlugin Interface

Each supported vendor is implemented as a plugin that satisfies a shared abstract interface. The interface defines a consistent contract covering:

- **Connection testing** — verify device reachability and credential validity
- **Configuration retrieval** — pull live configuration state from the device
- **Configuration parsing** — translate raw vendor output into the shared `NormalizedConfig` schema
- **Change plan construction** — build a structured, reviewable sequence of execution steps from validated intent
- **Execution** — dispatch change steps to the device using the appropriate transport method
- **Post-change verification** — confirm expected state after execution completes

The core platform logic is vendor-blind. Workflow engines, validation pipelines, change planning, approval gates, and audit services all operate against this interface contract, not against vendor-specific implementations. Adding a vendor requires implementing the interface and registering the plugin — no changes to core platform logic are required.

### NormalizedConfig

`NormalizedConfig` is the shared in-memory configuration schema that decouples platform logic from vendor-specific data formats. When a plugin retrieves and parses a device's configuration, the output is a `NormalizedConfig` instance regardless of vendor.

This shared schema allows:

- workflow engines to reason about configuration state without vendor-specific branching
- validation logic to apply consistent prerequisite checks across platforms
- AI components to receive a normalised configuration summary rather than raw vendor output
- cross-device diagnostic workflows to correlate state across different vendor platforms

Vendor-specific fields that have no equivalent in other platforms are accommodated through optional schema extensions, ensuring that the shared model remains intact without losing platform-specific fidelity.

### Template-Based Execution Model

Device commands are generated exclusively by server-owned, vendor-specific configuration templates. The abstraction layer enforces this boundary: AI components output structured semantic intent — never device configuration syntax. Templates translate validated, approved intent into vendor-specific commands.

This model ensures that:

- command generation is deterministic and independently testable
- vendor-specific syntax is fully encapsulated and never exposed to or produced by AI components
- the same change pipeline handles all vendors without modification
- execution output can be audited against the template that produced it

---

## Deterministic vs AI Boundaries

The vendor abstraction layer enforces a hard separation between AI-assisted reasoning and deterministic execution.

**AI components operate on intent, not syntax.** When AI assistance is used, its output is a structured representation of what the engineer intends — an action type, parameters, and confidence — not device commands, CLI syntax, XPath expressions, or REST payloads. This structured intent is then consumed as input to the deterministic pipeline.

**Templates own execution.** All device commands are produced by server-owned vendor-specific templates. This is not a design preference — it is an enforced architectural boundary. The template layer is the only component that produces executable device syntax.

**The deterministic path is always available.** Guided workflows and BAU templates bypass AI intent parsing entirely. In these paths, the change plan is built directly from structured form inputs and templates, without any LLM involvement. The same approval, execution, and verification pipeline applies regardless of whether AI was involved in generating the intent.

This boundary is what makes the platform auditable and operationally safe: every device command that reaches a vendor plugin can be traced to the template that produced it.

---

## Vendor Plugin Contract

Each vendor plugin exposes a consistent interface to the rest of the platform. The contract covers the full operational lifecycle:

| Responsibility | Description |
|---|---|
| Connection testing | Verify reachability and credential validity before any operation |
| Configuration retrieval | Pull live configuration state via the appropriate transport |
| Configuration parsing | Translate vendor-specific output into `NormalizedConfig` |
| Change plan construction | Build a structured, human-reviewable sequence of execution steps |
| Step execution | Dispatch individual steps to the device |
| Step verification | Confirm expected state for each completed step |
| Post-change verification | Run a holistic verification pass after the full change completes |

Vendor plugins are registered in a central registry. The dispatch layer resolves the correct plugin at runtime based on the device's vendor identifier. This keeps vendor-specific logic fully isolated and makes the extension model straightforward.

### Vendor Extensibility

Adding a new vendor requires:

1. Implementing the `VendorPlugin` abstract interface for the new platform
2. Registering the implementation in the vendor registry

No changes to core workflow logic, validation pipelines, AI components, the change approval system, or audit services are required. The plugin pattern is the same whether the vendor uses SSH, REST API, XML API, or a combination.

---

## Routing Protocol and Migration Abstraction

Protocol-level configuration — BGP, OSPF, and similar — is handled through a separate abstraction layer that sits between the API and the vendor plugins.

Shared protocol schemas model configuration intent in a vendor-neutral format. Vendor-specific rendering adapters translate that schema into the appropriate CLI or API syntax for each platform. Unsupported features — protocol knobs that exist in the schema but have no equivalent on a given platform — are surfaced explicitly in the output rather than silently dropped.

The same canonical-model pattern applies to the configuration migration workspace. Source vendor configs are normalised to a shared intermediate representation, which target vendor mappers render into platform-specific output. This keeps source and target parsing fully isolated and ensures the same translation pipeline handles any registered source-to-target pair.

Both patterns follow the same principle: separate intent representation from vendor rendering, and surface gaps explicitly rather than silently dropping unsupported constructs.

---

## Change Lifecycle as a Vendor-Neutral Safety Rail

Regardless of vendor, every production-impacting change follows the same lifecycle:

```
pending_review → approved → executing → verifying → completed
```

This lifecycle is enforced at the platform level, not within individual vendor plugins. A change that fails verification does not silently complete. Rollback tracking and audit records are created at each stage.

The vendor plugin is responsible for execution and verification steps. The lifecycle orchestration — approval gates, status tracking, audit logging, rollback record creation — is vendor-blind and applies uniformly across all platforms.

---

## Read-Only vs Change Operations

The platform distinguishes between diagnostic (read-only) operations and change workflows. This distinction is enforced at the architecture level, not left to convention.

Diagnostic workflows — VPN tunnel investigation, policy analysis, configuration consistency checks, route analysis — collect and correlate state without writing to devices. These workflows have no approval gate because they have no production impact.

Change workflows — VPN commissioning, interface configuration, policy updates, address object management — always enter the `pending_review → approved → executing → verifying → completed` pipeline and require explicit human approval before execution.

This distinction is preserved within the vendor abstraction layer. Plugins expose separate interfaces for read operations and write operations, making it structurally impossible for a diagnostic workflow to accidentally invoke execution logic.

---

## Device Interaction Methods

Depending on vendor platform capabilities, plugins interact with infrastructure using:

- **SSH / CLI** — used for platforms or operations where REST API coverage is incomplete
- **REST API** — preferred transport where fully supported
- **XML API** — used for platforms with XML-based management interfaces
- **Dual-transport with automatic fallback** — some platforms support both REST and SSH; the plugin attempts the preferred transport and falls back automatically if unavailable

The interaction method is abstracted from the workflow and validation layers. Workflows do not need to know whether a change is being executed via REST or SSH — that decision belongs to the plugin.

---

## Operational Safety

The vendor abstraction layer is designed to work alongside deterministic validation and operational safety controls at every stage.

**Prerequisite validation** runs before any change plan is presented for review. This includes interface and zone dependency checks, cryptographic compatibility validation, routing conflict detection, policy ordering analysis, and object naming consistency checks. These checks are vendor-aware — a validation rule that applies to one platform may not apply to another, and capability gaps are surfaced rather than ignored.

**Credential handling** is managed by the backend. Credentials are not passed through the abstraction layer in cleartext, are not accessible to AI components, and are never included in change previews or audit records presented to the UI.

**Audit logging** captures the full execution record for every change: the actor, the approved change plan, the steps executed, the verification result, and the rollback state. This record is immutable and vendor-agnostic.

**Rollback tracking** is built into the change lifecycle. The verification phase following execution is designed to detect incomplete or failed changes and associate them with a recoverable state.

---

## Current Development Focus

Current proof-of-concept development is focused on:

- Fortinet FortiGate (FortiOS 7.2.x, 7.4.x, 7.6.x via REST API and SSH)
- Palo Alto Networks PAN-OS 11.2.x (via XML API and SSH)

Planned expansion targets additional enterprise firewall platforms. See the [roadmap](./roadmap.md) for directional detail.

---

## Workflow Coverage

The abstraction layer currently supports vendor-normalised workflows across the following areas:

- VPN commissioning and troubleshooting (site-to-site and remote access)
- Firewall policy validation and management
- Address object management
- Route analysis and static routing
- Interface configuration workflows
- NAT validation
- Configuration consistency checks
- Cross-device diagnostic correlation
- Dynamic routing protocol templates (BGP and OSPF, FortiGate and PAN-OS)
- Firewall Health Assessment — read-only security and operational posture scoring across 10 assessment categories, with fleet overview and scheduled assessment support
- Configuration migration workspace — deterministic source-to-target config translation (Cisco ASA → FortiOS, FortiOS → PAN-OS); read-only, no device I/O

Vendor-specific capabilities and constraints are evaluated during workflow generation and validation. Workflows adapt their behavior to the target platform based on the registered plugin's capability profile.

---

## Public Repository Scope

This repository intentionally excludes:

- internal orchestration logic
- Jinja2 command templates and vendor-specific rendering logic
- proprietary normalization models
- backend execution pipelines
- vendor translation engines
- implementation-specific workflow logic
- private AI orchestration systems
- XPath expressions, REST payload structures, and CLI syntax

Only high-level architectural concepts, interface contracts, and sanitized workflow examples are included publicly.
