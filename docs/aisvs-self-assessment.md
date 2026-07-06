# OWASP AISVS 1.0 — PAC Chapter-Level Self-Assessment

This scores Personal A.I. Console&trade; (PAC) against the **[OWASP Artificial Intelligence Security Verification Standard (AISVS) 1.0](https://github.com/OWASP/AISVS)** &mdash; 12 chapters, 191 testable requirements, published by the OWASP Foundation in 2026. Where the [OWASP Agentic Top 10 mapping](owasp-agentic-mapping.md) answers "how does PAC line up against the threat categories," this answers the harder question: "how does PAC score against the *verification* standard built to test them."

**Read this honestly:**

- This is a **chapter-level** self-assessment: each of the 12 chapters gets a verdict and the reasoning behind it. It is not a requirement-by-requirement audit; a requirement-level pass against the Level 1 controls is the planned follow-up.
- Every claim below was **verified against the private build's code before publication** &mdash; the same discipline described in [engineering-discipline.md](engineering-discipline.md). Where PAC does not meet a chapter, that is stated, not softened.
- It is scoped to PAC's design target: **local, single-owner deployment** on hardware the owner controls. AISVS is written broadly enough to cover cloud-hosted, multi-tenant, model-training enterprises; several chapters therefore apply only partially, and two are largely not applicable *by architecture*. N/A verdicts state their reason so they can be challenged.
- It is a self-assessment, **not** an external audit, certification, or attestation.

**Verdict key:** ✅ Addressed within scope &middot; 🟡 Partially covered &middot; ⚪ Largely N/A by architecture (reason stated)

---

| Ch. | AISVS Chapter | Verdict | Assessment |
|---|---|---|---|
| **C1** | Training Data Integrity & Traceability | ⚪ N/A by architecture | PAC does not train, fine-tune, or label models &mdash; it runs pretrained local models as replaceable components. The nearest analogous surface *is* governed: ingested documents and owner memory carry provenance and trust metadata, and nothing enters long-term memory without an owner-controlled action. |
| **C2** | Input Validation | 🟡 Partial | An input firewall normalizes untrusted content, strips invisible-character and encoding tricks, and frames retrieved/pasted content as data rather than as a speaker; a secret scanner runs over content; inputs are length-capped. PAC does **not** run ML-based injection/jailbreak detection classifiers &mdash; by design, the load-bearing defense is containment downstream of input handling (see [threat-model.md](threat-model.md)), not detection at the gate. Detection would still add depth; it is a named gap. |
| **C3** | Model Lifecycle Management & Change Control | 🟡 Partial | Model changes flow through one governed path: models are configured through Settings, readiness is evaluated before a model takes a role, applying a change produces verifiable proof-of-apply state, and changing the embedding model flags every stale embedding for reindex. Artifact integrity relies on the local model manager's content-addressed pulls (Ollama digests); PAC adds no cryptographic signing of model artifacts on top, and there is no staged rollout/rollback machinery &mdash; single-machine deployment makes that surface small, but it is not formalized. |
| **C4** | Infrastructure, Configuration & Deployment Security | 🟡 Partial | Local-first by construction: the server runs loopback-bound in every launch profile and warns at startup when it cannot verify its declared bind, every non-public route requires the owner key, secrets live in a permission-restricted local file, and runtime state is validated against schemas. The enterprise hardware tier of this chapter &mdash; TEEs, GPU attestation, encrypted weights at rest &mdash; is not implemented; the threat model explicitly assumes an intact host (see [threat-model.md](threat-model.md), scope). |
| **C5** | Access Control & Identity | 🟡 Partial | Single-owner authority model: one owner key, an allow-listed anonymous surface pinned by a contract test so it cannot silently widen, and least-privilege capability packs bounding what each agent may touch. Multi-tenant isolation is N/A (one owner, by design). Per-agent cryptographic identities and ephemeral scoped tokens are not implemented &mdash; the same gap declared in the Agentic Top 10 mapping (ASI03). |
| **C6** | Supply Chain Security for Models | 🟡 Partial | Models are pulled through the local manager's content-addressed registry; capabilities are explicitly code-registered, never loaded dynamically from untrusted sources; Python dependencies are pinned. There is **no AI-BOM/SBOM and no dependency attestation** &mdash; the weakest chapter for PAC and a declared roadmap item. |
| **C7** | Model Behavior, Output Control & Safety Assurance | 🟡 Partial | Structured outputs are schema-validated with invalid drafts repaired or rejected, not trusted; outputs are length-bounded; and success language is *policy-gated* &mdash; the system will not claim an action ran, completed, or was approved without the corresponding receipt (a code-enforced truth gate). Freshness labeling prevents stale state posing as current. PAC does not do statistical confidence scoring or automated hallucination detection; its answer to hallucination is evidence discipline rather than a classifier, which covers actions well and prose only partially. |
| **C8** | Memory, Embeddings & Vector Database Security | ✅ Addressed within scope | The strongest chapter for PAC because it is the product's spine: the agent cannot write, promote, or delete long-term memory (no capability exists for it); memory carries provenance, trust, and sensitivity tiers with the most sensitive tier never auto-surfaced; embeddings are bound to their embedding model with stale vectors detected and flagged for reindex; retention policies and housekeeping are enforced; memory changes are versioned with rollback. Cross-tenant isolation requirements are N/A (single owner). |
| **C9** | Orchestration & Agentic Action Security | ✅ Addressed within scope | The chapter PAC was built for. High-impact actions require explicit human approval with the full plan and per-step parameters shown before anything runs; access decisions are **never delegated to the model** (tiers are registry truth; policy evaluation is deterministic and model-free); a persistent, fail-closed kill switch halts all autonomous execution and survives restart; execution is bounded (run leases, capped reasoning depth, stop-on-failure). Named gaps: no cryptographic agent identities binding actions across execution chains (C9.4), and execution budgets are bounds-based rather than formal per-tool cost quotas. |
| **C10** | Model Context Protocol (MCP) Security | ⚪ N/A by architecture | PAC does not use MCP &mdash; verified: zero MCP components, servers, or clients in the build. Tool access is an internal, code-registered capability registry rather than a pluggable protocol surface. If MCP connectors ever arrive on the roadmap, this chapter becomes binding and this page changes. |
| **C11** | Adversarial Robustness | 🟡 Partial | Much of this chapter defends a proprietary model API from external queriers (membership inference, model extraction, query-pattern analysis). PAC's model is local, open-weights, and not exposed as a service &mdash; there is no external query surface to extract from, and all API routes are rate-limited and key-gated regardless. What PAC adds: an evaluation harness gates model readiness before role assignment, and refused actions feed a denied-classes record. There is **no adversarial red-team test suite** yet; a structured adversarial pass against the full system is planned before wider exposure. |
| **C12** | Monitoring, Logging & Anomaly Detection | 🟡 Partial | Deep on evidence, thin on statistics. Every governed action leaves a receipt; policy decisions, approvals, and tool invocations write to audit surfaces separate from the main database, including an append-only audit trail; the runtime substrate logs schema-validated events and traces with rotation; refusals are counted and inspectable; and a configuration fingerprint makes silent changes to the governing setup (prompt, capabilities, agents) detectable as drift. Gaps: no ML-based behavioral anomaly detection, no statistical input-drift monitoring, and the audit trail is append-only but not yet cryptographically tamper-evident (roadmap). |

---

## Rollup

**2 chapters addressed within scope (C8, C9) &middot; 8 partial &middot; 2 largely N/A by architecture (C1, C10).**

That is the honest shape of a single-owner local system scored against a standard written for the full enterprise AI estate: strongest exactly where the product's thesis lives (memory governance, agentic authority), thinnest where enterprise machinery is assumed (SBOM, detection classifiers, hardware attestation).

## The gaps, in priority order

1. **Tamper-evident receipts** &mdash; append-only is a design property; hash-chained is a proof. The highest-value hardening item (C12, C9.4).
2. **AI-BOM / dependency attestation** &mdash; the weakest chapter (C6).
3. **Detection depth** &mdash; classifier-based injection screening and behavioral anomaly detection to complement containment (C2, C12).
4. **Adversarial red-team pass** &mdash; a structured attempt to break the system, recorded like everything else (C11).

## Next step

A requirement-level pass against the **Level 1** controls of the applicable chapters, published in the same format: verdict per requirement, gaps stated plainly.

---

*Chapter-level self-assessment of a private build, verified against code at the time of writing. Not an audit, a certification, or a guarantee. Framework: [OWASP AISVS 1.0](https://github.com/OWASP/AISVS).*
