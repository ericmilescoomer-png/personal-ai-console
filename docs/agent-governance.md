# Agent Governance

Personal A.I. Console&trade; (PAC) lets the command agent, Kora, operate **other agents**: specialized workers that handle recurring or domain-specific work. That capability is exactly where an agentic system usually gets dangerous: agents that grant themselves powers, promote themselves, edit live systems, or quietly coordinate out of view.

PAC governs those agents with the same discipline it applies to Kora herself. This document describes how, at a product level: no source, no schemas, no configuration. See [NOTICE.md](../NOTICE.md).

For the higher-level trust architecture, see [trust-model.md](trust-model.md); for how a single task flows, see [how-it-works.md](how-it-works.md).

---

## One kind of worker, defined explicitly

An agent in PAC is defined by a small, explicit set of properties, the same shape the industry has converged on: a **name**, a **description**, a **charter** (what it is for, in the owner's words), a **capability set** (what it may do), a **trigger** (when it runs), a **brain** (which model reasons for it), and an **autonomy** setting. Nothing about an agent's authority is implied by its title.

An earlier build carried a second worker class: single-purpose "helpers" walled off from mission work. That split was deliberately retired: its only mechanical effect was gating which workers could produce a mission deliverable, and encoding that as a species turned out to be ontology where a property belonged. Every agent is mission-eligible now; the historical records of the retired class remain readable in the audit trail.

No agent is a general process that "figures out what to do." Each is scoped to a job by its charter and bounded by its grants.

---

## Authority is granted, not claimed

An agent does **not** carry its own list of powers. It holds exactly the **capability set the Owner granted it**, chosen from a preset as a starting point or assembled item by item, with the grant's provenance recorded either way.

- The granted set is the agent's entire authority. There is no "and also" the agent can reach for.
- Enforcement is **fail-closed**: an agent attempting a capability outside its granted set is refused at execution time, intersected with the same SAFE / SENSITIVE / FORBIDDEN tier gate every action passes through. If the grant set cannot be resolved, the refusal is the answer.
- An agent with **zero capabilities is valid**: it can converse and advise, and can do nothing else. Least privilege starts at nothing.
- This is least-privilege by construction: an agent built to summarize documents cannot reach a capability that touches the network or the filesystem, no matter what it proposes.

A plan an agent produces is still subject to the deterministic policy gate. The grant narrows what the agent may attempt; the gate decides whether a given attempt proceeds. Neither can be talked out of its decision by the model.

---

## Trust is earned on a measured ratchet

Agents graduate through a lifecycle (**draft &rarr; trial &rarr; active &rarr; proven**) and the gates are numeric, recorded, and enforced (see [engineering-discipline.md](engineering-discipline.md)):

| Transition | Requirement |
|---|---|
| Trial &rarr; Active | A run of consecutive successful trial runs (default: 5 in a row) |
| Active &rarr; Proven | At least 20 total runs **and** a success rate of at least 95% |

An Owner can override a promotion, but the override is **recorded as a governed event**; the exception is visible, not silent. An agent cannot promote itself, and "trusted" is a status backed by behavior, not a label it can assign.

---

## Propose, don't apply

PAC's most pointed rule for self-improvement: **the system proposes changes; the Owner disposes of them.**

When an agent does code-oriented work, it runs inside a **throwaway, isolated workspace**: a disposable copy, never the live system. Its deliverable is a **proposed change** for the Owner to review, not an edit to anything running.

**No agent holds a capability to apply a change to live code.** The path from "an agent suggested this" to "this is now running" goes through the Owner. There is no automated bridge across that gap.

---

## The red zone: off-limits to any automated write

A specific set of things can **never** be modified through any automated path (not by an agent, not by Kora, not even with Owner approval routed through the agent surface):

- The policy and capability definitions themselves
- Secrets and credentials
- The system prompts and identity that define the agents
- The audit and receipt record

These are the parts that *define and prove* the trust boundary. Letting the system rewrite them automatically would mean the boundary could quietly move. So that door is closed in code. Changes to the red zone are Owner actions taken deliberately and outside the agent surface.

---

## No gossip between agents

Agents do not message each other, and they do not message Kora directly. The coordination model is deliberately boring:

- An agent **publishes** its typed output and its receipts.
- PAC Core **records** that output as evidence.
- **Kora routes** a filtered context bundle to whoever needs it: not the full history, and never an open channel between workers.

Before Kora relies on an agent's result, it passes a **validation gate**: the evidence must actually exist, it must support the claim, it must respect the capability tier and posture, and the deliverable must be in scope. Anything that fails is flagged for the Owner, not silently accepted. There is no agent-to-agent mesh to compromise, and no path for one worker to smuggle instructions to another. (See [owasp-agentic-mapping.md](owasp-agentic-mapping.md), ASI07.)

---

## What this does *not* claim

- **Intelligent agent routing** (Kora choosing the right agent automatically from a task description) is product direction, not a current property. Today, delegation is to a named agent.
- An agent can carry a **brain preference**: the local model by default, or an owner-hired cloud brain at the artifact boundary (see the [README](../README.md#whats-built)). Different *local* models per worker are on the [roadmap](roadmap.md), not shipped.
- Adding a multi-agent worker mesh in the future would reintroduce inter-agent surface that this single-owner, publish-and-route design currently avoids, and would require revisiting the guarantees above.

---

*See also: [trust-model.md](trust-model.md) &middot; [how-it-works.md](how-it-works.md) &middot; [engineering-discipline.md](engineering-discipline.md) &middot; [threat-model.md](threat-model.md) &middot; [NOTICE.md](../NOTICE.md)*
