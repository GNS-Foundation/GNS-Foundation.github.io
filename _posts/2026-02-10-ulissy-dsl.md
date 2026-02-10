---
title: 'Why I Built a Programming Language for Moving Machines'
date: 2026-02-10
permalink: /posts/2026/02/ulissy-dsl/
excerpt: 'On why moving machines deserve their own programming language, and what Odysseus has to do with it.'
tags:
  - ULissy
  - programming languages
  - Rust
  - DSL
  - TRIP
---

*On why moving machines deserve their own programming language, and what Odysseus has to do with it.*

---

I need to tell you about a mistake I made.

For two years, I've been building a protocol called TRIP — a system that proves you're human by the cryptographic trace of your movement through the physical world. No iris scans, no passwords, no central authority. Just signed breadcrumbs collected over time. I submitted it as an Internet-Draft to the IETF, and it's now in its third revision.

To build TRIP, I wrote a lot of Rust. Thousands of lines of it. Rust is a magnificent language — fast, safe, expressive. I love it. And I was drowning in it.

Not because Rust is bad. Because my problem was different from the problems Rust was designed for.

---

## The five things every moving machine needs

Here's what I discovered building the TRIP reference implementation, the GNS Browser mobile app, and the backend infrastructure. Every single application that deals with devices moving through the physical world needs to coordinate five concerns simultaneously:

**Location.** Where am I? But not as raw GPS coordinates — those are privacy catastrophes. As a quantized spatial cell that reveals *area* without revealing *position*.

**Identity.** Who am I? Not as a username or OAuth token, but as a cryptographic key pair. My public key *is* my identity. No password, no email, no account.

**Time.** When? Not as a Unix timestamp, but as durations, intervals, schedules. "Every ten minutes." "Within the last hour." "Since my last epoch."

**Cryptography.** Every piece of data must be signed, and certain data must be encrypted, and the key exchange must be forward-secret, and the nonces must never repeat, and if you get any of this wrong, the entire system is meaningless.

**Energy.** The phone has a battery. It dies. A protocol that drains the battery to prove you're human has proven nothing except that the protocol designer never carried a phone in Rome in August.

In Rust, coordinating these five things requires importing libraries for each one, writing glue code between them, and hoping the interaction patterns don't produce bugs that only surface at 2 AM in production when someone's phone is at 3% battery on a train between Milan and Florence.

I wrote that code. All 10,000 lines of it. It worked. It was also fragile, verbose, platform-specific in places, and nearly impossible for anyone else to audit against the TRIP specification.

So I asked a question that felt reckless at the time: *What if these five things were language primitives?*

---

## What a spatial-first language looks like

The language I built is called ULissy. The name honors Ulysses — Odysseus — the mythological figure who proved his identity not through credentials but through his journey. In ULissy, as in the Odyssey, trajectory is identity.

Here's what breadcrumb collection looks like in ULissy:

```
identity me = Keychain.primary

every 10.minutes when battery > 20% {
    let cell = here.h3(10)
    let crumb = breadcrumb(
        cell: cell,
        time: now,
        context: environment.digest
    ).signed(me)

    chain.append(crumb)
}
```

Nine lines. Read it out loud. You almost don't need to be a programmer to understand what it does.

Now here's the equivalent in Rust:

```rust
let identity = storage
    .list_identities()
    .await?
    .into_iter()
    .find(|i| i.is_default)
    .ok_or(GnsError::NoIdentity)?;

let mut interval = tokio::time::interval(Duration::from_secs(600));
loop {
    interval.tick().await;

    let battery = battery_manager.get_level().await?;
    if battery < 0.2 { continue; }

    let location = gps_provider
        .get_current_location()
        .await
        .map_err(|e| GnsError::Location(e))?;

    let lat_lng = LatLng::new(location.latitude, location.longitude)
        .map_err(|_| GnsError::InvalidCoordinate)?;
    let cell = lat_lng.to_cell(Resolution::Ten);

    let context = environment::collect_digest().await?;

    let crumb = Breadcrumb {
        cell_index: cell.to_string(),
        timestamp: Utc::now().timestamp() as u64,
        context_digest: context,
        prev_hash: chain.last_hash(),
        signature: vec![],
    };

    let hash = sha256(&crumb.to_deterministic_cbor()?);
    let signature = identity.secret_key.sign(&hash);
    crumb.signature = signature.to_bytes().to_vec();

    chain.append(crumb).await?;
}
```

Thirty-five lines. Every error case manually handled. Platform-specific GPS access. Manual serialization. Manual signing. Manual battery management. And this is the *simplified* version — the real code has retry logic, connectivity checks, and platform abstraction layers.

