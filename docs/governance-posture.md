# AI Governance Posture

Personal A.I. Console&trade; (PAC) was not built to pass a compliance checklist. It was built by someone who believed that if AI is going to act on a person's behalf, there had to be a real system of controls around it &mdash; not a disclaimer, not a terms-of-service, not a trust-me.

This document maps what PAC actually does to the vocabulary of AI governance: the frameworks, the concepts, and the controls that the field uses to evaluate whether an AI system is responsibly designed.

It is written for someone who works in AI governance, AI risk, or AI compliance &mdash; and who wants to understand what this system implements, not just what it claims.

---

## How to Read This

PAC is a local, single-owner system &mdash; not an enterprise platform, not a hosted service. The governance problems it solves are real ones, but they are scoped to that deployment target. Where enterprise patterns (multi-user, cloud-hosted, audited-by-third-parties) are different from what PAC does, this document says so plainly.

Every claim here is grounded in behavior that exists in the private build. For honesty about what is not yet built, see [roadmap.md](roadmap.md) and [owasp-agentic-mapping.md](owasp-agentic-mapping.md).

---

## Risk Classification

One of the foundational AI governance controls is **risk tiering**: not all AI actions carry the same risk, and the system should treat them differently based on that risk.

PAC implements this as a three-tier capability registry:

| Tier | Risk level | Behavior |
|---|---|---|
| **SAFE** | Routine; reversible or low-consequence | Executes without confirmation; still governed and still leaves evidence |
| **SENSITIVE** | Consequential; affects files, data, external systems, or agent state | Requires explicit owner confirmation before execution |
| **FORBIDDEN** | Out of policy | Blocked in code. Not a warning &mdash; a wall |

**The model never self-assigns its risk tier.** Tier assignment lives in PAC Core's capability registry, not in model output. A model that produces a persuasive argument for why a SENSITIVE action should be treated as SAFE does not move the tier &mdash; the registry is the source of truth, not the conversation.

This is the system's answer to one of the core AI governance questions: *who decides what the AI is allowed to do?* In PAC, the answer is the owner, at design time, in a registry the model cannot modify at runtime.

---

## Human-in-the-Loop Controls

AI governance frameworks &mdash; NIST AI RMF, EU AI Act, ISO/IEC 42001 &mdash; all converge on a common requirement: consequential AI decisions must involve meaningful human oversight. The challenge is defining what "meaningful" means in a system that is supposed to be useful.

PAC implements human-in-the-loop as a structural property, not a UI choice:

- **Approval gates are pre-execution, not post-hoc.** Before a SENSITIVE step runs, the owner sees the full proposed plan, each step's risk tier, and what specifically requires confirmation. Approval is given before anything consequential happens &mdash; not surfaced after the fact as an alert.
- **The gate is deterministic, not model-decided.** The system that evaluates whether a step requires owner confirmation is not the model. It is a policy engine in PAC Core that applies registry rules. The model proposes; it does not authorize.
- **The kill switch is fail-closed.** A persistent kill switch halts all autonomous execution immediately and survives restart. There is no grace period, no deferred shutdown, no way to work around it by rephrasing a request.
- **The approval surface is honest about what it's showing.** The owner sees the actual plan, not a summary of the plan. The approval UI is designed around the principle that a person who does not understand what they are approving has not actually approved it.

---

## Audit Trail and Accountability

A system with no record of what it did is not a governed system. Audit and accountability controls are among the most common requirements across AI governance frameworks.

PAC's accountability architecture:

- **Action receipts.** Every governed action that executes produces a receipt, lifecycle-tracked from proposal through owner confirmation, execution, and verification. The receipt captures what was requested, what was proposed, who approved it, what ran, and what was produced. An action without a receipt did not happen, as far as the system is concerned.
- **Append-only audit trail.** Policy decisions, tool invocations, and governed actions write to an audit log kept separate from the main working database. It is append-only by design; entries are not edited after the fact.
- **Development ledger.** Changes that affect security posture, capability permissions, memory semantics, or connectivity posture are recorded in a separate, append-only development ledger before the change is considered done. The ledger carries a configuration fingerprint after each entry, enabling drift detection: if the fingerprint changes without a ledger entry explaining why, the system flags it.
- **Receipted system prompt changes.** The owner can edit the base system prompt that frames the model's behavior &mdash; but every change is versioned, receipted, and revertible. The framing history is evidence.

---

## AI Risk Management Lifecycle (NIST AI RMF Alignment)

The NIST AI Risk Management Framework (AI RMF 1.0) organizes AI governance into four functions: **Govern, Map, Measure, Manage.** PAC addresses each:

