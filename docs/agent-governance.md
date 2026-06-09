# Agent Governance

Personal A.I. Console&trade; (PAC) lets the command agent, Kora, operate **other agents** &mdash; specialized workers that handle recurring or domain-specific work. That capability is exactly where an agentic system usually gets dangerous: agents that grant themselves powers, promote themselves, edit live systems, or quietly coordinate out of view.

PAC governs those agents with the same discipline it applies to Kora herself. This document describes how, at a product level &mdash; no source, no schemas, no configuration. See [NOTICE.md](../NOTICE.md).

For the higher-level trust architecture, see [trust-model.md](trust-model.md); for how a single task flows, see [how-it-works.md](how-it-works.md).

---

## Two kinds of worker

- **Specialists** own a domain and produce a mission deliverable the Owner can read.
- **Helpers** are single-purpose and emit one structured observation, not a free-roaming process.

Neither kind is a general agent that "figures out what to do." Each is scoped to a job.

---

## Authority comes from a pack, not from the agent

An agent in PAC does **not** carry its own list of powers. It is bound to a **capability pack** &mdash; a named, code-registered grant that defines exactly which capabilities the agent may use and at what trust tier.

- The pack is the agent's entire authority. There is no "and also" the agent can reach for.
- Pack enforcement is **fail-closed**: an agent attempting a capability outside its pack is refused at execution time, intersected with the same SAFE / SENSITIVE / FORBIDDEN tier gate every action passes through.
- This is least-privilege by construction: an agent built to summarize documents cannot reach a capability that touches the network or the filesystem, no matter what it proposes.

A plan an agent produces is still subject to the deterministic policy gate. The pack narrows what the agent may attempt; the gate decides whether a given attempt proceeds. Neither can be talked out of its decision by the model.

---

## Trust is earned on a measured ratchet

Agents graduate through a lifecycle &mdash; **draft &rarr; trial &rarr; active &rarr; proven** &mdash; and the gates are numeric, recorded, and enforced (see [engineering-discipline.md](engineering-discipline.md)):

| Transition | Requirement |
|---|---|
| Trial &rarr; Active | A run of consecutive successful trial runs (default: 5 in a row) |
| Active &rarr; Proven | At least 20 total runs **and** a success rate of at least 95% |

An Owner can override a promotion, but the override is **recorded as a governed event** &mdash; the exception is visible, not silent. An agent cannot promote itself, and "trusted" is a status backed by behavior, not a label it can assign.

---

## Propose, don't apply

PAC's most pointed rule for self-improvement: **the system proposes changes; the Owner disposes of them.**

When an agent does code-oriented work, it runs inside a **throwaway, isolated workspace** &mdash; a disposable copy, never the live system. Its deliverable is a **proposed change** for the Owner to review, not an edit to anything running.

**No agent holds a capability to apply a change to live code.** The path from "an agent suggested this" to "this is now running" goes through the Owner. There is no automated bridge across that gap.

---

## The red zone: off-limits to any automated write

A specific set of things can **never** be modified through any automated path &mdash; not by an agent, not by Kora, not even with Owner approval routed through the agent surface:

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
- **Kora routes** a filtered context pack to whoever needs it &mdash; not the full history, and never an open channel between workers.

Before Kora relies on an agent's result, it passes a **validation gate**: the evidence must actually exist, it must support the claim, it must respect the capability tier and posture, and the deliverable must be in scope. Anything that fails is flagged for the Owner, not silently accepted. There is no agent-to-agent mesh to compromise, and no path for one worker to smuggle instructions to another. (See [owasp-agentic-mapping.md](owasp-agentic-mapping.md), ASI07.)

---

## What this does *not* claim

- **Intelligent agent routing** &mdash; Kora choosing the right specialist automatically from a task description &mdash; is product direction, not a current property. Today, delegation is to a named agent.
- Specialized workers running **different models per worker** are on the [roadmap](roadmap.md), not shipped.
- Adding a multi-agent worker mesh in the future would reintroduce inter-agent surface that this single-owner, publish-and-route design currently avoids &mdash; and would require revisiting the guarantees above.

---

*See also: [trust-model.md](trust-model.md) &middot; [how-it-works.md](how-it-works.md) &middot; [engineering-discipline.md](engineering-discipline.md) &middot; [threat-model.md](threat-model.md) &middot; [NOTICE.md](../NOTICE.md)*
