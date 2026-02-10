---
title: "TRIP: Trajectory-based Recognition of Identity Proof (Revision 02)"
collection: publications
category: manuscripts
permalink: /publication/2026-02-10-trip-protocol-02
excerpt: 'IETF Internet-Draft revision 02 — adds RATS Architecture mapping, Active Verification Protocol, accessibility considerations for low-mobility users, and deterministic CBOR encoding. Addresses review feedback from Muhammad Usama Sardar and Jun Zhang.'
date: 2026-02-10
venue: 'IETF Datatracker (Individual Submission)'
paperurl: 'https://datatracker.ietf.org/doc/draft-ayerbe-trip-protocol/'
citation: 'Ayerbe Posada, C. (2026). &quot;TRIP: Trajectory-based Recognition of Identity Proof.&quot; <i>Internet-Draft draft-ayerbe-trip-protocol-02</i>, IETF.'
---

## Abstract

This document specifies the Trajectory-based Recognition of Identity Proof (TRIP) protocol, a decentralized mechanism for establishing claims of physical-world presence through cryptographically signed, spatially quantized location attestations called "breadcrumbs." Breadcrumbs are chained into an append-only log, bundled into verifiable epochs, and distilled into a Trajectory Identity Token (TIT) that serves as a persistent, privacy-preserving identifier for any authentication layer.

## Changes from -01

- Expanded RATS Architecture mapping (RFC 9334) with Verifier trust model
- Active Verification Protocol with challenge-response and CDDL schemas
- Corrected privacy model: quantization-based, not data-locality
- Deterministic CBOR signing (RFC 8949 §4.2), replaced canonical JSON
- H3 resolution range 7–10, default resolution 10
- Sybil Resistance section (replaces unlinkability language)
- Accessibility and Low-Mobility Users section (§15.5)
- Deployment Considerations with multi-Verifier support

## Acknowledgements

Revision 02 addresses review feedback from Muhammad Usama Sardar (RATS role mapping, attestation-result replay protection) and Jun Zhang (mobility accessibility concerns).

## Links

- **IETF Datatracker:** [draft-ayerbe-trip-protocol](https://datatracker.ietf.org/doc/draft-ayerbe-trip-protocol/)
- **Source Code:** [github.com/GNS-Foundation/trip-protocol](https://github.com/GNS-Foundation/trip-protocol)
- **IPR Disclosure:** [IETF IPR #7153](https://datatracker.ietf.org/ipr/7153/)
