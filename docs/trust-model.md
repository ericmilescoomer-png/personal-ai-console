# Trust Model

Personal A.I. Console (PAC) is built on a simple premise: powerful AI should make the owner more capable without making them less in control. This document explains *how* that control is kept — the trust architecture that sits between the model and the work.

It is written at a product level. It does not include source code, schemas, endpoints, or configuration. For the layered architecture, see [architecture.md](architecture.md); for what is built versus in progress, see the [README](../README.md).

---

## Why "Personal" Means Control

In Personal A.I. Console, *personal* is not a category of tasks. It is a control model.

The system is designed around one person — the **Owner** — who holds the machine, the data, the memory, the permission boundary, and the final call. The assistant becomes more capable over time, but authority does not drift to the model, to a vendor, or to the cloud. The owner stays the authority.

Everything below is in service of that one idea.

---

## One Owner

There is one Owner per system.

- The Owner grants authority. Nothing the system can do exists outside what the Owner has delegated.
- The Owner approves sensitive actions before they execute.
- The Owner decides what the system is allowed to do, and can withdraw that at any time.

**Kora** — the command agent inside PAC — plans and executes *within* the authority the Owner has granted. Kora is not the authorization boundary. The boundary is enforced in code by PAC Core, not by convention and not by the model's good behavior.

```text
Owner            grants authority, approves, decides
  │
Kora             plans and executes within granted authority
  │
PAC Core         enforces the boundary in code
  │
Model            provides reasoning — never decides its own permissions
```

---

## Capability Tiers

Every step that touches the system is classified into one of three tiers. **The model never decides its own tier** — tier assignment lives in PAC Core's capability registry, not in the model's output.

| Tier | Behavior |
|---|---|
| **SAFE** | May execute without owner confirmation; still governed and still leaves evidence. |
| **SENSITIVE** | Requires explicit owner confirmation before execution. |
| **FORBIDDEN** | Blocked in code. Not a warning — a wall. |

This is the heart of "more capable without less in control." SAFE work flows. SENSITIVE work pauses for the owner. FORBIDDEN work does not happen, regardless of how the request is phrased.

---

## Approval Gates

Sensitive work does not execute on the model's say-so. It stops and waits for the owner.

A plan is drafted, each step is tagged with its tier, and the plan is previewed to the owner *before* anything sensitive runs. The owner sees what was requested, what is proposed, and what will require their confirmation — and then approves, declines, or adjusts.

The model can propose. Only the owner can authorize the sensitive parts.

---

## Postures

The system runs under three formal **postures**. Posture changes what is allowed at the boundary — it is a system-wide stance, not a per-action setting.

| Posture | Meaning |
|---|---|
| **Sovereign** | No outbound. Local operation only. |
| **Connected** | Time-bounded, owner-authorized outbound through governed paths. |
| **Maintenance** | Time-bounded maintenance or elevated system work. |

Connected and Maintenance are **time-bounded, owner-authorized states**, not perpetual modes the system can quietly settle into.

**Degraded conditions are not a posture.** When the network or system is degraded, that condition is *surfaced as operational state* so the owner can see it — but a degraded condition never becomes permission to bypass posture rules. The system fails toward caution, not toward convenience.

---

## Owner-Controlled Memory

PAC treats long-term memory as **owner-controlled state, not model-owned state.**

- Kora may *retrieve* permitted memory as context, and may *suggest* that something be remembered.
- Kora does **not** write memory on her own. Memory creation, promotion, and deletion happen only through owner-controlled paths — Kora has no self-serve capability to persist memory through her own action surface.
- Nothing is silently logged into long-term memory as a side effect of a conversation.

Beyond *who* may write memory, PAC governs *what each memory is worth trusting*:

- **Provenance and trust metadata.** Every memory carries where it came from — an **authority chain** (owner-stated, owner-approved, observed pattern, agent-derived, or imported) — alongside a confidence level and when it was last confirmed or used. A fact the owner stated and a pattern an agent merely noticed are not treated as equally authoritative.
- **Consolidation is a proposal, not an edit.** When memory accumulates duplicates, conflicts, or stale entries, the system *proposes* a cleanup (merge / resolve-conflict / retire-stale) for the owner to review. It does not silently rewrite or delete what it thinks is redundant. Originals are soft-retired, never destroyed on the system's own judgment.
- **Memory spaces.** Memory is organized into owner-defined spaces, and retrieval is scoped — so context from one area of life isn't silently pulled into an unrelated one.
- **Portability and rollback.** The owner can export and import their memory set, with versioned history and rollback. The record is the owner's to move, inspect, and revert.

