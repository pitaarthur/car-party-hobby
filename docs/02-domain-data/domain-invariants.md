# Phase 2 — Domain Invariants

These invariants are normative constraints for Phase 3 architecture and later implementation/tests.

---

## INV-01 — Deterministic state transition

Given the same:

- LevelDefinition,
- normalized GameState,
- committed VehicleId,

the resulting normalized next GameState must be identical.

No hidden RNG may participate.

## INV-02 — Exclusive vehicle location

Every vehicle is in exactly one status:

- BOARD
- WAITING_SLOT
- DEPARTED

Never more than one.

## INV-03 — Exclusive passenger location

Every passenger exists in exactly one runtime partition:

- remaining queue,
- onboard exactly one waiting vehicle,
- served.

## INV-04 — Queue stability

Passenger queue order can change only by removing the current front passenger.

No insertion, shuffling, or middle removal exists in the MVP.

## INV-05 — Waiting-slot uniqueness

At most one WAITING_SLOT vehicle may reference a given slot ID.

## INV-06 — Stable waiting-slot priority

Waiting-slot indices are unique and immutable for the session.

Lower index always has higher assignment and boarding tie-break priority.

## INV-07 — Junction state validity

Every runtime Reactive Junction state must be one of the exactly two state IDs defined by that junction.

## INV-08 — No stopping on Reactive Junction

No vehicle board destination, staging-entry destination, or waiting slot may resolve onto a Reactive Junction node.

Reactive Junctions are traversal-only.

## INV-09 — Departure is terminal

A DEPARTED vehicle can never become active again during the same attempt.

Restart creates a new initial attempt instead of reversing departure.

## INV-10 — Departure has no exit-path side effect

MVP departure is immediate removal from active gameplay state.

It cannot block a lane, traverse a junction, or trigger another junction.

## INV-11 — Board occupancy integrity

Two active BOARD vehicles may not occupy the same logical occupancy key in a normalized state.

Static blocker occupancy may not overlap a valid active vehicle footprint.

## INV-12 — Full-path commit validity

A vehicle may transition BOARD → WAITING_SLOT only if its complete resolved route is valid at commit time.

## INV-13 — Unique route

For a movable active vehicle, route resolution produces at most one route to its staging entry under the current junction state.

Multiple simultaneously valid routes are invalid for the MVP.

## INV-14 — Junction timing

The committed vehicle's route is evaluated entirely with pre-move junction states.

Junction toggles apply only after the vehicle's atomic movement completes.

## INV-15 — Junction toggle cardinality

A committed move toggles each Reactive Junction it traverses at most once.

## INV-16 — Boarding eligibility

Only a vehicle currently occupying a waiting slot may board passengers.

## INV-17 — Boarding tie-breaker

If multiple waiting vehicles are eligible for the front passenger, the vehicle with the lowest slot index receives that passenger.

## INV-18 — Capacity bound

For every non-departed vehicle:

```text
0 <= onboardPassengerCount <= level.vehicleCapacity
```

A vehicle reaching capacity must be automatically departed before the state becomes player-interactive again.

## INV-19 — Normalized player-input state

Player input is accepted only when no immediate boarding/departure event remains unresolved.

## INV-20 — Outcome exclusivity

Exactly one outcome is active:

- IN_PROGRESS
- WON
- DEADLOCKED

WON and DEADLOCKED are terminal for that attempt.

## INV-21 — Win precedence

After auto-resolution, win is evaluated before deadlock.

A winning state must never also be labeled DEADLOCKED.

## INV-22 — Deadlock definition

A normalized non-winning state is DEADLOCKED iff no legal state-changing player move exists.

## INV-23 — Restart reproducibility

Restarting the same LevelDefinition always reconstructs the same logical initial GameState.

## INV-24 — Preview purity

Generating selection, route preview, blocker feedback, or predicted junction transitions must not mutate GameState.

## INV-25 — Static/runtime separation

LevelDefinition never stores mutable values such as current waiting occupancy, current passenger queue progress, current vehicle state, or current outcome.

---

## Invariant-to-requirement traceability

| Invariant group | Requirements covered |
|---|---|
| Determinism / purity | NFR-002, NFR-007, NFR-008 |
| Vehicle lifecycle | FR-009, FR-010, FR-014, FR-015 |
| Passenger lifecycle | FR-011, FR-013, FR-014 |
| Reactive Junctions | FR-007, FR-008, FR-009, GR-04, GR-05 |
| Waiting slots | FR-010, GR-06, GR-07, AC-23 |
| Outcome | FR-015, FR-016, GR-08, GR-09 |
| Restart | FR-017 |
| Preview | FR-003, FR-004, FR-005 |

ภาษาไทย: Invariant เหล่านี้คือกฎที่ implementation และ test ใน Phase หลัง ๆ ต้องรักษาไว้ หาก code ทำให้ invariant ใดแตก ต้องถือว่าเป็น domain defect ไม่ใช่เพียง UI bug
