# Glossary

Personal A.I. Console&trade; (PAC) uses a specific vocabulary. This glossary defines the terms as they are used across this repository. For how they fit together, see [architecture.md](architecture.md) and [trust-model.md](trust-model.md).

---

## Product and components

**Personal A.I. Console** — the product. A local-first AI command layer for a single owner, built so the system around the model &mdash; not the model itself &mdash; is the product.

**PAC** — the abbreviation for Personal A.I. Console.

**PAC Desktop** — the current private desktop implementation of Personal A.I. Console.

**Kora** — the command agent inside PAC. She reads evidence, drafts plans, requests approval when needed, executes through governed capabilities, and writes receipts. Kora's authority comes from the owner's delegation and the policy layer, not from the model.

**PAC Core** — the control plane. Holds policy, the capability registry, plan and mission lifecycle, the receipt spine, the memory governor, and the audit trail. PAC Core is the code-enforced authorization boundary.

**PAC OS** — the evidence plane. A local runtime of monitor agents that observe machine state and write structured evidence Core can read. It observes and reports; it does not decide policy.

**Local model provider** — the replaceable reasoning component. The reference build uses Ollama running Qwen; the model is configurable.

---

## Roles

**Owner** — the one person who owns the machine, the data, and the authority. The Owner grants capability, approves sensitive actions, and makes the final call. There is one Owner per system.

---

## The work loop

**Mission** — a unit of meaningful, accountable work with a visible lifecycle. Not every chat message becomes a mission.

**Observation** — reading available evidence (machine state, memory, prior receipts, documents) before acting.

**Plan** — a sequence of steps Kora drafts, each tagged with a capability tier.

**Approval** — the gate where SAFE steps may proceed and SENSITIVE steps require explicit Owner confirmation.

**Execution** — running approved steps through governed capabilities.

**Receipt** — the evidence record of work that executed, lifecycle-tracked from proposal through verification.

**Deliverable** — the typed output a completed mission produces (e.g., report, research, draft, status, audit).

---

## Authority and capability

**Capability** — a governed action PAC can perform, registered with a fixed tier. The model cannot invent capabilities or change their tier.

**Capability tier** — the risk classification applied to every step that touches the system:
- **SAFE** — may execute without owner confirmation; still governed and recorded.
- **SENSITIVE** — requires explicit owner confirmation before execution.
- **FORBIDDEN** — blocked in code.

---

## Posture and state

**Posture** — a formal, system-wide stance that changes what is allowed:
- **Sovereign** — no outbound; local operation only.
- **Connected** — time-bounded, owner-authorized outbound through governed paths.
- **Maintenance** — time-bounded maintenance or elevated system work.

**Degraded condition** — a network or system problem that is surfaced as operational state. It is **not** a posture, and it never becomes permission to bypass posture rules.

---

## Evidence and memory

**Audit trail** — an append-only record of governed actions, policy decisions, and tool invocations, kept separate from the main working data.

**Local evidence** — structured observations PAC OS writes about machine state, which Core can read to reason about what is currently true.

**Memory governance** — the principle that long-term memory is owner-controlled state. Kora may suggest memory changes but cannot create, promote, or delete memory on her own.

**Memory governor** — a separate, hardware-level concern: managing system resource memory (RAM/VRAM) before running work. Distinct from owner-controlled (semantic) memory; the two share a word, not a meaning.

---

*See also: [README](../README.md) · [architecture.md](architecture.md) · [trust-model.md](trust-model.md) · [NOTICE.md](../NOTICE.md)*
