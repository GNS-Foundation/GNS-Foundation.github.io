---
title: "Traffic Jam in the Skies: Why Satellites Need Identity Too"
excerpt: "On exploding satellites, orbital spies, a $500 million uninsured disaster, and what a protocol designed for humans can teach us about the 12,000 machines circling our heads."
date: 2026-02-19
permalink: /posts/2026/02/traffic-jam-skies/
tags:
  - Space
  - Satellites
  - Digital Identity
  - Space Debris
  - TRIP Protocol
---

# Traffic Jam in the Skies: Why Satellites Need Identity Too

*On exploding satellites, orbital spies, a $500 million uninsured disaster, and what a protocol designed for humans can teach us about the 12,000 machines circling our heads.*

---

On October 19, 2024, somewhere above the Indian Ocean, a $500 million satellite exploded.

No warning. No distress signal. Intelsat 33e — a Boeing-built communications satellite serving Europe, Africa, and most of Asia — simply ceased to exist at 04:30 UTC, fragmenting into what would eventually become more than 700 pieces of high-velocity debris, each one now orbiting Earth at 3 kilometers per second, 35,000 kilometers above our heads.

The satellite was not insured.

I read this news from Rome, where I had spent the previous two years building a protocol that proves human identity through the way people move through the world. TRIP — Trajectory-based Recognition of Identity Proof — uses cryptographically signed breadcrumbs to create an unfakeable record of movement. It was designed for people.

But as I read about Intelsat 33e, a thought took hold that I could not shake: if a protocol that identifies entities by their trajectory through space makes sense for humans walking through cities, why wouldn't it make sense for machines flying through orbit?

After all, a satellite is nothing but a trajectory.

---

## The Numbers Are Staggering

There are now 12,000 active satellites orbiting Earth. As of September 2025, tracking systems were monitoring over 25,000 objects, and that count excludes the millions of fragments too small to see from the ground but moving fast enough to destroy anything they hit.

SpaceX's Starlink constellation — already the largest satellite fleet ever assembled — performed 144,404 collision avoidance maneuvers in the first half of 2025 alone. That is a collision warning every two minutes, around the clock, for six months straight. The rate tripled from the previous six-month period.

ESA's 2025 Space Environment Report introduced something they called a "Health Index" for the orbital environment. The prognosis was not encouraging. Even if we stopped launching tomorrow — every rocket, every payload, zero new objects — the debris population would continue to grow, because fragments from future collisions would accumulate faster than atmospheric drag can clean them up. The technical name for this is the Kessler Syndrome. It was proposed as a theoretical scenario in 1978. In 2025, physicists are debating not whether it will happen, but whether it has already begun.

And here is the part that should make everyone uncomfortable: there is no universal system for identifying these objects, no cryptographic chain of custody for their trajectories, and — as the Intelsat 33e disaster demonstrated — often no insurance and no accountability when things go wrong.

The skies have a traffic jam. People are tracking it. But tracking is not the same as proving — and in orbit, as on the internet, the difference matters.

---

## Five Stories from Orbit

When I decided to build a working demonstration of TRIP applied to satellites, I expected a technical exercise. What I found instead were stories — real, dramatic, ongoing stories that illustrate exactly why trajectory-based identity matters above the atmosphere as much as it does below it.

These five satellites, all tracked live on a dashboard I built using real orbital data from the U.S. Space Force, embody the entire spectrum of the problem.

**The Stalker.** Luch/Olymp-K2, a Russian satellite launched in March 2023, immediately began doing something no legitimate communications satellite does: drifting through the geostationary belt and parking itself within 60 kilometers of Western military and commercial spacecraft. Slingshot Aerospace tracked it stopping near 3.2°E by October 2023. Its predecessor, Olymp-K1, visited twelve or more positions before being retired to a graveyard orbit in October 2025. In any domain besides space, this behavior has a name: surveillance. But in orbit, there is no formal mechanism to flag a satellite whose trajectory screams "I am watching you" — because the catalog only records where an object is, not how it got there. A trajectory-based identity system would catch this instantly. A geostationary satellite that drifts this often scores in the bottom tier of trust, no matter what its operator claims.

**The Uninsured Explosion.** Intelsat 33e was Boeing's pride — a high-throughput communications satellite designed for a 15-year lifespan. Its primary thruster failed during positioning. A second propulsion anomaly cut 3.5 years off its life. Then, on that October morning, it broke into 700 fragments that will orbit Earth for centuries. The satellite was uninsured. Intelsat had been offered a fleet-wide insurance policy after losing Intelsat 29e in 2019 under similar circumstances, but declined. The insurance market has been watching: 2023 was likely the worst year in history for satellite insurers, with losses exceeding $500 million. 2024 was worse. The fundamental problem is that insurers cannot price risk they cannot observe. Station-keeping degradation — the subtle loss of precision that precedes catastrophic failure — is invisible in the static catalogs that exist today. A cryptographic trajectory chain would have made that degradation visible months before the breakup.

**The Collision Course.** I chose a Starlink satellite — one of the 6,000-plus in the constellation — as an example of the most pressing operational challenge in space today. SpaceX publishes ephemeris data so other operators can predict close approaches, but there is no shared, cryptographically signed standard for this data. Operators trust each other's telemetry on faith. When SpaceX says a Starlink satellite is at a given position, other operators believe them because there is no alternative. This is the equivalent of highway drivers sharing their GPS coordinates over an honor system. It works until it does not.

