# Examples

The files in this directory are **public-safe, mock examples** that illustrate Personal A.I. Console (PAC) concepts. They are written for documentation only.

They are **not**:

- exported from a private or production environment
- generated from real owner data, conversations, or runs
- a representation of the actual private schema, field names, interfaces, or configuration

Field names here are illustrative and chosen for readability. The real implementation is not described, specified, or reproduced by these files. See [NOTICE.md](../NOTICE.md) for rights and scope.

## Contents

All examples below share one mock scenario &mdash; a "summarize the morning's meeting notes" mission &mdash; so the artifacts line up with each other.

| File | Shows |
|---|---|
| [`sample-mission-flow.json`](sample-mission-flow.json) | The lifecycle of a mission — plan, capability tiers, approval, execution, and receipts. See [docs/architecture.md](../docs/architecture.md) for the work loop. |
| [`sample-policy-decision.json`](sample-policy-decision.json) | A deterministic policy decision over a plan — including a step whose registry tier overrides what the plan proposed. See [docs/how-it-works.md](../docs/how-it-works.md). |
| [`sample-receipt.json`](sample-receipt.json) | The shape of a single action receipt, lifecycle-tracked from proposal through verification. See [docs/architecture.md](../docs/architecture.md) for the evidence model. |
| [`sample-audit-entry.json`](sample-audit-entry.json) | Append-only audit entries for one governed flow — plan created, policy checked, confirmed, executed. See [docs/how-it-works.md](../docs/how-it-works.md). |
| [`sample-agent-definition.json`](sample-agent-definition.json) | The shape of an agent definition, including its per-agent autonomy level (which controls confirmation frequency, not permission). See [docs/how-it-works.md](../docs/how-it-works.md). |
| [`sample-denial.json`](sample-denial.json) | A structured denial &mdash; what was blocked, why, and what would unlock it. A denial is the system working as designed, not an error. See [docs/trust-quartet.md](../docs/trust-quartet.md). |
| [`sample-freshness-response.json`](sample-freshness-response.json) | How reported state carries freshness: each value has an observed-at time and a label, and stale data is presented as last-known rather than live. See [docs/trust-quartet.md](../docs/trust-quartet.md). |

For how these concepts hold the owner in control, see the [Trust Model](../docs/trust-model.md); for the flow that produces them, see [How It Works](../docs/how-it-works.md) and the [Demo Walkthrough](../demos/demo-walkthrough.md).
