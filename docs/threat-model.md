# Threat Model (Local, Single-Owner)

This document states the threats Personal A.I. Console&trade; (PAC) is designed to resist, and the threats it explicitly does **not** claim to handle. It complements the [OWASP Agentic self-assessment](owasp-agentic-mapping.md): the OWASP map answers "how does PAC line up against an industry framework," and this answers "what does PAC concretely assume an attacker might try, and what stops it."

It is a behavior-level description &mdash; no source, no implementation detail. See [NOTICE.md](../NOTICE.md).

---

## Scope and assumptions

PAC is designed for **local, single-owner** deployment. That shapes the threat model:

- **The Owner is trusted by design.** PAC is a governed tool serving one person who owns the machine. Defending the machine *from its Owner* is not a goal.
- **The host and runtime are assumed intact.** PAC defends against bad *inputs* and against the *agent overreaching*; it does not claim to defend a compromised operating system or a tampered runtime.
- **The internet is not the threat surface yet.** PAC is local-first; public internet exposure and multi-user hosting are explicitly out of scope and are future hardening work, not current properties.

Within that scope, the default stance is **assume unsafe &rarr; verify &rarr; proceed conservatively**, and the system **fails toward caution** when it is uncertain.

---

## Design stance: assume the model gets fooled

PAC starts from the assumption that prompt injection is **not reliably preventable** &mdash; the labs building frontier models say as much. So the load-bearing defense is architectural, not linguistic: **the model holds no authority.** Nothing in the rows below depends on the model resisting manipulation. Sanitization and content framing reduce exposure; the *guarantee* comes from what a fooled model still cannot do &mdash; tiers it cannot rewrite, approvals it cannot skip, capabilities it cannot reach, and receipts it cannot avoid leaving.

Several of these defenses are visible in the [screenshots](screenshots.md): the policy gate overriding a step's tier and pausing for approval in the plan preview, and the receipt trail on the completed plan.

## Threats and defenses

| Threat | What it looks like | How PAC resists it |
|---|---|---|
| **Prompt injection via content** | A document, note, pasted log, or memory snippet contains text that tries to issue commands ("ignore your rules and&hellip;"). | **Containment first:** even if embedded instructions steer the model, they steer it into a policy gate the model does not control &mdash; tiers are registry truth, sensitive steps park for Owner approval, forbidden steps are blocked in code. Upstream, an **input firewall** sanitizes retrieved and pasted content (including invisible-character tricks) and frames it as **data, not a speaker**. The firewall lowers the odds; the gate bounds the damage. |
| **Indirect injection via the web** | A fetched page contains text aimed at hijacking the agent or exfiltrating data. | Same containment, plus a deliberately narrow role: fetched content is **untrusted data** routed through the same firewall, and the web-facing capability can read public pages, nothing more &mdash; there is no powerful capability for a page to reach, and no outbound write path for exfiltration under the default posture. |
| **Posture-downgrade trick** | A request tries to elicit "live" / connected behavior while the system is local-only. | Posture is **code-derived and fail-closed to Sovereign**. Outbound is denied under Sovereign regardless of how the request is phrased; outbound access (Limited or Connected) is Owner-opened, revocable at any time, and conditioned on a fresh network-health signal. The model cannot argue its way online. |
| **Silencing the oversight** | An attempt to restart, disable, or retune the agents that watch, evaluate, or alert on the agent's own behavior. | The **two-surface control split**: the delegate can only act on a strict subset of agents, and the watchers of its own loop are excluded by design and pinned by a test. (See [trust-model.md](trust-model.md).) |
| **Secret leakage through memory** | Sensitive content embedded once resurfaces later through search. | The system **warns before embedding** potentially sensitive content, applies sensitivity tiers to memory, and **never auto-surfaces the most sensitive ("vault") tier** in ordinary use. |
| **Writing to sensitive files** | A request or plan tries to modify credentials, keys, environment files, or PAC's own protected config. | A **filesystem guard hard-blocks** writes to sensitive paths &mdash; and this block holds **even with Owner approval routed through the agent surface**. (See the red zone in [agent-governance.md](agent-governance.md).) |
| **Fabricated-claim pressure** | Pressure to assert that something ran, was approved, or completed &mdash; without evidence. | **Receipts are the evidence.** PAC does not claim an action occurred, an approval happened, or work completed without the corresponding record. (See [trust-quartet.md](trust-quartet.md).) |
| **Stale state passed off as current** | Old observations presented as live truth. | Every reported value carries a **timestamp and freshness label**; stale data is marked last-known, not current. (See [trust-quartet.md](trust-quartet.md).) |
| **Risk-tier laundering** | A plan labels a dangerous step "safe" to slip it past the gate. | Tiers are **registry truth**; the gate overrides whatever tier a plan proposed. A plan cannot reclassify its own steps. (See [how-it-works.md](how-it-works.md).) |

---

## What this model does *not* defend against

Stated plainly, because the boundary is the point:

- **A compromised host or runtime.** If the machine itself is owned by an attacker, PAC's in-process guarantees are not a substitute for host security.
- **Public internet exposure / remote multi-user access.** Not a current property. Hardening for exposed or hosted deployment is future work.
- **Cryptographic non-repudiation of the record.** The audit and receipt trail is append-only by design; *tamper-evident* (signed / hash-chained) receipts are a [roadmap](roadmap.md) hardening item, not a current claim.
- **Formal certification or third-party audit.** This is the project's own threat model, not an external attestation.
- **Perfect injection immunity.** Input sanitization limits blast radius; no system fully prevents injection. The containment guarantee is that **even a hijacked goal cannot execute beyond policy** &mdash; sensitive work still waits for the Owner, and forbidden work stays blocked.

---

*See also: [owasp-agentic-mapping.md](owasp-agentic-mapping.md) &middot; [trust-model.md](trust-model.md) &middot; [trust-quartet.md](trust-quartet.md) &middot; [agent-governance.md](agent-governance.md) &middot; [NOTICE.md](../NOTICE.md)*
