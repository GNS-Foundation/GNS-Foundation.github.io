---
title: 'When the IETF Asked: "What About People Who Can''t Walk?"'
date: 2026-02-10
permalink: /posts/2026/02/ietf-accessibility/
excerpt: 'On accessibility, statistical mechanics, and what happened when I submitted a protocol that proves humanity through movement.'
tags:
  - TRIP
  - identity
  - accessibility
  - IETF
  - physics
---

*On accessibility, statistical mechanics, and what happened when I submitted a protocol that proves humanity through movement.*

---

Three months ago, I published an essay about starling murmurations over Rome and a protocol that proves you're human by the way you move through the world. The piece resonated with people who were tired of scanning their irises for corporations and solving CAPTCHAs for machines. But it also attracted a question I should have anticipated — one that cut to the heart of what I was building.

A researcher named Jun Zhang read my IETF Internet-Draft and wrote, plainly:

> *"This is discrimination against people with mobility difficulties and those who stay at home."*

He was right to ask. And the answer changed how I think about identity.

---

## The obvious objection

Let me state it clearly, because if you've read anything about TRIP — the Trajectory-based Recognition of Identity Proof protocol — your first instinct might be the same as Jun's.

If identity depends on movement, what happens to a person in a wheelchair? A bedridden grandmother? Someone with agoraphobia who hasn't left their apartment in two years?

Is the protocol saying they're less human?

No. But the fact that this reading was even possible meant the specification was incomplete. Not the idea — the specification. There's a difference, and it matters.

## What movement actually means

The misunderstanding — and I say this with genuine respect for the question — is that TRIP requires *geographic travel*. It doesn't. What it requires is sustained physical existence over time. These are fundamentally different things.

Consider my grandmother. She lives alone in a small apartment in Bogotá. She hasn't traveled in years. But her phone, sitting on her kitchen table, is not inert. The Wi-Fi access points her device can see change as neighbors add routers, replace hardware, switch providers. The cellular towers her phone connects to shift with network load — one tower at 3 AM, a different one at noon. The phone's own accelerometer detects micro-movements: the vibration of her washing machine, the tremor of her hand picking it up, the subtle tilt as she reads the news in bed.

None of this requires walking. None of it requires leaving a room.

What it produces is something remarkable: a stream of cryptographically signed attestations that are *never identical*, because the physical world around a real person is never static. An AI generating fake breadcrumbs from a server farm in Virginia would have to simulate not just a GPS coordinate, but the entire ambient electromagnetic and inertial environment of a specific place, changing in the specific way that place changes, over months and years.

That's the insight Jun Zhang's question forced me to articulate. TRIP doesn't measure distance traveled. It measures *temporal presence* — the irreducible fact of a physical body existing in a physical place over time.

## The math already knew this

Here's what surprised me when I went back to the specification: the trust scoring formula already accommodated this. I just hadn't made it explicit.

The formula assigns 20% weight to temporal continuity and 40% weight to breadcrumb count. Both accumulate regardless of whether you move at all. A person who stays home for a year, collecting breadcrumbs every ten minutes, achieves 60% of the maximum trust score from time and volume alone.

The spatial diversity component — how many different H3 cells you visit — is only one factor among several. And the Hamiltonian function that detects anomalies is *self-calibrating*: it learns each identity's own baseline. A homebound user develops a low-energy baseline. Anomalies are measured against *your own* pattern, not against some global expectation of how much a human should travel.

This is where Giorgio Parisi's physics becomes more than an elegant metaphor. In disordered systems, there is no single ground state. Each system finds its own equilibrium. The same principle applies here: each identity finds its own trajectory signature. The bedridden grandmother and the traveling salesman both produce valid, distinguishable, unfakeable patterns — just at different energy scales.

## What I changed

The second revision of the IETF draft — draft-ayerbe-trip-protocol-02, published today — includes a new section: *Accessibility and Low-Mobility Users*. It formally establishes that:

- Deployments MUST NOT impose minimum spatial diversity requirements
- The trust formula MAY be adjusted to increase temporal weighting for low-mobility identities
- Circadian rhythms in collection timing exhibit the same 1/f fractal characteristics as spatial displacement, providing an alternative criticality signal
- Implementations SHOULD supplement spatial analysis with temporal analysis when cell count is below a configurable threshold

These aren't accommodations. They're clarifications of what was always true about the protocol's mathematics. The physics doesn't discriminate. The specification needed to say so explicitly.

## What else changed in -02

Jun's question was one of several that improved the draft. Muhammad Usama Sardar, an expert in remote attestation, pushed hard on how TRIP maps to the IETF's RATS architecture — the formal framework for remote attestation procedures. His review surfaced a critical gap: the original draft described passive verification (checking someone's breadcrumb history) but had no mechanism for *active* verification — challenging someone to prove they're physically present *right now*.

The new revision adds an Active Verification Protocol: a challenge-response mechanism where a Verifier sends a random nonce, and the Prover must produce a breadcrumb incorporating that nonce within a time window. You can't pre-compute it. You can't replay it. You have to be *there*.

Other changes: the draft now uses deterministic CBOR encoding throughout instead of canonical JSON — because when you're signing data structures, bit-level determinism isn't optional. The H3 resolution range was expanded. The privacy model was corrected and clarified. And the relationship between TRIP and existing attestation standards was mapped explicitly.

Three revisions in. Three sets of expert feedback incorporated. This is how protocols are built — not in isolation, but through the specific, sometimes uncomfortable process of having people smarter than you find holes in your thinking.

## The deeper question

Jun Zhang's objection did more than improve a specification. It forced me to confront something I'd been circling around but hadn't fully articulated.

When we say "identity equals trajectory," we're not saying identity equals *distance*. We're saying identity equals *the irreducible trace of existing in the physical world over time*. A person in a coma, connected to a phone that records the changing electromagnetic signature of their hospital room, is producing trajectory. A prisoner in solitary confinement is producing trajectory. An infant who has never taken a step is producing trajectory.

Because trajectory isn't about where you go. It's about the fact that you exist somewhere, and that somewhere is always changing, and the record of that change is as unique and unfakeable as a fingerprint — except that unlike a fingerprint, it cannot be captured once and replayed forever.

The starlings over Rome move in magnificent, self-organizing patterns. But the old woman sitting on the bench in Piazza Navona watching them — she has a trajectory too.

---

*draft-ayerbe-trip-protocol-02 is available at [datatracker.ietf.org/doc/draft-ayerbe-trip-protocol](https://datatracker.ietf.org/doc/draft-ayerbe-trip-protocol/). Source code at [github.com/GNS-Foundation/trip-protocol](https://github.com/GNS-Foundation/trip-protocol).*

*This is the second in a series on identity, physics, and the protocol layer the internet never had.*
