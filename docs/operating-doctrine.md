# The Operating Doctrine

Most "AI command centers" are dashboards wearing the words. Personal A.I. Console&trade; (PAC) is built the other way around: a **command model** with a clean surface on top. And the command model isn't invented &mdash; it's borrowed from how human crews have run high-stakes operations for a very long time, long before any of it was software.

Four proven practices shape how PAC behaves. This document names each one and maps it to the system. It is written at a product level &mdash; no source, schemas, or configuration. See [NOTICE.md](../NOTICE.md).

For the trust architecture underneath this, see [trust-model.md](trust-model.md); for how a single task flows, see [how-it-works.md](how-it-works.md).

---

## 1. Mission command &mdash; intent down, initiative within bounds

A commander gives **intent** &mdash; the purpose, the constraints, the result that's wanted &mdash; and the crew decides *how* to execute within that boundary. Authority is delegated; the boundary is not.

That is the relationship between the Owner, Kora, and the specialists:

- The **Owner** sets intent and holds final authority.
- **Kora** understands the objective and coordinates the work.
- **Specialists** act *within* their granted clearance &mdash; never outside it.
- **PAC Core** enforces the boundary in code, so delegated initiative can't quietly become more than it was granted.

The point of mission command is that work moves without the commander touching every step &mdash; *and* without the commander losing the boundary. That's exactly what PAC's capability tiers and policy gate are for: Kora can act, but only within intent.

---

## 2. Watchstanding &mdash; someone is always on watch; the captain isn't pinged for every ripple

On a ship, a watch team holds the routine picture. The captain is not personally tracking every gauge and radar contact; the watch handles the ordinary and **escalates only when the captain's judgment or authority is actually needed.**

This is the **Operations** surface. It is a *watch report*, not a raw event log:

- It shows what the crew **did** and what it **means** &mdash; not every low-level event the system emitted.
- Routine, handled work stays in the watch report. It does not interrupt.
- Only what needs the Owner's authority is escalated &mdash; into the **Inbox**.

The failure mode this avoids is the one every noisy system falls into: a firehose of alerts that trains the operator to stop looking. A watch report is the opposite &mdash; calm, sourced, and meaningful, so that when something *does* rise to the Inbox, it carries weight.

---

## 3. Crew resource management &mdash; the crew is named, and the work is attributed

High-reliability fields &mdash; aviation, surgery, firefighting, ships &mdash; learned that hiding *who did what* behind a single authority is dangerous. Crews are trained so that every member is visible and accountable, and so no single point of view goes unchallenged.

PAC refuses to flatten everything into "the AI did X." Work is **attributed to the crew member that did it**:

> Builder prepared the isolated workspace and verified the branch.
> Records indexed the receipts and linked them to the mission.
> PAC Core blocked a write outside the approved workspace.
> Kora routed the completed work to review and summarized what it means.

Attribution isn't decoration. It's how the Owner keeps situational awareness over a system doing work on their behalf &mdash; and how a wrong move is traceable to a specific actor instead of a vague "the assistant."

The crew is **owner-curated**, not a fixed cast. PAC ships a small set of scoped specialists &mdash; for example **Builder** (prepares code and file changes inside an isolated safe workspace for the Owner to approve, never against live files), **Research** (treats the public web as evidence and returns cited digests, never as a speaker to obey), and a **Records Specialist** (indexes receipts and links them to the work they belong to). The Owner adds and shapes the crew over time. Whatever the roster, the rule underneath is constant: a specialist's title is just a label &mdash; its real authority is a **capability pack** (least privilege) and a scoped charter, and it earns trust on a measured ladder before it's relied on. See [agent-governance.md](agent-governance.md).

---

## 4. SBAR &mdash; the shape of a clean handoff

SBAR &mdash; **Situation, Background, Assessment, Recommendation** &mdash; came out of the military and nuclear-submarine world as a way to hand off critical information in a short, predictable form, and spread into aviation and medicine. It's how you transfer a picture fast without losing what matters.

PAC doesn't put "Situation / Background / Assessment" headers on the screen &mdash; that would feel like a form. But every Operations report and every Inbox call carries the **compressed SBAR spine** underneath:

- **What happened** (situation)
- **What it means** (assessment)
- **What's next** &mdash; nothing needed, moved to the Inbox, or proof available (recommendation)

> *Builder hit a workspace setup issue because the model backend timed out. Nothing changed in your live system. The raw failure details are kept in receipts.*

That is SBAR without looking like SBAR: situation, meaning, next state, proof one tap away.

---

## The mapping

| Real-world command practice | PAC surface | What it means |
|---|---|---|
| The captain's call | **Inbox** | The decisions only the Owner can make: approve, review, recover. |
| Watch log / operations brief | **Operations** | What the crew did or observed that matters &mdash; in plain language, attributed. |
| The official logbook | **Receipts / Journal** | Durable proof of what happened. |
| Records office | **Library** | Durable knowledge and artifacts. |
| Standing orders | **Rules / Permissions / Policy** | What Kora and the crew may do without asking. |
| Staff sections / crew | **Agents / Specialists** | An owner-curated crew, each bound to a capability pack and scoped to a job &mdash; e.g. Builder (safe-workspace changes), Research (cited web evidence), Records Specialist (receipts &amp; indexing). |
| Engineering control room | **System** | Health, monitors, runtime, services. |
| The watch officer | **Kora** | Coordinates the crew, briefs the Owner, escalates when authority is needed. |

---

## Why borrow from the old world

Two reasons.

First, it's **proven**. The problem PAC solves &mdash; a principal who can't watch everything, a trusted crew acting under intent, reporting that surfaces meaning and escalates by exception, with proof available if challenged &mdash; is not new. People have been solving it for centuries. The vocabulary already exists; PAC didn't have to invent it.

Second, it happens to be **where serious autonomous-systems thinking is heading anyway**. The current human-in-the-loop direction &mdash; keep the human in command, surface meaning rather than raw exhaust, make actions auditable, escalate by exception &mdash; is the same lesson watchstanding and mission command encoded long ago. PAC arrived at it from the old end rather than the new one. (No claim that PAC *implements* any particular framework or standard &mdash; only that the doctrine lines up.)

The result is a single rule that governs the whole surface:

> **Operations shows what the crew did and what it means. The Inbox asks the Owner to decide. Receipts prove it. System watches health. Library keeps the knowledge.**

A dashboard tells you *what is happening*. A command deck tells you *what it means, whether you're needed, and where the proof is.* That difference &mdash; not the words "command center" &mdash; is the lane PAC is built for.

---

## What this doctrine does *not* claim

- It does **not** claim PAC is the only AI system with a command-center surface. Agent control towers, orchestration layers, and governance consoles are a real and crowded category. PAC's claim is narrower: a *local-first, single-owner* command surface run with crew discipline, not a generic agent dashboard.
- It does **not** claim conformance to any military, aviation, or clinical standard. These are design analogies that shape behavior, not certifications.
- It describes how PAC is *meant to behave* at a product level &mdash; not the implementation. See [NOTICE.md](../NOTICE.md).

---

*See also: [trust-model.md](trust-model.md) &middot; [how-it-works.md](how-it-works.md) &middot; [agent-governance.md](agent-governance.md) &middot; [design-language.md](design-language.md) &middot; [README](../README.md)*