Memory also carries a sensitivity level. The most sensitive ("vault") memory is never surfaced automatically during ordinary chat — it takes an explicit owner instruction to bring it forward. Personal context is treated as context, never as policy.

> Note: PAC also has an internal *resource* memory governor that manages hardware memory pressure (RAM and VRAM) before running work. That is a separate, hardware-level concern and is unrelated to the owner-controlled memory described here. The two share a word, not a meaning.

---

## Evidence and Receipts

Personal A.I. Console treats the absence of a receipt as the absence of an action.

- **Receipts.** Governed work that affects the system or produces a deliverable is captured in a receipt, lifecycle-tracked from proposal through owner confirmation, execution, and verification. As far as the system is concerned, *an action without a receipt didn't happen.*
- **Audit trail.** Governed actions, policy decisions, and tool invocations write to an append-only audit trail kept separate from the main working data.
- **Local evidence.** A separate runtime layer (PAC OS) observes machine state and writes structured evidence the system can read when reasoning about what is currently true — so decisions are grounded in observed reality, not assumption.

Read-only work does not generally produce receipts. Receipts exist to make *consequential* work inspectable after the fact, by the owner, without trusting anyone's memory of it.

---

## The Model Is Replaceable

The model provides reasoning and language. It does not provide authority, memory, or accountability — those live in the system around it.

Kora's continuity — her receipts, mission history, standing orders, and the owner's preferences — lives outside the model. Swap the model, and Kora persists. This is deliberate: tying trust to a specific model would make trust as replaceable as the model. PAC ties trust to the owner-controlled system instead.

The current reference build reasons with a local model provider (Ollama, running Qwen). Model choice is configurable. The model is a component, not the product.

---

## The System Prompt Has a Floor

The owner can edit how Kora is framed — tone, standing preferences, working style — through an editable base system prompt. But that editable layer sits on top of a **protected grounding floor** the owner's edits cannot reach.

- The **grounding floor** holds the non-negotiable honesty constraints: don't invent system behavior, know when state data is missing rather than guessing, never present stale data as live. These are the same invariants the rest of this document defends — and they are kept out of the editable surface so they cannot be tuned away, by the owner *or* by anything that reaches the owner's editing surface.
- The editable base prompt is **versioned, revertible, and receipted** — every change is recorded, and any prior version can be restored. The framing is adjustable; the change history is evidence.

The effect: you can shape *how* Kora talks and what she prioritizes, without being able to talk her out of telling you the truth about the system.

---

## Oversight That Can't Be Delegated Away

PAC OS runs a set of runtime monitor agents. Control over them is intentionally split into two surfaces:

- An **Owner control surface** — the full set of agents the owner can see, restart, disable, or retune.
- A smaller **delegable surface** — the subset Kora may act on through governed, policy-checked capabilities.

Kora **cannot restart the agents that observe her own behavior, evaluate her learning, or alert on her actions.** The split is enforced in code, so delegated authority can never quietly expand to cover the watchers. The delegate does not get to silence the oversight.

---

## What This Trust Model Does *Not* Claim

To keep this honest:

- It does **not** claim hardening for public internet exposure or multi-user hosting. PAC is designed for local, single-owner deployment. Internet-exposure hardening is future work, not a current property.
- It does **not** claim formal security certification, third-party audit, or compliance attestation.
- It does **not** claim cross-platform validation. The validated platform is Windows.
- It does **not** claim that every byte the system reads produces a receipt — receipts cover *governed, consequential* work, not lightweight reads.
- It describes the product's trust architecture, not its implementation. It is not a specification for re-implementation and does not represent the actual schemas, interfaces, or configurations of the private build.

---

## Summary

Personal A.I. Console is "personal" because the owner stays in control of it.

That control is not a promise in a README — it is built into how the system works: one owner, code-enforced capability tiers, approval gates on sensitive work, formal postures that fail toward caution, owner-controlled memory, receipts and audit for consequential work, a replaceable model, and oversight the delegate cannot switch off.

The model gets more capable. The owner stays in charge. That is the whole point.

---

*See also: [README](../README.md) · [architecture.md](architecture.md) · [NOTICE.md](../NOTICE.md)*
