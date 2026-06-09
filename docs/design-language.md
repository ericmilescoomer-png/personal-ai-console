# Design Language &mdash; Calm by Default

The trust architecture decides what Personal A.I. Console&trade; (PAC) is *allowed* to do. The design language decides how it *feels* to use. They are held to the same standard: the interface is built to keep the Owner oriented and in control, never to flatter, alarm, or obscure.

This document describes the design philosophy at a product level. It is not a style sheet or a component spec. See [NOTICE.md](../NOTICE.md). For the views themselves, see [screenshots.md](screenshots.md).

---

## The philosophy: Calm Core, Active Edges

PAC is one quiet command surface. Pages differ by structure, density, and behavior &mdash; not by decoration. Four principles govern every screen:

- **Calm by default.** When the system is healthy, the interface is quiet. Visual escalation is *earned* by a real condition, not spent on ambient flourish. A nominal system looks matte and still.
- **Whitespace is the container.** Information is grouped by space and alignment, not by stacking bordered boxes inside bordered boxes.
- **Color means something.** Every color has a job. Nothing is colored for flavor.
- **One primary action per panel.** A screen makes the next step obvious instead of offering ten equal buttons.

---

## Color carries meaning, not mood

PAC uses a deliberately small palette, and its discipline is the point:

- **Two brand colors.** One signals *interactive* (links, buttons, focus, the active location); one is reserved for *the AI* &mdash; Kora and AI-generated content. They are never swapped or used decoratively.
- **A brand gradient** appears in a small, fixed set of identity places (the product mark, the active-location indicator, the hero title, the generation glow) &mdash; and nowhere else.
- **Semantic colors &mdash; green, amber, red &mdash; are reserved exclusively for state.** Green is healthy, amber is degraded, red is critical. They are never used for ordinary UI accents, so when one appears it always *means* something.
- **Nominal is matte; glow is earned.** Motion and glow are reserved for degraded or critical conditions. A calm system does not pulse.

### The detail that captures the whole philosophy

The Owner can recolor the *interactive* accent to taste &mdash; but the picker deliberately **excludes green, amber, and red.** A personalization choice is never allowed to impersonate a system state. You cannot accidentally (or deliberately) make "healthy green" your button color and lose the ability to tell health from preference. The aesthetic bends to the Owner; the meaning of color does not.

---

## Confirmation is a ladder, not a habit

Friction is matched to consequence, so routine work stays light and dangerous work is unmistakable:

| Level | When | What the Owner sees |
|---|---|---|
| **0 &mdash; none** | Reads | Nothing; it just happens |
| **1 &mdash; soft** | Reversible writes | A confirmation and a toast |
| **2 &mdash; hard** | Deletes, posture changes | An explicit confirm dialog |
| **3 &mdash; two-step** | High-consequence actions | A deliberate, two-step modal stating the consequence |

The native browser `alert()` and `confirm()` pop-ups do not exist anywhere in PAC &mdash; every confirmation flows through one consistent, legible system, so a serious decision never looks like a stray browser dialog.

---

## State is always visible, never hunted for

A fixed bar persists on every page &mdash; it does not scroll away. It always shows **where you are**, the current **posture** (with a live countdown when a time-bounded window is open), and the system's **health**. The Owner never has to go looking to answer "what is the system allowed to do right now, and is it okay?" The answer is always on screen. That, too, is the trust model expressed as interface: the boundary you are operating under is never hidden.

---

## What this document does *not* claim

- It describes the design *philosophy*, not a finished or pixel-frozen interface. Some surfaces are more mature than others, and the [README](../README.md) is candid about which.
- It is not a component library, a CSS specification, or an invitation to reproduce the interface. See [NOTICE.md](../NOTICE.md).

---

*See also: [screenshots.md](screenshots.md) &middot; [trust-model.md](trust-model.md) &middot; [README](../README.md) &middot; [NOTICE.md](../NOTICE.md)*
