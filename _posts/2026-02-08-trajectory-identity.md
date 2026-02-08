---
title: 'You Are Not Your Iris. You Are Your Trajectory.'
date: 2026-02-08
permalink: /posts/2026/02/trajectory-identity/
tags:
  - identity
  - privacy
  - TRIP
  - decentralization
  - GNS
---

*On starling murmurations, Nobel Prize physics, and why the future of digital identity lives in the way you move through the world.*

---

In the winter skies above Rome, something extraordinary happens every evening. Hundreds of thousands of starlings gather above the Tiber and perform what can only be described as a single, breathing organism — a murmuration. No leader. No central coordination. Just individual birds tracking their six or seven nearest neighbors, and from that simple local rule, global coherence emerges. The flock turns as one. Information propagates from one edge to another in milliseconds. A predator appears, and a million birds respond simultaneously.

In 2021, Giorgio Parisi won the Nobel Prize in Physics for explaining why this works. His insight was startling in its simplicity: these systems operate at a critical point — the precise boundary between order and chaos. Too much order, and the flock becomes rigid, unable to adapt. Too much chaos, and it dissolves into noise. At criticality, the system achieves something remarkable: scale-free correlations. What one bird does ripples across the entire flock, regardless of size.

I have spent much of my intellectual life at similar boundaries — between disciplines, between traditions, between the quantitative and the qualitative. My thesis in epistemology under the late Remo Bodei explored the tension between Darwinian competition and Lamarckian cooperation as engines of evolution. What I did not expect, decades later, was to find that same tension encoded in a debate about how we prove who we are on the Internet.

---

## The Identity Problem, Restated

The Internet has an identity crisis. Not a philosophical one — a mechanical one. There is no native way to prove that the entity on the other side of a screen is a human being rather than a script, a bot, or a replay. Every solution we have invented to address this creates new problems.

Passwords are shared, stolen, and forgotten. CAPTCHAs are solved by AI faster than by the humans they are meant to protect. And the latest entrant — biometric verification — asks us to trade our most intimate biological data for the privilege of being believed.

Worldcoin, the most prominent example, proposes scanning the irises of every human on Earth with a device called the Orb. The premise is straightforward: your iris is unique, therefore your iris is you. But this reasoning contains a deep categorical error. My iris is a feature of my body. It is not me. It does not encode my habits, my rhythms, my relationship with the physical world. It is a static identifier extracted under controlled conditions and stored in a database — a honeypot, however well-encrypted, waiting for the adversary who is patient enough.

More fundamentally, the biometric approach embodies precisely the Darwinian extractivism I have written about before: it reduces the richness of human existence to a data point, optimizes for a single metric of "uniqueness," and concentrates trust in a centralized apparatus. It is efficient. It is also philosophically impoverished.

What if we took a Lamarckian approach instead? What if identity were not extracted but emergent — accumulated through lived experience, shaped by interaction with the environment, and impossible to separate from the life that produced it?

---

## The Physics of Being Human

This is where Parisi's starlings become more than a metaphor.

Albert-László Barabási, the network scientist, demonstrated something profound about human mobility in a landmark 2008 paper in *Nature*: despite our subjective sense of spontaneity, 93% of human movement is predictable based on historical patterns. We are creatures of habit — but not mechanical habit. Our displacements follow what physicists call truncated Lévy flights: mostly short hops (home to office, office to café) punctuated by occasional long jumps (a weekend trip, a holiday abroad), all following a precise power-law distribution with an exponential cutoff.

And here is the critical connection to Parisi: when you analyze the Power Spectral Density of a human trajectory over time — the frequency signature of how we move — it produces pink noise. The technical designation is 1/f^α noise, where the exponent α falls between 0.30 and 0.80 for biological systems. This is the mathematical fingerprint of a system operating at the edge of criticality.

A random walk generator produces white noise — α near zero. No memory, no correlations. A deterministic replay produces brown noise — α near 2. Too much memory, too predictable. Only a living organism, embedded in a physical environment and operating under the constraints of gravity, metabolism, social obligation, and circadian rhythm, produces the characteristic pink noise of criticality.

This is not a statistical curiosity. It is a physical law of embodied existence.

---

## From Physics to Protocol

I have spent the last two years translating this insight into a working protocol called TRIP — Trajectory-based Recognition of Identity Proof. The principle is simple: instead of asking what you are (a biometric scan), TRIP evaluates how you move through the world.