**The Day the ISS Crew Sheltered.** On November 15, 2021, Russia destroyed its own Cosmos 1408 satellite with a direct-ascent anti-satellite missile at approximately 480 kilometers altitude. The explosion generated over 1,800 trackable debris fragments. The seven crew members aboard the International Space Station — which passes through the debris field every 90 minutes — were ordered to shelter in their escape vehicles. The ISS has the most complete trajectory record of any object in space: decades of continuous GPS data, radar tracking, and maneuvering logs. Yet none of this data is cryptographically chained. There is no tamper-proof record linking cause (an ASAT test) to effect (station maneuvers, crew risk, mission disruption). Under the 1972 Liability Convention, a state is liable for damage caused by its space objects. But proving that liability requires evidence. Without a cryptographic chain of custody for trajectory data, the evidence is whatever each party claims it to be.

**The Dutiful Observer.** ESA's Sentinel-2A has operated flawlessly since June 2015, providing free, open multispectral imagery that underpins global climate monitoring, disaster response, and agricultural mapping. It maintains precise orbit control, performs regular collision avoidance maneuvers, and follows ESA's Zero Debris charter. This is what a well-operated satellite looks like. In a world where trajectory data is cryptographically signed, Sentinel-2A's decade of clean, consistent behavior would earn it the highest trust score — and the lowest insurance premium. TRIP creates the infrastructure to reward this kind of stewardship, turning responsible operation from a moral choice into an economic advantage.

---

## What TRIP Actually Does (and Does Not Require)

A question I hear often: does applying TRIP to satellites mean putting sensors on every spacecraft?

No. This is the whole point.

TRIP operates at three tiers of attestation, and the first — which is already running in the live demo — requires zero cooperation from the satellite itself.

The first tier is ground-attested. The U.S. Space Force publishes orbital observations through a system called CelesTrak. My pipeline takes those observations and builds Ed25519-signed breadcrumb chains on behalf of each satellite, exactly as a DNS registry assigns a domain name without the website's cooperation. The satellite has no idea it is being tracked cryptographically. This is the ICANN model applied to orbit: a registry of identity imposed by observation, not by hardware.

The second tier is operator-attested. The satellite operator signs their own telemetry with a cryptographic keypair before publishing. No onboard hardware change — just a software modification to the ground segment. Higher trust score, because the operator is committing to the data.

The third tier is self-attested. An onboard module signs GPS fixes directly. Highest trust, because the attestation happens at the source. This is the CubeSat identity module on the Phase 3 roadmap.

The point is that the system works today, with existing infrastructure, without anyone's permission. We built a registry. The satellites are already in it.

---

## The Demo Is Live

I built this as a live dashboard. You can see it right now.

**https://orbital-trip-production.up.railway.app**

It tracks over 200 satellites across every orbital regime — geostationary communications birds, Starlink and Iridium constellations, weather satellites, military platforms, CubeSats, and debris. Each one has a cryptographic identity derived from its trajectory. Each one is scored for trust based on its pattern of behavior.

Click on "Stories" and you will find the five satellites I described above, with full narratives, timelines, and explanations of why their trajectories matter.

The entire system runs on Ed25519 signatures, SGP4 orbital propagation, and real-time TLE data from CelesTrak. The code is open source under MIT license. No special hardware. No orbital cooperation. Just math, observation, and cryptography.

The current demo tracks over 200 curated satellites. The next version will ingest the full CelesTrak GP catalog — every one of the roughly 28,000 tracked objects in Earth orbit. Every active satellite, every defunct spacecraft, every piece of debris large enough to be catalogued. Each one with a cryptographic identity. Each one scored for trust. A complete registry for the orbital commons.

---

## From Humans to Machines and Back

I wrote in my first essay that you are not your iris — you are your trajectory. I was talking about people. About the way human movement through the physical world produces a pink-noise signature that is impossible to fake digitally. About the physics of embodied existence.

I did not anticipate that the same principle would apply with even greater urgency to machines.

A satellite is its trajectory. Literally. Remove the trajectory and you have a piece of metal drifting in a vacuum. The trajectory is the satellite's mission, its value, its identity, its risk profile. It is also the only thing that distinguishes a communications satellite from a spy platform, a functioning asset from an uncontrolled fragment, a responsible operator from a reckless one.

And yet — remarkably, absurdly — no universal system exists to cryptographically record these trajectories, to make them tamper-proof, to create an auditable chain of custody that transforms trajectory data from self-reported telemetry into verifiable evidence.

This is the gap that TRIP fills. Not because I designed it for satellites. Because I designed it for anything that moves through the world and needs to be trusted based on how it moves.

---

## The View from the Tiber

I still watch the starlings over Rome every winter evening. The murmurations are smaller this year — perhaps the mild winter, perhaps something else. But the physics has not changed. Each bird tracks its nearest neighbors. Local interactions produce global coherence. Trust emerges from trajectory.

Above the murmurations, invisible, 12,000 satellites trace their own paths. Some are dutiful observers. Some are stalkers. Some are ticking time bombs. And some have already exploded, leaving clouds of debris that will orbit for centuries.

We built an identity protocol for the humans below. It turns out the machines above need it just as badly. And soon enough, when autonomous robots begin walking among us on the streets — not orbiting overhead but sharing our sidewalks — the question will be the same one it has always been: *how did you get here, and can you prove it?*

The TRIP protocol specification is an IETF Internet-Draft: draft-ayerbe-trip-protocol-00. The live satellite demo is at orbital-trip-production.up.railway.app. The code is at github.com/GNS-Foundation/orbital-trip.

---

*I'm Camilo. I build identity infrastructure at the GNS Foundation. My previous essay, "You Are Not Your Iris. You Are Your Trajectory," introduced the physics behind TRIP. This piece extends the same principle to orbit — because it turns out Parisi's starlings had something to teach us about satellites too.*

*Find me at @camilo on GNS, or the usual places until everyone migrates over.*
