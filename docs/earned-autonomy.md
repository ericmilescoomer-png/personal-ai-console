# Earned Autonomy

*A governance framework for owner-controlled AI agents. Version 1.0, October 2026.*

Personal A.I. Console&trade; (PAC) is a product. The rules it runs on are not product-specific, and this page states them as a framework so they can be read, argued with, and applied to a system that is not PAC. Everything here is implemented in the private build and described elsewhere in this repository at the product level; this page is the same material organized the way governance frameworks are organized: principles, controls, levels, and a crosswalk to the frameworks the field already uses.

It is written at a behavior level. No source, schemas, or configuration. See [NOTICE.md](../NOTICE.md).

---

## The problem it answers

An agent is a model that can plan, call tools, and change the world outside itself. Prompt injection against such a model is not reliably preventable, and the labs building the models say so. So the governing question is not how to stop the model from being fooled. It is what the system lets a fooled model do.

If the answer is "whatever the model decides," the system has an authority problem, not a model problem. Earned Autonomy is a framework for building the system so that the answer is "ask," and so that the record of what it asked, and what it did, cannot be argued with afterward.

The framework is scoped to one principal, called the Owner, who owns the hardware, the data, and the authority. Multi-tenant and hosted deployments change the identity and isolation problems and are out of scope here.

---

## Definitions

| Term | Meaning |
|---|---|
| **Owner** | The one human principal. Grants authority, approves consequential work, and holds the final call. |
| **Operator** | The AI agent that plans and executes within the authority the Owner delegated. The operator is never the authorization boundary. |
| **Capability** | A governed action the system can perform, registered in code with a fixed risk tier. |
| **Policy gate** | The deterministic, model-free decision that runs before any capability executes. |
| **Receipt** | The evidence record of a governed action, tracked from proposal through verification. |
| **Posture** | The system-wide stance on reaching outside the machine. |
| **Mission** | Work that outlives the Owner's attention and runs as a bounded loop of passes. |
| **Verifier** | The external judge of done. It is not the worker and does not take the worker's word. |

---

## Part 1. Principles

These are the floor. A feature that would break one is redesigned, not shipped. Each is stated as a property a test can fail.

**P1. The Owner's authority is never simulated.** Approval is an act by the Owner. No operator, agent, scheduler, or background process may supply it, infer it, or inherit it.

**P2. The model holds no authority.** It proposes; policy disposes. The model never assigns its own risk tier, never widens its own permissions, and never decides whether its own proposal runs.

**P3. No claim without a receipt.** The system never states that an action occurred unless a record of it exists. A missing receipt means the action did not happen.

**P4. Freshness is part of truth.** Every reported value carries when it was observed and where it came from. Stale state is labeled last-known, never presented as current.

**P5. Failure is a delivery.** Anything that stops, whether deferred, denied, refused, timed out, or crashed, owes the Owner the same report a success owes: what was attempted, what stopped it, and what would change it. Silence is the only forbidden outcome.

**P6. Nothing leaves the machine by default.** Outbound reach is a posture the Owner opens deliberately, not a capability the model earns by asking well. The system's own internals never leave at all.

**P7. Done is judged, never declared.** Completion is decided by a verifier working from evidence assembled in code, against criteria locked before the work began. The worker's "done" is a proposal.

**P8. The delegate cannot silence its oversight.** The set of things the operator may control is a strict subset of what the Owner controls, and whatever observes, evaluates, or alerts on the operator's own behavior is outside that subset.

---

## Part 2. Controls

What a conforming system implements. Each control cites the principle it serves.

### Authority (P1, P2)

- **Tiered capability registry.** Every capability carries one of three tiers fixed in code: safe, which runs without confirmation and still leaves evidence; sensitive, which requires explicit Owner confirmation before execution; forbidden, which is blocked. The tier comes from the registry, never from the plan or the model.
- **Deterministic policy gate.** The same plan yields the same decision every time. The gate makes no model calls, judges against registry tiers rather than the plan's claims, and blocks any capability it does not recognize.
- **Pre-execution approval.** A sensitive step parks the plan until the Owner acts, with the actual plan shown, not a summary. An approval opens its door exactly once; re-approving finished work returns the recorded receipt, and unapproved work refuses to run.
- **Granted capability sets.** A delegated agent holds exactly the capabilities the Owner granted it, resolved fail-closed at execution time. An agent with no capabilities is valid; it can only converse.
- **A red zone.** Policy and capability definitions, secrets, the prompts that define the agents, and the audit record are never modifiable through any automated path, even one the Owner approved through the agent surface.

