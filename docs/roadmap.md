# Roadmap

This roadmap separates what Personal A.I. Console&trade; (PAC) is **today** from where it is **headed**. It is deliberately honest about scope: PAC is an active private prototype, and most items below "Now" are direction, not shipped features.

For current detail see the [README](../README.md); for architecture and trust see [architecture.md](architecture.md) and [trust-model.md](trust-model.md).

---

## The discipline: earned autonomy

PAC's capability grows in a strict sequence. Each rung must be solid before the next is allowed to matter:

1. **Observability** — evidence exists, is timestamped, is fresh, and is surfaced to the owner.
2. **Policy** — capability tiers are enforced in code; nothing runs without a policy evaluation.
3. **Proposals** — structured plans with evidence, scope, and a risk summary.
4. **Approvals** — the owner consents explicitly; never inferred.
5. **Execution** — approved actions run within declared scope, with a receipt.
6. **Verification** — deterministic checks confirm the outcome before the receipt closes.

The current build is solid through **Approvals (rung 4)**. **Execution sandboxing and Verification (rungs 5–6)** are what unlock next. The roadmap below is organized around extending that foundation, not around adding ungoverned capability on top of it.

---

## Now — current build

Working today on a local, owner-controlled, Windows-validated build:

- Local-first command console (chat, home, agents, library, Kora workspace, settings).
- **Kora**, the command agent: observes, drafts plans, requests approval, executes governed capabilities, and writes receipts.
- The work loop — Observation → Plan → Approval → Execution → Receipt → Deliverable.
- Code-enforced capability tiers (**SAFE / SENSITIVE / FORBIDDEN**) and formal postures (**Sovereign / Connected / Maintenance**).
- Action receipt spine and an append-only audit trail, separate from the main data.
- Owner-controlled memory; a local document repository, episodic memory, and local embeddings.
- A configurable local model provider (Ollama, running Qwen in the reference build).
- A local runtime substrate (PAC OS) of monitor agents producing structured evidence.
- A durable single-owner key/access model for local operation.

---

## Near-term — public showcase

Making the project visible while keeping the engine private:

- Redacted screenshots (in place) with captions.
- Public-safe trust model, glossary, roadmap, and example artifacts.
- A short demo walkthrough once a clean end-to-end demo is recorded.

---

## Product direction

Direction, not shipped features. Grouped by the two families the system is built around: **mission work** the owner assigns, and **ambient infrastructure** that accumulates value in the background.

### Mission expansion

- **Deliverable drafting** — Kora drafts long-form output (document, report, draft) into a mission deliverable, optionally saved to the local library.
- **Governed web research** — a new governed capability set (search, fetch, extract, source storage, citations) under the same tier system, gated to Connected posture. A substantial lift, not a quick add.
- **Mission templates** — reusable mission shapes for recurring patterns.
- **Mission inspector** — a context-aware panel showing a mission's request, plan, steps, artifacts, receipts, and journal in one place.
- **Multi-step / multi-agent missions** — longer-running work and mission-to-mission dependencies, only as real workflows demand.

### Ambient infrastructure

- **Re-entry brief** — on return from idle, a concise, sourced handoff: what happened, what's pending, what changed, what's awaiting attention.
- **Experience search** — meaning-based retrieval across journal, receipts, deliverables, and ingested documents, with provenance and freshness on everything.
- **Preference learning** — Kora learns from the work loop (what's approved, deferred, read) into inspectable, owner-deletable preferences — from a documented trail, not model fine-tuning.
- **Context signatures** — lightweight daily fingerprints of how time was spent, enriching mission context.
- **WAN awareness** — graceful handling of connectivity changes; offline-first, with online-only work deferring and resurfacing automatically.

### Reach

- **Standing watches** — owner-defined watches that monitor sources and surface intel under policy, with receipts.
- **Focus areas** — owner-defined contexts ("desks") that tune Kora and filter content for a domain, without separate installations. A lens over one database, not isolated workspaces.
- **Specialized agent workers** — task-specific agents, potentially running different models per worker.
- **Deployment profiles** — bringing the same governance spine to other hardware:
  - **PAC Lite** — entry-level hardware (≤16GB VRAM or CPU-only); a small model in both lanes; the bar is *working*, not *fast*.
  - **PAC Pro** — prosumer hardware (24–32GB VRAM); mid-range models, possibly split fast/ops lanes.
  - (The current build is the offline reference profile.)
- **Connected-posture hardening** — strengthening the system for connected and, eventually, more-exposed deployments, including stronger key/secret backing. (See "Out of scope" for the current boundary.)
- **Smart-home / IoT control plane** — the project's origin, returning as a governed surface.

---

## Out of scope (for now)

Explicitly **not** current capabilities, and not on the near-term path:

- **Public internet exposure / hardened remote deployment.** PAC is built for local, single-owner use. Internet-exposure hardening is future work, not a current property.
- **Multi-operator / multi-seat / hosted deployments.** PAC is single-owner by design.
- **Cloud-hosted model providers.** PAC is provider-agnostic; if cloud models are ever used, they would arrive through the governed connector/broker system under policy — not as a default.
- **Cross-platform validation** beyond Windows.
- **A public source-code release.** This repository is a product showcase, not an open-source distribution — see [NOTICE.md](../NOTICE.md).

---

*Roadmap items are subject to change. Nothing here is a commitment or a shipped feature unless it appears under "Now."*
