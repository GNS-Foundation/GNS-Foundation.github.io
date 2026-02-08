---
title: 'I Wrote 10,000 Lines of Location Code. Then I Deleted It All and Built a Language.'
date: 2026-02-08
permalink: /posts/2026/02/ulissy-language/
tags:
  - programming-languages
  - rust
  - ULissy
  - GNS
  - cryptography
  - mobile-development
---

*Why mobile apps for moving machines are broken, and what a domain-specific language can do about it.*

---

Here's a confession: before I built a programming language, I built a mobile app. A decentralized identity system. Flutter on the front end, Rust on the back end, Ed25519 cryptography, H3 geospatial indexing, Stellar blockchain integration, WebSocket sync — the works. It's called GNS, and it does something no other system does: it proves you're a real human being based on how you move through the world, not by scanning your eyeball.

But somewhere around line 10,000, I realized something that changed everything.

Every single module was doing the same five things: getting the device's location, managing cryptographic keys, checking the battery, handling time intervals, and dealing with connectivity. Over and over. Different files. Different frameworks. Same logic. The messaging module needed location for proximity. The payment module needed crypto for signing. The identity module needed time for decay functions. The IoT module needed energy for power management. All of them needed all of it, all the time.

I was writing the same 250 lines of boilerplate for every feature. So I deleted it and built a language where those 250 lines are 30.

---

## The Problem Nobody Talks About

We have languages optimized for systems programming (Rust, C), for web development (JavaScript, TypeScript), for data science (Python, R), for mobile UI (Swift, Kotlin). But we don't have a language optimized for **machines that move through physical space**.

Think about what every mobile/IoT developer has to manually coordinate:

| Concern | What You Import | What Goes Wrong |
|---------|----------------|-----------------|
| Location | CoreLocation, FusedLocation | Platform-specific APIs, raw coords leak privacy |
| Identity | OAuth, JWT, keychain wrappers | Centralized, password-based, SIM-swappable |
| Cryptography | OpenSSL, libsodium, CryptoKit | Complex APIs, easy to misuse, silent failures |
| Time | Date, NSTimer, Duration | No unit types, timezone bugs, no intervals |
| Energy | UIDevice.battery, PowerManager | Afterthought, never integrated into logic |

Every location-aware app reinvents this stack. Every one of them gets at least one thing wrong — usually the cryptography.

---

## What If These Were Language Primitives?

This is ULissy. A complete breadcrumb collection program:

```
identity me = Keychain.primary

config {
    resolution: 7,
    interval: 10.minutes,
    minBreadcrumbsPerEpoch: 100
}

every config.interval when battery > 20 && gps.available {
    let crumb = breadcrumb(
        cell: here.h3(config.resolution),
        context: sensors.digest,
        previous: me.trajectory.last?.hash ?? "genesis"
    ).signed(me)

    me.trajectory.append(crumb)
}

when me.trajectory.pending >= config.minBreadcrumbsPerEpoch {
    let epoch = me.trajectory.bundleEpoch()
    network.publish(epoch)
}
```

That's it. No GPS library. No cryptography boilerplate. No battery management code. No timer setup. The language handles it.

The equivalent in Swift + CoreLocation + CryptoKit? 200+ lines with manual coordination between three frameworks and at least two delegate patterns.

The name, by the way: Ulysses. The mythological figure who proved his identity not through credentials, but through his journey. In ULissy, trajectory is identity.

---

## Privacy Enforced at Compile Time

This is the feature I'm most proud of. In ULissy, raw GPS coordinates are a **restricted type**:

```
let raw = gps.current            // Type: Coordinates (restricted)
print(raw)                       // COMPILE ERROR: Coordinates not printable
send(raw, to: server)            // COMPILE ERROR: Coordinates not transmittable

let cell = raw.h3(resolution: 10)    // Type: H3Cell (safe)
print(cell)                          // OK
send(cell, to: server)               // OK
```

You literally cannot accidentally leak precise location. The compiler won't let you. You have to explicitly quantize to an H3 hexagonal cell first — and even then, the resolution determines how much precision you're sharing. Resolution 7 (~5 km²) for general proof-of-presence. Resolution 12 (~300 m²) for delivery verification.