### Autonomy (P1, P8)

- **Two dials, one fence.** A per-agent autonomy level and a system-wide profile change how often the system pauses to confirm. Neither changes what is permitted. Turning autonomy up never unlocks a tier.
- **A fail-closed kill switch.** One control halts all autonomous execution immediately and survives restart.
- **Two lanes.** Work the Owner is present for stays in the conversation and answers there. Work the Owner is absent for runs on receipts and returns as a delivery carrying who did it. A plan exists only because something needs the Owner's authority; a mission exists only because the work outlives the Owner's attention. Anything that needs neither is an answer.
- **The two-surface split.** The Owner controls the whole runtime. The operator may act on a smaller delegable subset, and the agents that watch the operator are excluded from it in code.

### Reach (P6)

- **Three postures.** Sovereign, no outbound, is the default and the resting state. Limited permits an explicit allowlist. Connected is an Owner-opened window through a governed broker, standing until closed. An Owner-defined blocklist is refused in every posture.
- **Degraded is not permission.** A network or system fault is surfaced as operational state. It never relaxes posture.
- **Egress accountability.** Every outbound call records a fingerprint of exactly what left, with size, tokens, and cost. Under Sovereign the call is killed before the network is touched, and the refusal receipt still records what would have left.

### Evidence (P3, P4)

- **Receipts with a lifecycle.** Governed work is recorded from proposal through confirmation, execution, and verification, and the receipt is read back into both the Owner's disclosure and the operator's own context, so both narrate from the record.
- **An append-only audit trail**, separate from working data, cryptographically chained so that any edit, deletion, reordering, or truncation is detectable.
- **Identity on every actor.** Each governed actor carries an identity that binds its actions on the audit trail.
- **A configuration fingerprint** over the system prompt, the capability registry, and the agent set, so a change to the governing configuration is detectable, and one without a recorded reason is treated as drift.
- **Freshness labels** on every reported value, with the staleness threshold set per kind of value.

### Memory (P1, P2)

- **Owner-governed intake.** Routine observations are captured with a receipt and a one-click revert. Anything sensitive, and anything that contradicts what the Owner said, asks first.
- **A write-door firewall.** Personal memory is seeded only by the Owner's own words. Document, web, and tool content is refused as a memory source.
- **An authority chain** on every record, with machine-observed facts capped below Owner-stated ones, and durability earned by recurrence across separate conversations.

### Long work (P5, P7)

- **Bounded passes.** A mission runs as a loop of small plans, each through the same gate and executor as any other, so every pass is receipted by construction. State lives on disk; each pass starts from a fresh, bounded context assembled in code from the records.
- **Locked criteria.** The operator proposes done criteria and a budget when the mission opens; the Owner edits or approves; they lock at approval.
- **Measurement by code.** Whatever can be counted, such as words, documents read, citations checked, or files present, is counted by code every pass and never asked of a model. A judged criterion is met only when the verifier can quote the product as its evidence.
- **Repair, not retreat.** A verdict of not met sends the work back. The mission stops only on budget, on a block that needs the Owner, or when no recoverable move remains.
- **Honest exit.** Every stop delivers the work so far, with what is unresolved and why, rendered from the records rather than written by the model.

---

## Part 3. Levels

Autonomy is earned in a fixed order. A system is at a level when the statement for that level holds and every lower level still holds. Skipping a level is how an agent system becomes a liability.

| Level | Name | A system is here when |
|---|---|---|
| 1 | **Observability** | Evidence about the environment exists, is timestamped, is fresh-labeled, and reaches the Owner. |
| 2 | **Policy** | Capability tiers are enforced in code and nothing executes without a policy evaluation. |
| 3 | **Proposals** | The operator produces structured plans naming each step's capability, scope, and risk, and cannot execute them directly. |
| 4 | **Approvals** | Consequential work waits for an explicit act by the Owner, never an inferred one. |
| 5 | **Execution** | Approved steps run inside their declared scope, under enforced deadlines, each leaving a receipt. |
| 6 | **Verification** | Outcomes are checked by code, and done is decided by an external verifier before any completion is claimed. |

