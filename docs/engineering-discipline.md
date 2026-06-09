# Engineering Discipline

The rest of this repository describes *what* Personal A.I. Console&trade; (PAC) is. This document is about *how it is built* &mdash; because for a system whose entire premise is trust, the engineering practice is part of the product, not a footnote to it.

It is written at a methodology level. It does not include source code, test code, file layouts, or internal interfaces. It describes the practices and the guarantees they produce. See [NOTICE.md](../NOTICE.md) for scope.

---

## Why this document exists

A trust architecture is only as good as the evidence that it actually holds. Capability tiers, approval gates, and an oversight boundary are claims. What turns a claim into a property is a test that fails when the claim stops being true.

PAC is engineered so that its load-bearing guarantees are **executable**, not aspirational. The practices below are how the project keeps the trust model honest over time.

---

## A test suite that covers the guarantees, not just the code

The private build is covered by **more than 1,300 automated tests**. They are organized into tiers, each answering a different question:

| Tier | Question it answers |
|---|---|
| **Unit** | Does this piece behave correctly in isolation? |
| **Contract** | Does a load-bearing invariant of the system still hold? |
| **Integration** | Does a whole governed flow behave correctly end to end? |
| **Runtime substrate** | Does the local evidence layer collect, validate, and persist state correctly? |

The point of the tiering is the **contract** layer. Most test suites prove that functions return the right values. The contract tier proves that the *trust properties* are intact &mdash; the ones this repository makes claims about.

---

## Contract tests: invariants that are enforced, not promised

More than twenty contract tests exist for one reason: to make the trust model's promises break the build if they are ever violated. Among the invariants pinned this way:

- **The oversight boundary is a strict subset.** The set of agents Kora is allowed to restart is asserted to be a strict subset of the set the Owner controls &mdash; and the agents that observe, evaluate, learn from, or alert on Kora's own behavior are asserted to be *excluded* from her reach. If a future change let the delegate reach its own watchers, the test fails. (See [trust-model.md](trust-model.md) and [owasp-agentic-mapping.md](owasp-agentic-mapping.md), ASI10.)
- **Sensitive work cannot self-authorize.** Tests assert that a SENSITIVE step parks for Owner confirmation and does not execute on the model's say-so.
- **The approval surface can't be skipped.** A sensitive action with no way to reach the Owner does not quietly proceed.
- **Least-privilege packs are enforced.** An agent attempting a capability outside its granted pack is refused at execution time, not merely discouraged.
- **The trust ratchet is real.** Agent promotion thresholds (below) are asserted, so an agent cannot be promoted without meeting them.
- **The public surface is allow-listed.** Which routes may be reached without authority is pinned, so the anonymous surface cannot silently widen.

These are not the whole list, but they are the spirit of it: **the governance claims in this repository correspond to tests that defend them.**

---

## The earned-autonomy ratchet is a measured threshold

Agents do not graduate by vibes. The lifecycle (draft &rarr; trial &rarr; active &rarr; proven) is gated on recorded statistics:

- **Trial &rarr; Active** requires a run of **consecutive successful trial runs** (default: 5 in a row).
- **Active &rarr; Proven** requires **at least 20 total runs** *and* a **success rate of at least 95%**.
- An Owner may override a promotion &mdash; and the override is **itself recorded** as a governed event, so the exception is visible rather than silent.

Trust is accrued from behavior over time and is inspectable. It is not granted by assertion.

---

## An append-only change ledger

Every change that touches security posture, capability permissions, memory semantics, the OS&harr;Core contract, or connectivity posture is recorded in an **append-only development ledger** before the change is considered done. The ledger is chronological and never edited after the fact.

In the current build it holds roughly **sixty-seven entries spanning about three months** of continuous work, and **nearly every entry carries a configuration fingerprint** (below). Routine formatting and cosmetic edits are deliberately *excluded* &mdash; the ledger is a record of consequential change, not a commit log.

The discipline this enforces is simple: **if a meaningful change isn't in the ledger, it isn't finished.**

---

## Configuration drift detection

PAC keeps a **fingerprint** of its own governing configuration &mdash; a hash over the system prompt, the tool/capability registry, and the set of runtime agents. Each ledger entry records the fingerprint *after* the change.

If the fingerprint moves and the ledger does not explain why, the system is in a **drift state** &mdash; a signal to stop and find the unexplained change before doing more work. This makes silent, untracked changes to the trust-relevant configuration detectable rather than invisible.

---

## Honesty as an engineering practice

The most unusual discipline is the least glamorous: the project is built to **distrust its own documentation**.

- **Code is the only hard truth.** Where a design note and the running system disagree, the system wins, and the note is corrected &mdash; not the other way around.
- **Verify against code, not memory.** Claims about how something behaves are re-checked against the implementation before they are relied on. (This very document's numbers were verified against the build before publication.)
- **Documents flag their own gaps.** Internal specs carry status tags (current / target / needs-reconciliation), and the build's own audits openly record where the implementation does not yet meet a stated goal &mdash; rather than papering over it.

This is the same standard applied to the public repository: every claim here is meant to be **true now**, not true eventually. Where something is partial, the [README](../README.md) and [roadmap.md](roadmap.md) say so plainly.

---

## What this document does *not* claim

- It does **not** publish test code, counts as a contract, file structure, or internal interfaces. Numbers are descriptive snapshots of a private build at a point in time, not a guarantee.
- It does **not** claim external audit, certification, or third-party verification. The tests are the project's own.
- It does **not** claim the suite is exhaustive or that the system is defect-free &mdash; only that the trust-relevant guarantees are defended by tests and that change is tracked.

---

*See also: [README](../README.md) &middot; [trust-model.md](trust-model.md) &middot; [owasp-agentic-mapping.md](owasp-agentic-mapping.md) &middot; [roadmap.md](roadmap.md) &middot; [NOTICE.md](../NOTICE.md)*