This isn't a linting rule. It's a type system constraint. If your code compiles, your location data is privacy-safe.

---

## The Type System of Moving Machines

ULissy's primitives aren't `int`, `string`, `bool`. They're the actual concepts that location-aware applications deal with:

**Identity:**
`PublicKey` · `Signature` · `Handle` · `TIT` · `SharedSecret`

**Spatial:**
`H3Cell` · `Location` · `Distance` · `Coordinates` (restricted)

**Temporal:**
`Moment` · `Duration` · `Interval`

**Crypto:**
`Hash` · `Ciphertext` · `Nonce`

**Energy:**
`BatteryLevel` · `PowerMode`

And they're unit-aware:

```
let d1 = 500.meters
let d2 = 2.kilometers
let d3 = d1 + d2              // OK: 2500.meters

let t = 10.minutes
let x = d1 + t                // COMPILE ERROR: cannot add Distance + Duration
```

You cannot add meters to minutes. You cannot send a `Coordinates` over the network. You cannot sign a message with someone else's key. The compiler catches categories of bugs that runtime testing misses entirely.

---

## Facet Addressing: Identity as Syntax

GNS uses a concept called "facets" — contextual sub-identities derived from a single cryptographic root. In ULissy, these are first-class syntax:

```
dix@me.post("Hello world", visibility: .public)
home@me/lights.set(brightness: 80%)
pay@merchant.request(50.USD)
email@alice.send(subject: "Meeting", body: message)
car@me.unlock()
```

Each `facet@handle` expression resolves to a deterministic key derivation. `dix@me` and `pay@me` produce different Ed25519 keypairs from the same root identity. This means your social posts, your payments, your home automation, and your email all use cryptographically isolated keys — but you manage a single identity.

No more juggling Google accounts, Apple IDs, and wallet addresses. One key to derive them all.

---

## Temporal and Spatial Constructs

Time and space are control flow:

```
// Do something every 10 minutes, but only if the battery allows it
every 10.minutes when battery > 20% and connectivity.available {
    sync()
}

// Geofencing as a language construct
when here exits homeZone {
    arm(securitySystem)
}

// Proximity
let nearby = friends.filter { distance(to: $0) < 1.kilometer }

// Timeout handling
within 10.seconds {
    let response = await server.request()
} timeout {
    showOfflineMode()
}
```

No `NSTimer`. No `Handler.postDelayed`. No `CLLocationManagerDelegate`. The temporal and spatial patterns that every mobile app needs are language-level constructs with compile-time guarantees.

---

## Encryption Is Implicit

Sending an encrypted message in ULissy:

```
send to @alice {
    message: "Meeting at 3pm"
    attachment: document
}
```

Under the hood, the compiler generates X25519 key exchange, ChaCha20-Poly1305 encryption, ephemeral key creation for forward secrecy, and Ed25519 signing. All of it. Automatically. Correctly.

The developer doesn't choose a cipher suite. They don't manage nonces. They don't worry about key rotation. The language makes the secure path the only path.

Compare this to the typical approach: import libsodium, read the docs, hope you're using `crypto_box_seal` instead of `crypto_secretbox`, remember to never reuse a nonce, manually manage key lifecycle, write tests for all of it, and pray.

---

## The Compiler

ULissy transpiles to Rust. The pipeline:

```
Source (.ul)
    │
    ▼
┌─────────┐
│  Lexer   │  64 token types: handles, facets, units, interpolation
└─────────┘
    │
    ▼
┌─────────┐
│  Parser  │  18 statement types, self/Self/config, for loops
└─────────┘
    │
    ▼
┌──────────┐
│  Types   │  Two-pass resolution, domain-specific type checking
└──────────┘
    │
    ▼
┌──────────┐
│  Codegen │  Rust emission, Cargo.toml generation, async support
└──────────┘
    │
    ▼
Generated Rust → cargo build → Native Binary
```

The compiler itself is written in Rust. Four crates: `ulissy-lexer`, `ulissy-parser`, `ulissy-types`, `ulissy-codegen`. The lexer handles 64 token types including `@handle` literals, `facet@handle` expressions, unit suffixes (`.minutes`, `.meters`, `.kilometers`), and string interpolation. The parser produces 18 statement types. The type checker does two-pass resolution for forward references and domain-specific constraint validation.