Above the levels sits the autonomy dial. It is adjustable only once the levels beneath it hold, and it adjusts confirmation frequency, never permission. PAC's current standing on these levels is stated in the [roadmap](roadmap.md).

---

## Part 4. Crosswalk

The framework was not derived from these documents. It arrived at the same shape from a different direction, and the overlap is the point: it shows the shape is right.

| External framework | Where Earned Autonomy answers it |
|---|---|
| **NIST AI RMF 1.0**, Govern, Map, Measure, Manage | Govern: P1, P8, the registry. Map: postures, the authority chain on memory, Level 1 evidence. Measure: the levels, the fingerprint, measurement by code. Manage: the kill switch, posture fail-toward-caution, receipted rollback. Detail in [governance-posture.md](governance-posture.md). |
| **NIST NCCoE, Software and AI Agent Identity and Authorization** (concept paper, February 2026), which asks about identification, authentication, authorization, auditing, and what limits the impact after a prompt injection succeeds | Identification and authentication: identity on every actor. Authorization and least privilege: granted capability sets, the tiered registry, the deterministic gate. Delegation and binding to a human: P1, the two-surface split, approval as an act by the Owner. Auditing: the chained audit trail and receipts. Post-injection impact: P2, which is the whole thesis. |
| **OWASP Top 10 for Agentic Applications 2026** | Tool misuse: the registry. Rogue agents: P8. Memory poisoning: the write-door firewall. Human-agent trust: pre-execution approval of the actual plan. Row by row in [owasp-agentic-mapping.md](owasp-agentic-mapping.md). |
| **OWASP AISVS 1.0** | Chapter-level scoring in [aisvs-self-assessment.md](aisvs-self-assessment.md). |
| **EU AI Act**, Articles 10, 13, 14, 15 | Human oversight: approvals and the kill switch. Transparency: the plan preview and the receipt. Robustness: the input firewall and the gate. Data governance: owner-governed memory. |

---

## Part 5. Applying it to a system that is not PAC

Twelve questions. A "no" names the gap; the framework does not grade on intent.

1. Is there exactly one principal whose approval is required for consequential work, and can no process supply that approval on their behalf?
2. Does every action the agent can take carry a risk tier fixed in code, outside the model's reach?
3. Is the decision to run an action made without a model call, and does it block actions it does not recognize?
4. Does the principal see the actual proposed action before approving it, and can an approval be consumed only once?
5. Does a delegated agent hold exactly the permissions it was granted, resolved fail-closed?
6. Does turning autonomy up change only how often the system asks, never what it may do?
7. Is there a kill switch that halts autonomous execution and survives restart?
8. Is outbound reach closed by default and opened only by an explicit act of the principal?
9. Does every governed action leave a record that is read back into what the agent says next, and is that record tamper-evident?
10. Does every reported value carry its observation time, and is stale state labeled as such?
11. Can content the agent reads, whether a document, a web page, or a tool result, ever write to its long-term memory?
12. Is completion decided by something other than the worker, against criteria fixed before the work began, and does every stop deliver what exists so far with what is missing?

---

## What this framework does not claim

- It is not a standard, a certification, or a conformance program. It is one builder's framework, stated so others can use or challenge it.
- It does not claim immunity to prompt injection. It claims to bound what a fooled model can do.
- It does not cover multi-tenant identity, hosted deployment, or inter-agent messaging. Those reintroduce surfaces this framework deliberately avoids.
- It does not claim PAC meets every control at every moment; the [README](../README.md) and [roadmap](roadmap.md) say where the build stands.

## License for this page

This page, and only this page, is licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). Copy it, adapt it, build on it, commercially or not, as long as you credit Eric Coomer and link back to this repository, and say if you changed it. The rest of this repository stays under its [LICENSE](../LICENSE), and the names Personal A.I. Console, PAC, and Kora stay covered by [TRADEMARK.md](../TRADEMARK.md); this license grants no rights in them.

Suggested attribution: *Earned Autonomy, v1.0, by Eric Coomer, CC BY 4.0.*

---

*See also: [trust-model.md](trust-model.md) &middot; [trust-quartet.md](trust-quartet.md) &middot; [how-it-works.md](how-it-works.md) &middot; [agent-governance.md](agent-governance.md) &middot; [governance-posture.md](governance-posture.md) &middot; [examples/sample-scorecard.json](../examples/sample-scorecard.json) &middot; [NOTICE.md](../NOTICE.md)*
