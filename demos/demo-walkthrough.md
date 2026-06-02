# Demo Walkthrough

This walks through a single governed task in Personal A.I. Console&trade; (PAC), end to end, using the screenshots in [`../assets/screenshots/`](../assets/screenshots/). It is captured from the current private prototype with demo data &mdash; the owner is shown simply as **Owner**, and nothing here exposes private implementation, paths, or personal data.

The point of the walkthrough is to show *how PAC behaves*, not just what it looks like: a request becomes a plan, the plan is classified and gated, sensitive work waits for the owner, and the result leaves a receipt.

**Scenario:** the Owner asks Kora to *back up the local library*.

---

## 1. The system the work runs on

![System overview](../assets/screenshots/03-system-overview.png)

Before any work happens, PAC presents its own state: the active local model, current **posture** (Sovereign &mdash; local only, no outbound), overall health, and local resource use &mdash; all on the owner's hardware. This is the command surface the rest of the flow happens under. Nothing here depends on a cloud service.

---

## 2. Kora drafts a plan and stops for approval

![Plan preview and approval](../assets/screenshots/01-plan-preview.png)

The Owner's request becomes a **plan**, not an immediate action. Kora drafts the steps and presents them *before* anything runs:

1. Check system health before backup &mdash; **SAFE**
2. Confirm available disk capacity &mdash; **SAFE**
3. Create a backup of the local library &mdash; **SENSITIVE**

Two things are happening underneath this screen:

- Each step's **tier** comes from PAC's capability registry, not from the model. The model proposes work; it does not get to declare its own work "safe."
- A deterministic policy evaluation runs over the plan. Because step 3 is **SENSITIVE**, the plan's outcome is *requires confirmation* &mdash; so the plan pauses, awaiting the Owner's **Go ahead / Not now** decision. The read-only checks could run on their own; the consequential step cannot.

This is the heart of PAC: the owner sees what was requested, what is proposed, and exactly what will need their say-so &mdash; before it happens.

---

## 3. After approval: execution leaves a receipt

![Action receipt](../assets/screenshots/02-action-receipt.png)

Once the Owner approves, the steps execute and the completed plan carries its evidence. Each step shows its capability and a **done** status, and Kora's summary states the work was verified: *"The backup snapshot is complete and verified. No further action is required."*

As far as PAC is concerned, an action without a receipt didn't happen. The completed view is the receipt: what was planned, what was approved, what ran, and the verification behind it.

---

## 4. Memory stays the owner's

![Memory governance](../assets/screenshots/05-memory-governance.png)

Throughout, Kora can *suggest* what's worth remembering, but cannot change long-term memory on her own. Persistent memory is owner-controlled state, changed only through an action the Owner takes &mdash; so nothing is remembered, edited, or forgotten behind the owner's back.

---

## 5. The model is a replaceable part

![Model settings](../assets/screenshots/04-model-settings.png)

The reasoning above came from a local model (Ollama, running Qwen in the reference build), configurable through Settings. Swap the model, and everything that made the flow trustworthy &mdash; the tiers, the approval gate, the receipt, the posture &mdash; persists, because none of it lives in the model.

---

## What this flow demonstrates

| Guarantee | Where it showed up |
|---|---|
| The model doesn't set its own permissions | Tiers come from the registry (step 2) |
| Sensitive work needs explicit owner approval | The plan paused on the SENSITIVE step (step 2) |
| Consequential work leaves evidence | The verified receipt (step 3) |
| Memory is owner-controlled | Kora can suggest, not write (step 4) |
| The model is a component, not the authority | Configurable provider (step 5) |
| Local-first by default | Sovereign posture, no outbound (step 1) |

The capability tiers, autonomy levels, and policy behavior shown here are described further in [how-it-works.md](../docs/how-it-works.md) and [trust-model.md](../docs/trust-model.md). For the shapes of the artifacts a flow like this produces, see [`../examples/`](../examples/).

*This walkthrough uses demo data and describes product behavior. It is not an implementation guide.*