The output is clean, idiomatic Rust with a generated `Cargo.toml`. From there, you get native binaries for every platform Rust supports.

---

## 74 Tests Passing

The TRIP Protocol test suite — written entirely in ULissy — validates the complete identity lifecycle:

| Section | Tests | What It Validates |
|---------|-------|-------------------|
| TIT Vectors | 3 | SHA-256 → 16-byte Trajectory Identity Token |
| Identity Generation | 4 | Ed25519 keys, Stellar address derivation |
| Sign and Verify | 4 | Ed25519 signatures, wrong-key rejection |
| Facet Derivation | 5 | Deterministic key derivation per facet |
| Handle Validation | 12 | @handle rules (lowercase, 1-20 chars) |
| Breadcrumb Chain | 8 | Genesis block, hash linking, timestamp ordering |
| Trust Levels & Permissions | 18 | Epoch thresholds, capability gates |
| Trust Score & Decay | 14 | Parisi k=7 computation, Gaussian decay (τ₀ = 30 days) |
| Entity Speeds | 5 | Human, drone, vehicle, robot speed limits |
| Complete Lifecycle | 1 | End-to-end identity creation through trust scoring |
| **Total** | **74** | **All passing** |

These aren't toy tests. The TIT vectors match the SHA-256 output exactly. The trust decay follows the Gaussian curve from Parisi's work on relaxation in disordered systems. The handle validation covers every edge case in the GNS specification.

---

## Why Not a Library?

Fair question. Why build a language instead of a Rust crate or a Swift package?

Three reasons:

**1. Compile-time privacy guarantees.** A library can document that you shouldn't transmit raw coordinates. A language can make it impossible. `Coordinates` is not `Transmittable`. Period. The compiler rejects it. No discipline required.

**2. Cross-cutting concerns.** Location, crypto, time, and energy aren't independent modules — they interact constantly. A breadcrumb needs location + crypto + time. A sync needs connectivity + energy + time. A payment needs crypto + identity + connectivity. In a library, you compose these manually. In a language, they compose through the type system.

**3. Protocol compliance by construction.** The GNS specification says breadcrumbs must be chained, signed, and H3-quantized. In ULissy, the `Breadcrumb` type has invariants:

```
type Breadcrumb {
    index:      Uint64
    timestamp:  Moment
    cell:       H3Cell
    context:    Hash
    previous:   Hash
    signature:  Signature

    invariant timestamp > previous.timestamp
    invariant signature.valid(for: self, by: owner)
    invariant cell.reachable(from: previous.cell, within: timestamp - previous.timestamp)
}
```

If it compiles, it's a valid breadcrumb. You can't create an invalid one. The type system is the specification.

---

## What's Next

ULissy is open source under MIT. The compiler is at [github.com/GNS-Foundation](https://github.com/GNS-Foundation), with a VS Code extension for syntax highlighting. The TRIP Protocol that ULissy implements is an active IETF Internet-Draft ([draft-ayerbe-trip-protocol-01](https://datatracker.ietf.org/doc/draft-ayerbe-trip-protocol/)).

The roadmap: WebAssembly target for browser-based GNS applications, a Tauri plugin for desktop integration, and expanding the standard library to cover mesh networking and peer-to-peer attestation.

If you're building anything that moves through physical space — fleet tracking, delivery verification, citizen science, drone coordination, IoT — and you're tired of manually stitching together GPS libraries, crypto frameworks, and battery managers, take a look. The code you delete might be more valuable than the code you write.

---

*Camilo Ayerbe Posada is the founder of ULISSY s.r.l. and the GNS Foundation. He builds identity infrastructure for the physical world from Rome, Italy.*

*GitHub: [GNS-Foundation](https://github.com/GNS-Foundation) · Medium: [@camilo.ayerbe](https://medium.com/@camilo.ayerbe) · IETF: [draft-ayerbe-trip-protocol](https://datatracker.ietf.org/doc/draft-ayerbe-trip-protocol/)*
