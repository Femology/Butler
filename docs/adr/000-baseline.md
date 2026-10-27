# ADR 000: Source-of-Truth Baseline and Architectural Gap Analysis

**Date:** 2026-10-27  
**Status:** Accepted  
**Deciders:** Anas Abubakar (@Femology), Antigravity Agent  
**Context:** TED — The Butler | Phase P00 Execution  

---

## 1. Context & Problem Statement

TED is a sovereign, owner-controlled personal AI operating system encompassing 500 discrete functional requirements organized into 25 domains. The target architecture is codified in docs/ARCHITECTURE.md (v4.0) and guided by the non-negotiable constitution in AGENTS.md.

We inherit the existing Femology/Butler repository (commit 9bd869d792bc5df06aed3677a871dc5b21c255a1) as the implementation starting point. Before any code mutations or architectural transformations occur, we must establish a verified baseline of the existing codebase, document structural divergences from TED Architecture v4.0, and freeze the 500-feature traceability matrix.

---

## 2. Decision

1. **Monorepo Baseline Acceptance**: We accept Femology/Butler as the baseline codebase on branch phase/p00. No working functionality (voice bridge, chat, notes, delegations, OAuth connectors) will be mutated during Phase P00.
2. **500 Feature Freeze**: The 500 feature IDs (TED-001 through TED-500) codified in eatures/TED_500_traceability.csv are canonical contracts. Each ID maps to exactly one domain and one phase owner (P27 through P51).
3. **Architectural Gap Classification**: We classify all divergences between the current Butler implementation and target TED Architecture v4.0 into explicit migration paths:
   * **Identity & Authentication**: Current Butler uses Firebase Google OAuth. Target architecture mandates a single-owner /enter code-entry challenge with WebAuthn/FIDO2 passkey hardware pairing. *Resolution: Firebase auth coexists behind a migration flag during early phases until native passkey enrollment is introduced in P05.*
   * **AI Execution Layer**: Current Butler uses direct REST calls to Google Gemini (gemini-3.5-flash) and WebSocket live audio (gemini-3.1-flash-live-preview). Target architecture mandates the Antigravity CLI (gy) headless worker running with Google AI Pro subscription entitlement on the host, supervised by a Go task manager. *Resolution: Documented in ADR 000; adapter developed in research lab will be integrated in P12.*
   * **Job Execution & Event Bus**: Current Butler executes actions synchronously inside HTTP request cycles. Target architecture mandates River job queues backed by PostgreSQL and Server-Sent Events (SSE) for UI reactivity. *Resolution: Scheduled for P04/P11.*
   * **Design Language**: Current Butler uses a dark/gold theme. Target architecture mandates the **Obsidian Atelier** design system (#11100F canvas, #1C1A18 surface, #F6F0E8 text, copper #D88A56, jade #A8CBA9 - strictly zero blue). *Resolution: Scheduled for P07/P08.*
4. **Target Host Verification**: Oracle Cloud ARM64 instance (Server 1 London, 140.238.102.221) is verified with 16 GB available RAM, 147 GB NVMe disk, Docker 29.9.2, and Coolify 4.5.0. Bare-metal Go and Antigravity CLI on the host will be provisioned in targeted phases; the worker remains OFF during P00 per constitution.

---

## 3. Consequences

* **Positive**: Full traceability from baseline commit 9bd869d to target 500-feature operating system without architectural drift or lost context.
* **Positive**: Absolute adherence to AGENTS.md stop-the-line rules; no fabricated green CI or premature phase advancement.
* **Trade-off**: Requires running dual auth pathways (Firebase legacy + native passkey) during transitional phases until full cutover in P05.