A TRIP-enabled device collects "breadcrumbs" — compact, cryptographically signed attestations of spatiotemporal presence. Each breadcrumb records not raw GPS coordinates but a quantized position on a hexagonal grid (using Uber's H3 system), signed with the device's Ed25519 key, and chained to the previous breadcrumb via SHA-256 hashes. No raw location data ever needs to leave the device.

Over time, the trajectory is evaluated by what I call the Criticality Engine — a six-component analysis that examines:

- Whether your displacement pattern follows the expected truncated Lévy flight distribution
- Whether you are in the right places at the right times according to your own circadian rhythm
- Whether your movement aligns with the "flock" of co-located humans (Parisi's topological neighbors)
- Whether your device's inertial sensors corroborate the GPS data
- Whether the timing and structure of your breadcrumb chain exhibit the regularity of a machine or the organic irregularity of a person

Each of these dimensions contributes to a Hamiltonian — an energy function borrowed from statistical mechanics — that quantifies how much a given breadcrumb deviates from your learned behavioral profile. Low energy means normalcy. High energy means anomaly. The thresholds are self-calibrating: your own history defines what is normal for you.

The output is a Proof-of-Humanity Certificate that contains only the statistical exponents — α, β, κ — and nothing else. A relying party learns that you move like a biological organism. It learns nothing about where you have been.

---

## What Cannot Be Faked

This approach inverts the security model. Instead of protecting a static secret (a password, a biometric template), TRIP makes the cost of fabrication the defense.

To fool the PSD analysis, an adversary would need to generate synthetic movement that exhibits 1/f^α pink noise with α in the biological range — while simultaneously matching a plausible Lévy flight displacement distribution, maintaining consistent circadian and weekly activity patterns, producing correlated IMU and GPS data, and aligning with the real-time movement of actual co-located humans.

This is not a matter of computational difficulty. It is a matter of physical impossibility for a purely digital adversary. You cannot fabricate the flock, because the flock is made of other real humans moving through the same space. You cannot replay a recorded trajectory, because the ambient flock has changed. You cannot emulate from a server, because there are no inertial sensor readings to correlate.

The only way to produce a valid TRIP trajectory is to physically move through the real world, over time, like a human being.

---

## The Murmuration Principle

There is a deeper principle at work here — one that connects Parisi's starlings to the question of digital identity in a way that I find both technically elegant and philosophically satisfying.

In a starling murmuration, each bird tracks only its six or seven nearest topological neighbors. Not all birds within a radius — a fixed number of nearest neighbors, regardless of density. From this simple local rule, global coherence emerges. The correlation length scales with the size of the flock. Information propagates across the entire system.

In TRIP, each identity attests only to local encounters. You do not need to prove yourself to a central authority or to the entire network. You prove yourself to the immediate context — the local flock, the ambient environment. And from those local attestations, trust propagates across the entire system, with the same scale-free correlations that Parisi found in the Roman skies.

The flock is the identity. The murmuration is the trust.

---

## A Different Kind of Standard

I have recently submitted TRIP as an Internet-Draft to the IETF — the body that has maintained the technical standards of the Internet since its inception. The protocol maps naturally to the RATS (Remote ATtestation procedureS) architecture defined in RFC 9334, where breadcrumbs serve as Evidence, the Criticality Engine acts as the Verifier, and the Proof-of-Humanity Certificate constitutes the Attestation Result.

This is not an accident. TRIP is designed to be infrastructure — a protocol layer that any application, any authentication provider, any platform can integrate. It does not compete with existing identity systems. It complements them with something that has been missing from the beginning: a way to establish that an online actor corresponds to a physical entity, without requiring that entity to surrender its biometric data to anyone.

The draft, the mathematics, and a reference implementation are publicly available at the [GNS Foundation's repository](https://github.com/GNS-Foundation/trip-protocol).

---

## Toward a Lamarckian Internet

The Internet we have today was built on a Darwinian logic: extract, optimize, compete. Identity was an afterthought — bolted on as passwords, then CAPTCHAs, then iris scans, each solution more invasive than the last.

What if identity were instead Lamarckian — something that grows, adapts, and accumulates through interaction with the world? Not a feature extracted from your body, but a trajectory traced by your life. Not a secret to be guarded, but a pattern to be lived.

Parisi showed us that the most robust systems in nature are those that operate at criticality — on the edge between order and chaos, where local interactions produce global coherence. Human mobility operates at this same critical point. TRIP is simply the protocol that recognizes this fact and turns it into verifiable proof.

You are not your iris. You are not your password. You are not your government ID.

You are your trajectory through the world.

---

*Originally published on [Medium](https://medium.com/@camilo.ayerbe/you-are-not-your-iris-you-are-your-trajectory-1869a3e43084). Camilo Ayerbe Posada is the founder of ULISSY s.r.l. and the author of the TRIP Protocol ([draft-ayerbe-trip-protocol](https://datatracker.ietf.org/doc/draft-ayerbe-trip-protocol/), IETF). He holds a degree in Philosophy from the University of Pisa, where he studied epistemology under Remo Bodei. He lives in Rome.*
