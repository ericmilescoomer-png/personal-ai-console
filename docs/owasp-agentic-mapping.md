# OWASP Top 10 for Agentic Applications — PAC Self-Assessment

This maps Personal A.I. Console&trade; (PAC) against the **[OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)** &mdash; the ten threat categories (commonly referenced by their **ASI01&ndash;ASI10** codes), published by the OWASP Gen AI Security Project.

**Read this honestly:**
- This is PAC's own self-assessment, **not** an external audit, certification, or compliance attestation.
- It is scoped to PAC's design target: **local, single-owner deployment.** It does not claim hardening for public internet exposure or multi-user hosting.
- It is **not** a "10/10 covered" claim. Several items are partial, one is largely out of scope for the current single-owner design, and the gaps are listed explicitly.
- It describes *what PAC does*, at a behavior level &mdash; not the implementation. See [NOTICE.md](../NOTICE.md).

**Status key:** ✅ Covered &middot; 🟡 Partially covered &middot; 🟭 Planned &middot; ⚪ Largely not applicable (current design)

---

| # | Threat | Status | How PAC addresses it (and where it doesn't) |
|---|--------|--------|---------------------------------------------|
| **ASI01** | Agent Goal Hijack | 🟡 Partial | Untrusted input is sanitized before it reaches the model, and &mdash; critically &mdash; even a hijacked goal cannot execute beyond policy: the model never sets its own permissions, sensitive steps still require owner approval, and forbidden capabilities are blocked. Containment limits blast radius; no system fully prevents injection. |
| **ASI02** | Tool Misuse & Exploitation | ✅ Covered (core design) | Every capability is registered with a fixed risk tier (SAFE / SENSITIVE / FORBIDDEN); tiers are registry truth, not model-claimed. SENSITIVE requires confirmation, FORBIDDEN is blocked, and destructive work never auto-executes. This is the heart of the system. |
| **ASI03** | Identity & Privilege Abuse | 🟡 Partial | Single-owner authority model; delegated agent authority is bounded by the policy gate; oversight agents cannot be controlled by the agent they watch (enforced by a contract test). PAC does **not** use per-agent non-human identities / ephemeral tokens (an enterprise pattern) &mdash; the single-owner local model reduces, but does not formalize, this surface. |
| **ASI04** | Agentic Supply-Chain | 🟡 Partial | Capabilities are explicitly code-registered, not dynamically loaded from untrusted sources; external tool access is gateway-mediated; a secret scanner runs over content. There is no formal dependency-attestation / SBOM process yet. |
| **ASI05** | Unexpected Code Execution (RCE) | 🟡 Partial | PAC does **not** expose raw code execution as the model's primitive &mdash; the agent calls registered capabilities by key, not an arbitrary `exec(code)`. Code-oriented capabilities run under OS-level job isolation. Stronger sandboxing is a known hardening item (see Gaps). |
| **ASI06** | Context Management & Retrieval Manipulation | 🟡 Partial | Long-term memory is owner-controlled: the agent cannot write, promote, or delete memory on its own. Memory carries sensitivity tiers; the most sensitive tier is never auto-surfaced; retrieved memory is explicitly marked as historical, not live state. Ingested-document poisoning is mitigated by input sanitization but not eliminated. |
| **ASI07** | Insecure Inter-Agent Communication | ⚪ Largely N/A | PAC's current design is single-owner with local monitor agents coordinated by the core; the evidence plane is one-directional (observe → report, never cross-write). There is no multi-agent message mesh to compromise today. (Specialized agent workers on the roadmap would reintroduce some of this surface and require revisiting.) |
| **ASI08** | Cascading Failures | 🟡 Partial | Agents run with bounded execution and graceful degradation; a kill switch halts all auto-execution immediately; posture and policy fail toward caution rather than convenience. There is no formal multi-agent blast-radius simulation. |
| **ASI09** | Human-Agent Trust Exploitation | 🟡 Partial | The approval surface shows the owner the actual plan, the per-step tiers, and exactly what needs a decision &mdash; before anything sensitive runs &mdash; and the gate that classifies risk is deterministic, not model-decided. This reduces authority-bias manipulation; no UX fully prevents a persuasive agent. |
| **ASI10** | Rogue Agents (drift within policy) | ✅ Covered (core design) | The policy gate is deterministic, registry-truthful, and model-free: wording cannot change a decision. Autonomy level changes confirmation frequency, never permission. The agent cannot restart or silence the agents that observe, evaluate, or alert on it &mdash; enforced in code and pinned by a contract test. This "the delegate can't disable its oversight" property directly targets in-policy drift. |

---

## Gaps and planned hardening

Called out plainly, because honesty is the point of this document:

- **Tamper-evident receipts (planned).** PAC today produces receipt-backed accountability and an append-only audit trail &mdash; append-only by design, but **not yet cryptographically tamper-evident.** Signed / hash-chained receipt integrity is a roadmap hardening item.
- **Sandboxing maturity (partial).** Code-oriented work runs under OS-level job isolation; stronger, zero-network sandboxing is an area to harden.
- **Supply-chain attestation (gap).** No formal dependency attestation / SBOM yet.
- **Enterprise identity patterns (by design, absent).** Per-agent non-human identities and ephemeral, scoped tokens are not part of the single-owner local model; they would matter for multi-user or hosted deployments, which are explicitly out of scope.

See [roadmap.md](roadmap.md) for where these sit, and [how-it-works.md](how-it-works.md) / [trust-model.md](trust-model.md) for the controls referenced above.

*This is a behavior-level self-assessment for a local, single-owner deployment. It is not an audit, a certification, or a guarantee.*

*Framework reference: [OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/), OWASP Gen AI Security Project.*
