# Phase 2 — State Transition Specification

## 1. Purpose

This document converts the Phase 1 canonical turn order into explicit domain transitions.

The transition model is deterministic and sequential.

---

## 2. Commands vs derived queries

### Queries — no GameState mutation

- `selectVehicle(vehicleId)`
- `deriveMovePreview(vehicleId)`
- `validateMove(vehicleId)`
- `deriveLegalMoves()`
- `deriveOutcomeEligibility()`

### State-changing command

- `commitMove(vehicleId)`

### Session command

- `restartLevel()`

Only `commitMove` and `restartLevel` alter authoritative gameplay state.

---

## 3. Preview transition

```text
NO_SELECTION
   │ select vehicle
   ▼
SELECTED
   │ derive preview
   ▼
PREVIEW_AVAILABLE
```

Effects:

- InteractionState changes.
- GameState does not change.
- No junction toggles.
- No passenger boards.
- turnNumber does not increment.

If preview validation fails, the preview carries a blocking reason and commit is rejected.

---

## 4. CommitMove preconditions

A move may commit only when:

1. `GameState.outcome === IN_PROGRESS`
2. vehicle status is `BOARD`
3. unique route exists to that vehicle's staging entry
4. complete route is free of conflicting vehicle occupancy
5. complete route is free of static blockers
6. current Reactive Junction state permits the route
7. at least one waiting slot is free
8. selected destination/waiting slot is not a Reactive Junction

If any precondition fails:

- GameState remains byte-for-byte logically equivalent,
- turnNumber does not increment,
- no automatic resolution runs.

---

## 5. Successful move transaction

Conceptual transaction:

```text
S0 = normalized pre-move GameState

route = resolveRoute(S0, vehicle)
slot  = lowestIndexFreeWaitingSlot(S0)

S1 = moveVehicleBoardToWaiting(S0, vehicle, slot)
S2 = toggleTraversedJunctions(S1, route)
S3 = resolveAutomaticBoardingAndDeparture(S2)
S4 = deriveOutcome(S3)
S5 = incrementTurnNumber(S4)

return normalized S5
```

The externally observable result is one committed turn.

### Atomicity

Intermediate states S1–S4 are logical transition steps and are not player-interactive states.

Animation may visualize them, but player input is not accepted until normalized S5 exists.

---

## 6. Junction transition

For each unique Reactive Junction traversed by the committed route:

```text
A -> B
B -> A
```

Rules:

- use pre-move state for route resolution,
- toggle after board movement completes,
- toggle at most once per traversed junction per committed move,
- do not toggle on preview or invalid move,
- do not allow the vehicle to stop on the junction.

If a future route traverses several Reactive Junctions, each one toggles once in deterministic route order; because toggles affect only future moves, the final state is independent of animation timing.

---

## 7. Waiting-slot assignment

Free slots are calculated from current vehicle states.

```text
freeSlots = waitingSlots
  - slots occupied by WAITING_SLOT vehicles

assignedSlot = minimum(freeSlots by index)
```

Assignment happens only for a successful move.

There is no slot compaction.

---

## 8. Automatic boarding fixed-point algorithm

After a vehicle enters a waiting slot and junction toggles have been applied:

```text
repeat:
  if queue is empty:
    stop

  front = queue[0]

  eligible = waiting vehicles where:
    vehicle.matchKey == front.matchKey
    AND onboardCount < vehicleCapacity

  if eligible is empty:
    stop

  chosen = eligible vehicle with lowest waiting-slot index

  remove front passenger from queue
  append passenger to chosen vehicle onboard list

  if chosen onboardCount == vehicleCapacity:
    depart chosen immediately
    move chosen onboard passengers to servedPassengerIds
    clear its waiting slot by changing vehicle status to DEPARTED

until no automatic event remains
```

The algorithm must terminate because every boarding iteration removes one passenger from the remaining queue.

---

## 9. Outcome transition

After auto-resolution:

### Evaluate win first

```text
win =
  no required passenger remains unserved
  AND
  no required vehicle is BOARD
  AND
  no required vehicle is WAITING_SLOT
```

If true:

```text
outcome = WON
```

### Otherwise evaluate legal moves

For each `BOARD` vehicle, derive move validation.

If no valid move exists:

```text
outcome = DEADLOCKED
```

Else:

```text
outcome = IN_PROGRESS
```

Win evaluation always precedes deadlock evaluation.

---

## 10. Restart transition

```text
ANY CURRENT ATTEMPT
        │ restart
        ▼
NEW INITIAL GameState
```

Effects:

- reconstruct GameState from LevelDefinition,
- clear InteractionState,
- discard preview,
- reset turnNumber,
- restore initial junction states,
- restore initial passenger order,
- restore every vehicle to its defined board position,
- outcome becomes `IN_PROGRESS` unless the level definition itself is invalid.

---

## 11. Illegal transitions

The MVP domain forbids:

- `DEPARTED -> WAITING_SLOT`
- `DEPARTED -> BOARD`
- `WAITING_SLOT -> BOARD`
- passenger `SERVED -> QUEUE`
- passenger boarding from a non-front queue position
- passenger boarding into a BOARD vehicle
- junction toggle caused by preview
- junction toggle caused by an invalid move
- player input while auto-resolution is in progress
- destination/staging state located on a Reactive Junction

These should become explicit test cases later.