The ULissy version and the Rust version compile to the same binary. The same performance. The same security. But one reads like a specification, and the other reads like an implementation.

---

## Why a language, not a library?

This is the question every experienced developer asks, and it's the right one. Libraries exist. SDKs exist. Why build an entire language?

Three reasons.

**First: compile-time invariants.** In ULissy, certain things are not just conventions — they're grammar. You cannot construct a breadcrumb without signing it. The type system won't let you. You cannot access raw GPS coordinates; the compiler only gives you quantized H3 cells. You cannot send a message to another identity without encrypting it. These aren't rules a developer has to remember. They're constraints the compiler enforces.

Try this in ULissy:

```
let lat = here.latitude   // Compiler error: raw coordinates not accessible
```

That error doesn't exist in Rust. In Rust, nothing prevents you from logging `location.latitude` to a file, sending it to a server, or printing it to the console. The protection exists only in documentation that developers may or may not read.

**Second: protocol fidelity.** The TRIP specification defines breadcrumbs, epochs, trust scoring, Hamiltonian energy functions, and Proof-of-Humanity certificates. In ULissy, these are language constructs. In any other language, they're library abstractions that can drift from the spec over time. When the IETF updates the draft, I update the compiler, and every ULissy program is automatically compliant. There's no "oh, we forgot to update the signing algorithm in the Python SDK" — because the signing algorithm isn't in an SDK. It's in the grammar.

**Third: cross-platform from a single source.** ULissy transpiles to Rust, which means it compiles to iOS, Android, macOS, Windows, Linux, and WebAssembly from one codebase. The Tauri framework handles the native shell. The developer writes specification-level code; the compiler handles platform differences.

---

## The compiler pipeline

ULissy is built as a chain of four crates, each doing one transformation:

**Lexer** — reads source text and produces tokens. Sixty-three token types, including domain-specific ones: `@handles` are tokens, `H3Cell` is a type literal, `10.minutes` is a duration expression. The lexer knows about the physical world.

**Parser** — turns tokens into an Abstract Syntax Tree. Seventeen statement types. The parser understands `every`, `when`, `breadcrumb`, `send to`, `search nearby` — constructs that exist in no other language because no other language was designed for machines that move.

**Type Checker** — validates the AST against the type system. This is where the security guarantees live. The type checker knows that a `Breadcrumb` must contain a signature, that an `H3Cell` cannot be converted back to coordinates, that a `Duration` cannot be zero, that an `Identity` must come from a keychain. Violations are compiler errors, not runtime exceptions.

**Code Generator** — transforms the typed AST into Rust source code, which is then compiled by `rustc` into a native binary. By transpiling to Rust rather than compiling directly to machine code, ULissy inherits fifteen years of compiler optimization engineering for free.

The pipeline runs seventy-four tests. All pass. The compiler is written entirely in Rust.

---

## What this has to do with TRIP

When I submitted the first TRIP draft to the IETF, a reviewer asked whether the protocol's mathematical framework — Parisi's statistical mechanics, Barabási's mobility models, Hamiltonian energy functions — was testable. Could someone independently implement TRIP and verify that their implementation matched mine?

This is the *interoperability* question, and it's the question that separates protocols from products.

ULissy is my answer. If the language constructs *are* the protocol constructs, then any program written in ULissy is, by construction, a conformant TRIP implementation. The compiler is the specification. The grammar is the standard.

This is not how most protocols work. Most protocols publish a spec in English, then hope that independent implementations converge. HTTP, TLS, DNS — they all went through painful interoperability testing where implementations disagreed about edge cases that the English-language spec left ambiguous.

ULissy tries a different approach: make the spec executable. If it compiles, it's correct.

---

## The name, again

Odysseus spent twenty years away from Ithaca. When he returned, he had to prove his identity — not with a document, not with a password, but with knowledge that could only come from having lived his specific life. The scar on his thigh. The construction of his bed. The trajectory of his journey.

Every mobile phone is on its own odyssey. Every device moving through the world collects experiences that no other device could have collected. ULissy is a language for writing programs that make use of that fact.

I deleted 10,000 lines of Rust and replaced them with a compiler that writes better Rust than I ever could. The programs got shorter. The guarantees got stronger. And the specification became the implementation.

Sometimes the most productive thing a programmer can do is build the tool that makes the programming unnecessary.

---

*ULissy is open source: [github.com/cayerbe/ulissy_program](https://github.com/cayerbe/ulissy_program). The TRIP protocol it implements: [datatracker.ietf.org/doc/draft-ayerbe-trip-protocol](https://datatracker.ietf.org/doc/draft-ayerbe-trip-protocol/). The language whitepaper is available in the repository.*

*This is the third in a series on identity, physics, and the protocol layer the internet never had.*
