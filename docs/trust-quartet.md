# The Trust Quartet

Most of Personal A.I. Console&trade; (PAC) reduces to four sentences. Everything else &mdash; the tiers, the postures, the receipt spine, the agent governance &mdash; is implementation detail in service of these four invariants.

They are written as guarantees the system is built to keep, and as properties it is tested against. This document states the four, then expands the two that are easiest to miss and hardest to fake.

It is written at a product level and does not include source, schemas, or configuration. See [NOTICE.md](../NOTICE.md).

---

## The four invariants

**1. PAC never claims an action occurred without a receipt.**
If the system says it did something, there is an evidence record proving it. A confident sentence from the model is not proof. The receipt is. A *missing* receipt is itself meaningful &mdash; it means the action did not happen.

**2. PAC never presents cached or replicated data as current.**
Every piece of reported state carries a timestamp and a source. Stale data is labeled stale and presented as last-known &mdash; never dressed up as live. (Expanded below.)

**3. PAC never sends system internals outbound.**
The system's own operational data does not leave the machine. Owner content may cross the boundary only under its own export rules, through a governed path, with a receipt &mdash; and external data that enters is never presented as first-party truth without provenance.

**4. PAC never lets a meaningful task end invisibly.**
Work the system took responsibility for terminates in something the Owner can see: a deliverable to read, a failure with a reason, a request for approval, or an explicit close. No silent completion. No "I did the thing, trust me."

These are the floor. Every other capability sits on top of them, and a feature that would violate one of them is redesigned rather than shipped.

---

## Freshness is part of truth

A system that reports the past as the present is not merely imprecise &mdash; it is lying, even if every number it states was true at some point.

PAC treats freshness as a first-class property of any reported fact:

- Every reported value carries **when it was observed** and **where it came from**.
- If a value is older than the staleness threshold for its kind, it is **labeled stale** and presented as last-known, not as current.
- The words **"currently," "right now," "as of now," "at this moment"** are not used in generated output unless there is a live fetch backing them. Instead of bluffing, the system says *"CPU was at 87% as of 14:23 &mdash; this may be out of date,"* or simply *"no current data available."*

This is why the local evidence layer ([PAC OS](architecture.md)) timestamps everything it observes, and why "I don't have fresh data on that" is an acceptable, designed answer rather than a failure. Knowing the *age* of what you know is part of knowing it.

---

## Refusals are not errors

When PAC declines to do something, that is usually the system **working as designed** &mdash; not a malfunction. PAC draws a hard line between two very different events, and is built never to describe one as the other:

| | What it is | Examples |
|---|---|---|
| **Refusal** | The governance layer intentionally declining | A FORBIDDEN capability is blocked; a SENSITIVE step parks for approval; an outbound call is denied under Sovereign posture; an unknown capability is rejected. |
| **Runtime error** | Something failed after it was allowed to run | A timeout; a malformed result; a missing file; a verification that didn't pass. |

A refusal is a **decision with a reason**. PAC surfaces refusals with stable, machine-readable reason codes &mdash; *why* it was blocked, and *what would unlock it* &mdash; so a "no" is informative rather than a dead end. A request blocked because the system is in Sovereign posture is answered with "this needs a Connected window you can open," not with a vague apology.

Treating refusals as first-class, explainable outcomes is what lets autonomy grow safely: the Owner can see exactly where the boundaries are and why a step stopped, instead of guessing whether the system broke or chose.

---

## Why four, not one

The invariants are deliberately separable. A system could keep receipts but bluff freshness; could be honest about staleness but leak internals; could do all three but let work vanish silently. Each invariant closes a distinct way a "helpful" assistant quietly becomes untrustworthy. Held together, they are the difference between *a system that is confident* and *a system that is accountable.*

---

*See also: [trust-model.md](trust-model.md) &middot; [how-it-works.md](how-it-works.md) &middot; [engineering-discipline.md](engineering-discipline.md) &middot; [README](../README.md) &middot; [NOTICE.md](../NOTICE.md)*