**Govern** &mdash; *policies, roles, and accountability structures.*
- Single named owner with exclusive authority. Authority is not distributed or assumed; it is explicit and enforced in code.
- Capability tiers define what the AI is allowed to do. The policy is code, not prose.
- Oversight agents (PAC OS monitor layer) cannot be controlled by the agent they observe. Kora cannot restart the agents that watch her.

**Map** &mdash; *identify context, categorize risk, understand the AI's operating environment.*
- The system distinguishes between local (sovereign) operation and outbound-enabled operation, and treats them as different risk postures with different permission boundaries.
- Memory carries provenance: every memory record includes where it came from, who authorized it, and how confident the system is in it. A fact the owner stated and a pattern the model inferred are not treated as equally authoritative.
- The local evidence layer (PAC OS) provides structured, timestamped observations of the operating environment so the system reasons from observed state, not assumption.

**Measure** &mdash; *analyze, assess, and track AI risk.*
- The earned-autonomy ratchet is a measured threshold, not a judgment call. Agent promotion (draft &rarr; trial &rarr; active &rarr; proven) requires recorded performance statistics over time. An agent that has not earned trust is not granted it.
- Configuration fingerprinting detects drift in the trust-relevant configuration (system prompt, capability registry, agent set). Unexplained drift is a signal, not a silent state change.
- More than 160 contract tests pin the trust invariants &mdash; the coverage is against the governance properties, not just functional behavior.

**Manage** &mdash; *treat, respond to, and recover from AI risk.*
- The kill switch halts all autonomous execution immediately and survives restart.
- Connectivity postures fail toward caution: Sovereign (no outbound) is the default and the resting state. A degraded network condition is surfaced as operational state, not treated as permission to bypass posture rules.
- Memory consolidation is a proposal for owner review, not a silent edit. The system does not rewrite what the owner knows based on its own judgment.
- Agent lifecycle management includes structured rollback: an agent in trial can be retired without affecting the rest of the system.

---

## EU AI Act Alignment (Concepts)

The EU AI Act classifies AI systems by risk level and imposes requirements proportional to that risk. PAC is a personal, private-use system &mdash; not a commercial product or a high-risk application under the Act &mdash; but the design choices it makes track the Act's reasoning about what safe AI deployment looks like.

**Human oversight (Article 14):** The approval gate, the kill switch, and the SENSITIVE tier are structural implementations of "human oversight measures." The owner is not an approver-in-name; the system literally will not run SENSITIVE work without them.

**Transparency and information to users (Article 13):** The plan preview shows the owner what is proposed and what requires their decision before execution. The receipt shows them what ran. The audit trail shows what the system decided and why. Opacity is not a design option.

**Accuracy, robustness, and cybersecurity (Article 15):** The input firewall sanitizes untrusted content before it reaches the model. The secret scanner redacts sensitive patterns. The filesystem guard blocks sensitive paths. These are explicit defenses, not assumed properties.

**Data and data governance (Article 10):** Memory is owner-controlled, provenance-tagged, and never silently modified. The owner can export, inspect, and roll back their memory set. What the system knows about the owner belongs to the owner.

*This is a design-level mapping, not a compliance certification or legal opinion. PAC is a private prototype, not a commercial AI system subject to regulatory certification.*

---

## OWASP Agentic AI Top 10

PAC has a full self-assessment against the OWASP Top 10 for Agentic Applications (2026). See [owasp-agentic-mapping.md](owasp-agentic-mapping.md) for the detailed mapping, including honest identification of gaps.

The short version: the two items marked fully addressed by core design are **Tool Misuse (ASI02)** &mdash; the capability tier registry &mdash; and **Rogue Agents (ASI10)** &mdash; the oversight split that prevents the delegate from silencing its watchers. Those are the system's hardest properties and the ones it was most deliberately designed around.

---

## What This Document Does Not Claim

- PAC has not been externally audited, certified, or assessed by a third party. This is a design-level self-assessment.
- PAC is not a commercial AI product and is not subject to regulatory certification under any current framework.
- The implementation is private. Claims here are about behavior and design, not source code.
- Enterprise governance patterns &mdash; multi-user identity, ephemeral scoped tokens, formal SBOM, SOC 2 controls &mdash; are not part of the current single-owner local design.

---

## Summary

PAC was built with governance as a first-order design requirement, not a retrofit. The owner stays in control because control is enforced in code. Consequential work requires real approval because the gate is structural, not advisory. The record exists because the system treats an action without a receipt as an action that did not happen.

These are not governance-adjacent properties. They are the governance.

---

*See also: [README](../README.md) &middot; [trust-model.md](trust-model.md) &middot; [owasp-agentic-mapping.md](owasp-agentic-mapping.md) &middot; [roadmap.md](roadmap.md) &middot; [NOTICE.md](../NOTICE.md)*
