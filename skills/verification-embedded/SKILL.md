---
name: verification-embedded
description: Defines what "verified" means for embedded/firmware work — a three-tier requirement (mock/unit tests, simulation, hardware-in-loop) plus rules on stating memory/timing/interrupt assumptions and never abbreviating hardware-facing detail. Use this whenever a change touches firmware, microcontroller code, peripheral drivers, register access, interrupt handlers, or anything that will run on physical hardware. Trigger even for small-looking firmware changes — timing and register bugs rarely show up in a diff review.
---

# Embedded Verification

Success is verified in tiers — each one catches bugs the others miss, none substitutes for
another.

## Tiers

1. **Mock/unit tests (host-side)** — HAL/peripheral access replaced with fakes, testing logic and
   state machines fast and in CI. Catches logic bugs but has no timing/register fidelity.
2. **Simulation (QEMU/Renode/etc.)** — closer register/interrupt fidelity than mocks, but still an
   approximation; bus contention, clock drift, and peripheral quirks may not be modeled.
3. **HIL / real hardware** — the only tier that confirms actual timing under load, electrical
   behavior, and silicon-specific quirks.

A change isn't "verified" until it has passed the tiers relevant to what it touches — logic-only
changes may stop at (1), anything touching timing or peripherals should reach (3).

## Requirements

- State memory, stack, timing, and interrupt-context assumptions explicitly before implementing,
  not only after something breaks.
- No silent-failure shortcuts for the sake of brevity — e.g. swallowing an error return code to
  keep code short is unacceptable even where it would "simplify" the diff.
- Any abbreviation of units, register names, active-high/active-low polarity, or timing values is
  treated as a bug, not a style choice — precision here overrides the general concision rule in
  the root CLAUDE.md.
- "Adjacent code" (per the root file's Surgical Changes rule) includes shared registers, ISR
  vectors, and global/static state — draw the boundary of "what you touched" conservatively.

