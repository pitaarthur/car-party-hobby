# Phase 2 — Game State Model

## 1. Authoritative state boundary

`GameState` is the complete authoritative mutable puzzle state.

Conceptual model:

```text
GameState
├── attemptId
├── turnNumber
├── vehicleStates
├── remainingPassengerQueue
├── servedPassengerIds
├── junctionStates
└── outcome
```

Selection, animation progress, camera position, visual highlights, and route-preview graphics are not authoritative gameplay state.

---

## 2. Initial state construction

`createInitialGameState(LevelDefinition)` conceptually performs:

1. create every vehicle in `BOARD` state from its definition,
2. copy the level's passenger IDs in declared queue order,
3. set `servedPassengerIds = []`,
4. initialize every Reactive Junction to its declared initial state,
5. set `turnNumber = 0`,
6. set `outcome = IN_PROGRESS`.

The resulting initial state must be reproducible from the same LevelDefinition.

### Restart

Restart discards the current runtime state and reconstructs the initial state from `LevelDefinition`.

It does **not** reverse individual mutations.

This guarantees FR-017 exact restoration.

---

## 3. GameState conceptual schema

```text
GameState {
  attemptId
  turnNumber
  vehicleStates: VehicleId -> VehicleState
  remainingPassengerQueue: PassengerId[]
  servedPassengerIds: PassengerId[]
  junctionStates: JunctionId -> StateId
  outcome: IN_PROGRESS | WON | DEADLOCKED
}
```

### attemptId

Identifies one play attempt. It is session metadata and does not affect gameplay determinism.

### turnNumber

Counts successfully committed player moves.

Selection changes and invalid commit attempts do not increment it.

---

## 4. Vehicle lifecycle

```text
BOARD
  │ successful atomic move
  ▼
WAITING_SLOT
  │ reaches capacity
  ▼
DEPARTED
```

No reverse transition exists in the MVP.

### BOARD → WAITING_SLOT

Occurs only when:

- the vehicle is active,
- a unique route exists,
- the complete route is valid,
- a waiting slot is available.

The assigned slot is the currently free slot with the lowest index.

### WAITING_SLOT → DEPARTED

Occurs automatically when:

```text
boardedPassengerIds.length === level.vehicleCapacity
```

Departure:

- removes the vehicle from active board/waiting participation,
- frees the waiting slot immediately,
- moves all onboard passenger IDs into `servedPassengerIds`,
- requires no exit path.

---

## 5. Passenger lifecycle

```text
QUEUE
  │ front passenger matches eligible waiting vehicle
  ▼
ONBOARD(vehicle)
  │ vehicle reaches capacity and departs
  ▼
SERVED
```

Rules:

- only the front passenger may leave the queue,
- queue reordering is forbidden,
- a passenger may board only a vehicle currently in a waiting slot,
- if several vehicles match, lowest waiting-slot index wins,
- served passengers never return to the queue.

---

## 6. Reactive Junction lifecycle

Every Reactive Junction has exactly two allowed states:

```text
A <──toggle──> B
```

During one committed move:

1. route is resolved using the **pre-move** junction states,
2. vehicle movement completes atomically,
3. every traversed Reactive Junction toggles once,
4. the new state affects only subsequent moves.

Previewing or attempting an invalid move never toggles a junction.

---

## 7. Outcome derivation

Outcome is evaluated only after automatic boarding/departure reaches a fixed point.

### WON

```text
all required passengers served
AND
no required vehicle status is BOARD
AND
no required vehicle status is WAITING_SLOT
```

### DEADLOCKED

```text
outcome != WON
AND
legalPlayerMoves.length === 0
```

### IN_PROGRESS

Any non-terminal normalized state with at least one legal player move.

---

## 8. Normalized state

A GameState is **normalized** when no immediate automatic boarding or departure remains unresolved.

Player input is accepted only in a normalized `IN_PROGRESS` state.

This produces a useful domain boundary:

```text
PLAYER DECISION
     │
     ▼
ATOMIC MOVE
     │
     ▼
AUTO-RESOLUTION
     │
     ▼
NORMALIZED STATE
     │
     ├── WON
     ├── DEADLOCKED
     └── IN_PROGRESS → next player decision
```

---

## 9. InteractionState

Conceptual model:

```text
InteractionState {
  selectedVehicleId: VehicleId | null
}
```

Rules:

- selection may change without changing `turnNumber`,
- selecting a vehicle generates a derived MovePreview,
- selecting a different vehicle replaces the preview,
- committing requires the currently selected vehicle,
- after a successful commit, selection resets to null,
- after restart, selection resets to null.

---

## 10. State ownership rule

Gameplay logic owns GameState.

Rendering and animation may observe GameState and derived transition results, but must not independently alter:

- vehicle lifecycle,
- passenger queue,
- boarded passengers,
- served passengers,
- junction state,
- outcome,
- turn number.

This directly satisfies NFR-008 state integrity.
