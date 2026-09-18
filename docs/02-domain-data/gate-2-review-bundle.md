# Gate 2 Independent Review Bundle

Project: Car Party Hobby
Phase: 2 — Domain & Data
Purpose: Self-contained review packet for an external reviewer that cannot browse the repository.

---

## Review Instructions

Review the Phase 2 Domain & Data artifacts against the approved Phase 1 requirements.

Return exactly:

FINAL GATE 2 RECOMMENDATION:
PASS / PASS WITH MINOR FIXES / BLOCK

CRITICAL FINDINGS
- ...

MAJOR FINDINGS
- ...

MINOR FINDINGS
- ...

For every finding include:
- affected file
- affected entity/invariant/section
- problem
- why it matters
- exact recommended wording/model change

Finally answer:
"Is this domain model safe to hand off to Phase 3 Architecture?"
Yes / No, with a short reason.

---

# AUTHORITATIVE UPSTREAM REQUIREMENTS

## File: docs/01-requirements/requirements.md

# Phase 1 — Requirements

**Project:** Car Party Hobby  
**Status:** ✅ Gate 1 Approved  
**Primary Role:** Business/System Analyst  
**Specific Primary Agent:** ChatGPT: Work  
**Supporting / Review:** ChatGPT: Chat + Gemini: Chat  
**Human Gate:** Gate 1 — Product Owner

---

## 1. Objective / เป้าหมาย

Translate the approved Discovery decisions into explicit gameplay requirements, functional rules, non-functional requirements, and acceptance criteria for the first playable vertical slice.

แปลง decision ที่ผ่าน Gate 0 แล้วให้เป็น gameplay requirements, functional rules, non-functional requirements และ acceptance criteria ที่ชัดเจนสำหรับ playable vertical slice แรก

## 2. Approved inputs from Gate 0

- Target: casual players of all ages
- Session length: 2–5 minutes
- Platform: Android only
- App stack constraint: React Native + TypeScript
- Prototype: Expo Go acceptable
- Movement model: lane/path-based
- Signature mechanic: Reactive Lanes
- Information model: full-information deterministic puzzle
- MVP: offline/local-first
- Vertical slice: one playable level
- No backend/login/ads/IAP/leaderboard/multiplayer in V1

---

## 3. Functional requirements

### FR-001 — Board inspection

The player must be able to inspect the complete current puzzle state required for a meaningful decision before committing a move.

ผู้เล่นต้องสามารถมองเห็นสถานะ puzzle ปัจจุบันที่จำเป็นต่อการตัดสินใจก่อน commit move

### FR-002 — Vehicle selection

The player must be able to tap/select a vehicle that remains active on the board.

ผู้เล่นต้องสามารถแตะ/เลือก vehicle ที่ยัง active อยู่บนกระดานได้

### FR-003 — Route preview

Selecting a vehicle must show its intended current path from its current position to its next destination/staging point.

เมื่อเลือก vehicle ระบบต้องแสดง path ปัจจุบันจากตำแหน่งรถไปยัง destination/staging point ถัดไป

### FR-004 — Blocking feedback

If the vehicle cannot move, the game must indicate the first blocking point or blocking reason.

หากรถเคลื่อนไม่ได้ ระบบต้องแสดงจุดแรกหรือเหตุผลแรกที่ทำให้ถูก block

### FR-005 — Move commit interaction

The MVP uses a two-step tap interaction:

1. First tap selects a vehicle and shows its preview.
2. Tapping the same selected vehicle again commits the move if valid.
3. Tapping another vehicle changes selection and preview.
4. An invalid move must not mutate gameplay state.

MVP ใช้ interaction แบบสองขั้น: แตะครั้งแรกเพื่อเลือกและ preview และแตะรถคันเดิมอีกครั้งเพื่อ commit move หาก valid

### FR-006 — Lane/path validation

Before a move can be committed, the game must validate the complete required path against:

- current vehicle occupancy,
- static blockers,
- current Reactive Junction state,
- destination/staging availability.

ก่อน commit ระบบต้องตรวจ path ทั้งเส้นที่จำเป็นกับ occupancy, blocker, junction state และ destination availability

### FR-007 — Reactive Junction state

At least one junction in the vertical slice must have exactly two deterministic states.

Reactive Junction ใน vertical slice อย่างน้อย 1 จุดต้องมี 2 state ที่ deterministic

### FR-008 — Junction change preview

Before committing a move, the player must be able to see whether that move will trigger a Reactive Junction and what its next state will be.

### FR-009 — Junction trigger resolution

A committed route that traverses a configured Reactive Junction must toggle that junction exactly once after the vehicle completes its movement.

รถที่ traverse Reactive Junction ตาม route ที่ commit แล้วจะ trigger junction หนึ่งครั้งหลัง movement เสร็จ

### FR-010 — Waiting/parking capacity

The level must include a finite number of staging/waiting slots. A board vehicle that requires staging may move only if it can legally reach an available slot.

### FR-011 — Passenger queue and matching

Passengers are represented as a fully visible ordered queue.

A vehicle may automatically board passengers only from the **front of the queue**, and only while the front passenger color/type matches that vehicle.

Passenger เป็น queue ที่มองเห็นลำดับทั้งหมด และรับขึ้นรถจากหัวคิวเท่านั้นเมื่อสี/ประเภทตรงกับรถ

### FR-012 — Vehicle capacity

For the first vertical slice, all passenger-carrying vehicles use a **fixed capacity defined by the level**.

The capacity value is visible when relevant. Per-vehicle variable capacities are deferred beyond the first vertical slice.

Vertical slice แรกใช้ capacity แบบค่าคงที่ระดับ level เพื่อลด complexity ส่วน capacity ต่างกันรายคันเลื่อนไปหลัง MVP

### FR-013 — Automatic boarding resolution

After every successful vehicle arrival and after every queue change, the game must automatically resolve all immediately available deterministic boarding events before accepting the next player move.

### FR-014 — Waiting vehicle departure

A waiting vehicle departs and frees its slot when it reaches its required capacity.

If it has not reached capacity, it remains in its slot and may automatically board additional matching passengers later when they reach the front of the queue.

### FR-015 — Win detection

The game must detect the win state when:

1. all required passengers have boarded and departed, **and**
2. all required active vehicles for the level have cleared the board/waiting area.

### FR-016 — Lose/deadlock detection

After automatic resolution is exhausted, the game must detect deadlock when:

1. the level is not already won, **and**
2. there is no legal player move that would mutate gameplay state.

A legal move is one that can complete its required path and legally resolve its destination/staging requirement under the current state.

### FR-017 — Restart

The player must be able to restart the level to its exact initial deterministic state, including:

- vehicle positions,
- passenger queue,
- waiting slots,
- Reactive Junction states,
- completion state.

### FR-018 — Local-only runtime

The MVP must function without account login, cloud services, or network/backend dependencies.

---

## 4. Approved gameplay rules

### GR-01 — Path occupancy

**Decision: occupied-footprint blocking + full-path validation.**

Idle vehicles block only the lane/path footprint they currently occupy.

Before a selected vehicle commits, the game validates its complete required route. Because MVP movement is sequential and atomic, there is no persistent "reserved next segment" state between player turns.

รถที่ยังไม่เคลื่อน block เฉพาะพื้นที่ที่ครอบครองจริง ระบบตรวจเส้นทางทั้งหมดก่อน commit และไม่มีการ reserve segment ข้าม turn

**Reasoning:** keeps the state deterministic without introducing traffic-simulation concurrency.

---

### GR-02 — Intersection priority

**Decision: no simultaneous right-of-way system in MVP.**

Only the currently selected vehicle is evaluated for movement. An intersection is traversable when:

- the selected route permits it,
- its current Reactive Junction state permits it,
- no blocking vehicle/static obstacle occupies the required path.

There is no traffic-light timing, speed comparison, or simultaneous vehicle priority in the first vertical slice.

**Reasoning:** the puzzle is about sequence selection, not real-time traffic arbitration.

---

### GR-03 — Move atomicity

**Decision: moves are atomic.**

A committed vehicle moves from its current location to its resolved next destination/staging point as one gameplay transaction.

A vehicle cannot be intentionally stopped mid-path by the player.

Animation may visually show travel through path segments, but the underlying rule resolves one atomic move.

**Reasoning:** visual animation must not create intermediate gameplay states that change the puzzle rules.

---

### GR-04 — Reactive Junction trigger

**Decision: traversal trigger.**

A Reactive Junction triggers when the committed route **traverses** that junction.

For the MVP:

- one route traversal causes at most one toggle of that junction,
- merely selecting/previewing a route does not trigger it,
- an invalid/blocked move does not trigger it,
- a vehicle's final destination or staging/waiting slot **cannot be located on a Reactive Junction**; vehicles may only traverse Reactive Junctions.

สำหรับ MVP จุดหมายปลายทางหรือ waiting/staging slot ต้องไม่อยู่บน Reactive Junction รถสามารถวิ่งผ่าน Reactive Junction ได้เท่านั้น

---

### GR-05 — Junction timing

**Decision: toggle after vehicle movement completes.**

The junction state used to calculate the current vehicle's route remains fixed for that move.

After the vehicle reaches its destination/staging point, each triggered Reactive Junction toggles before the next player decision.

**Reasoning:** the current vehicle cannot reroute itself halfway through the move; Reactive Lanes affect future moves.

---

### GR-06 — Passenger boarding order

**Decision: visible front-of-queue automatic boarding.**

Passengers form an ordered queue visible to the player.

For each boarding resolution:

1. inspect the front passenger,
2. if a vehicle **currently occupying a waiting slot** of the matching color/type is eligible, board that passenger,
3. repeat while the next front passenger can board deterministically,
4. stop when the front passenger has no eligible vehicle.

If multiple waiting vehicles are equally eligible for the same front passenger, the vehicle occupying the **earliest waiting-slot index** receives boarding priority.

**Reasoning:** this provides a deterministic tie-breaker and makes queue order strategically meaningful.

---

### GR-07 — Waiting slot behavior

**Decision: first-available slot + stay-until-full.**

- A vehicle that reaches staging occupies the lowest-index available waiting slot.
- Boarding resolves immediately after arrival.
- A vehicle remains in that slot until its fixed capacity is reached.
- When full, it departs automatically and frees the slot.
- For the MVP, **departure means the vehicle is immediately removed/despawned from the active gameplay state without requiring an exit route**; the waiting slot becomes free immediately.
- Slot ordering remains stable; remaining vehicles do not need to compact/reorder in the MVP.

สำหรับ MVP คำว่า departure หมายถึงรถถูกนำออกจาก active gameplay state ทันทีโดยไม่ต้องคำนวณเส้นทางออก และ waiting slot ว่างทันที

**Reasoning:** slot state is visible, stable, and easy to predict.

---

### GR-08 — Deadlock definition

**Decision: no legal state-changing move after automatic resolution.**

The game first resolves all automatic boarding/departure events.

Then:

```
deadlock =
  !win &&
  legalPlayerMoves.length === 0
```

A legal player move must:

- have a valid complete path,
- satisfy current junction/path constraints,
- have a valid destination or available waiting slot,
- cause an actual state transition.

This definition intentionally avoids solver-based look-ahead in the MVP.

**Example deadlock:** all required staging slots are occupied, the front passenger matches none of the waiting vehicles, and every remaining board vehicle is blocked or has no legal destination.

---

### GR-09 — Win condition

**Decision: passengers served + required vehicles cleared.**

The level is won only when:

1. there are no unserved required passengers remaining,
2. no required vehicle remains active on the board,
3. no required vehicle remains in a waiting slot.

This prevents a "win" while unresolved vehicles still occupy the gameplay state.

---

## 5. Canonical turn-resolution order

Every committed move follows this exact logical sequence:

1. **Select** vehicle.
2. **Preview** route, blocker status, destination, and predicted Reactive Junction changes.
3. **Validate** the complete move.
4. **Commit** the move.
5. **Move** vehicle atomically to its destination/staging point.
6. **Toggle** any Reactive Junction traversed by the committed route.
7. **Assign waiting slot** if required.
8. **Resolve automatic passenger boarding** from the visible queue.
9. **Depart full vehicles** and free their slots.
10. Repeat automatic boarding/departure until no further automatic event is possible.
11. **Evaluate win**.
12. If not won, generate legal player moves.
13. If zero legal moves remain, **evaluate deadlock/lose**.
14. Otherwise return control to the player.

ภาษาไทยโดยสรุป:

**Preview → Validate → Commit → Move → Toggle Junction → Waiting Slot → Boarding → Departure → Win/Deadlock → Next Turn**

This order is normative for Phase 2 domain modeling and later automated tests.

---

## 6. Non-functional requirements

### NFR-001 — Responsiveness

Selection feedback and route preview should appear without perceptible delay on typical Android phones targeted by the project.

### NFR-002 — Determinism

The same initial level state and same committed move sequence must produce the same gameplay result.

### NFR-003 — Explainability

A player must be able to understand why a move is valid or blocked using visible feedback.

### NFR-004 — Short-session design

The first level should be completable within the intended 2–5 minute session window after the player understands the controls.

### NFR-005 — Offline operation

The vertical slice must remain fully playable with no network connection.

### NFR-006 — Testability

Core rules must be representable independently of rendering so later phases can execute automated state-transition tests.

### NFR-007 — No hidden RNG

The vertical slice must not use hidden random values that alter route validity, passenger order, junction behavior, win state, or deadlock state.

### NFR-008 — State integrity

A visual animation must not independently mutate authoritative gameplay state outside the approved turn-resolution rules.

---

## 7. Vertical-slice acceptance criteria

The Phase 1 specification must support one level satisfying all of the following:

1. The player can inspect all information required to make a deterministic decision.
2. At least two vehicles create a meaningful ordering decision.
3. Selecting a vehicle previews its complete current route.
4. A blocked vehicle clearly identifies its first blocker/reason.
5. A valid move uses the two-step select → commit interaction.
6. At least one move is constrained by path occupancy.
7. At least one Reactive Junction has two visible states.
8. At least one successful move traverses and toggles that junction.
9. The junction's predicted next state is visible before commit.
10. The toggled junction changes the legal route or move availability of a later vehicle.
11. Waiting capacity is finite and affects at least one decision.
12. Passengers are shown as a visible ordered queue.
13. Boarding occurs only from the front of that queue using deterministic color/type matching.
14. At least one vehicle must wait because it cannot yet become full.
15. A full waiting vehicle departs automatically and frees its slot.
16. The level has a reproducible win state.
17. The level has a reproducible deadlock/lose state.
18. Restart restores the exact initial state.
19. Replaying the same move sequence produces the same result.
20. The complete experience works offline.
21. No hidden RNG is required.
22. A typical successful playthrough can fit the intended 2–5 minute session target.
23. If multiple waiting vehicles match the front passenger, the vehicle in the **lowest-index waiting slot** deterministically receives the passenger.

---

## 8. Gate 1 decision register

| Decision | Status | Approved rule |
|---|---|---|
| Path occupancy rule | ✅ Closed | Idle vehicles block occupied footprint; full path validated before atomic move |
| Intersection priority | ✅ Closed | No simultaneous priority; selected vehicle evaluated against current state |
| Move atomicity | ✅ Closed | Atomic source → destination/staging move |
| Reactive Junction trigger | ✅ Closed | Toggle on successful traversal |
| Junction timing | ✅ Closed | Toggle after movement completes, before next player decision |
| Boarding order | ✅ Closed | Visible queue; front-only deterministic matching; earliest waiting slot wins ties |
| Waiting slot behavior | ✅ Closed | Lowest-index free slot; vehicle stays until full; full vehicle departs |
| Deadlock definition | ✅ Closed | Not won + no legal player move after automatic resolution |
| Win condition | ✅ Closed | All required passengers served + all required vehicles cleared |

---

## 9. Explicitly deferred beyond Phase 1

The following are intentionally **not** required to pass Gate 1:

- exact numeric board dimensions,
- exact number of passenger colors,
- exact fixed vehicle capacity value,
- data/schema representation of lanes and junctions,
- rendering library,
- animation library,
- state-management library,
- audio behavior,
- art direction,
- advanced Reactive Junction variants,
- variable vehicle capacities,
- chained/timed/random junctions,
- level progression/meta-game.

These belong to Phase 2–4 or later.

---

## 10. Gate 1 exit criteria

| Criterion | Status |
|---|---|
| Functional requirements complete enough for domain modeling | ✅ Pass |
| Core gameplay rules closed or explicitly deferred | ✅ Pass |
| Win state unambiguous | ✅ Pass |
| Deadlock state unambiguous | ✅ Pass |
| Reactive Lanes behavior deterministic | ✅ Pass |
| Turn-resolution order defined | ✅ Pass |
| Vertical-slice acceptance criteria testable | ✅ Pass |
| Architecture/library choices avoided | ✅ Pass |

## 11. Independent review / ผลการ Review

**Reviewer:** Gemini: Chat  
**Result:** ✅ **PASS WITH MINOR FIXES**  
**Reported by:** Product Owner

### Review findings and resolution

| Finding | Severity | Resolution |
|---|---|---|
| Vehicle could ambiguously stop on a Reactive Junction | Major | ✅ GR-04 now forbids destinations/staging slots on Reactive Junctions |
| "Departure" behavior was undefined | Major | ✅ GR-07 now defines departure as immediate despawn/removal with no exit-route calculation |
| Boarding eligibility could include non-slot vehicles | Minor | ✅ GR-06 now restricts boarding to vehicles currently occupying waiting slots |
| Boarding tie-breaker lacked explicit acceptance coverage | Minor | ✅ AC-23 added for lowest-index waiting-slot priority |

All reviewer findings have been incorporated. No Critical findings remain, and the requirements are now accepted for handoff to Phase 2 — Domain & Data.

ข้อเสนอแนะจาก Independent Review ถูกแก้ครบทั้ง 4 จุดแล้ว ไม่มี Critical finding คงค้าง และ requirement พร้อมส่งต่อไป Phase 2 — Domain & Data

---

**Gate 1 status:** ✅ **APPROVED — REVIEW FINDINGS RESOLVED**

**Next after approval:** Phase 2 — Domain & Data.


---

# PHASE 2 ARTIFACTS

## File: docs/02-domain-data/domain-model.md

# Phase 2 — Domain Model

**Project:** Car Party Hobby  
**Phase:** 2 — Domain & Data  
**Status:** Draft complete — Gate 2 review pending

---

## 1. Modeling principles / หลักการออกแบบ Domain

The domain model is technology-agnostic. It describes gameplay meaning and state relationships without assuming React Native components, a state-management library, or a rendering engine.

Domain model นี้ไม่ผูกกับ UI/rendering library และแยกข้อมูลออกเป็นสองกลุ่มหลัก:

1. **LevelDefinition — immutable/static definition**  
   ข้อมูลที่นิยามด่านและไม่เปลี่ยนระหว่างการเล่นรอบหนึ่ง

2. **GameState — mutable authoritative runtime state**  
   สถานะจริงของเกมที่เปลี่ยนจาก move และ automatic resolution

Derived UI state such as selection and route preview is not authoritative gameplay state.

---

## 2. Aggregate root

### GameSession

A game session combines:

- one immutable `LevelDefinition`,
- one mutable `GameState`,
- transient `InteractionState`,
- derived `MovePreview`.

```text
GameSession
├── LevelDefinition (immutable)
├── GameState      (authoritative mutable state)
├── InteractionState (transient)
└── MovePreview    (derived)
```

**Rule:** Only approved domain transitions may mutate `GameState`.

---

## 3. Core entities

### 3.1 LevelDefinition

Represents one deterministic puzzle definition.

Fields:

- `levelId` — stable unique level identifier
- `schemaVersion`
- `vehicleCapacity` — fixed capacity for all passenger vehicles in the MVP
- `pathNetwork`
- `reactiveJunctions`
- `waitingSlots`
- `staticBlockers`
- `vehicles`
- `passengers`
- `initialJunctionStates`

The level definition is immutable during one game session.

### 3.2 VehicleDefinition

Static identity and starting configuration for a vehicle.

Fields:

- `vehicleId`
- `matchKey` — passenger matching value such as a color/type key
- `startNodeId`
- `initialOccupancyKeys[]`
- `targetStagingEntryNodeId`
- `requiredForWin` — true for all MVP vehicles unless explicitly stated otherwise

A vehicle definition does not store its changing runtime location.

### 3.3 PassengerDefinition

Represents one passenger token in the ordered queue.

Fields:

- `passengerId`
- `matchKey`

Passenger identity is stable even after boarding/departure.

### 3.4 PathNetwork

Logical directed network used for route resolution.

Contains:

- `nodes[]`
- `segments[]`

A path is derived from enabled directed segments under the current Reactive Junction states.

### 3.5 PathNode

Fields:

- `nodeId`
- `kind`

Allowed MVP kinds:

- `ENTRY`
- `WAYPOINT`
- `JUNCTION`
- `STAGING_ENTRY`

A `JUNCTION` node may be associated with exactly one `ReactiveJunctionDefinition`.

A final destination/staging entry must not be a `JUNCTION`.

### 3.6 PathSegment

Directed connection between two nodes.

Fields:

- `segmentId`
- `fromNodeId`
- `toNodeId`
- `occupancyKeys[]`

`occupancyKeys` are logical collision-space identifiers. They are domain-level occupancy units, not pixels.

A route is blocked when its required occupancy keys intersect another active board vehicle's occupied keys or a static blocker's keys.

### 3.7 ReactiveJunctionDefinition

Static definition of a two-state Reactive Junction.

Fields:

- `junctionId`
- `nodeId`
- `stateIds[2]`
- `enabledOutgoingSegmentsByState`

Example meaning:

```text
state A -> north-to-east enabled
state B -> north-to-west enabled
```

The runtime state is stored separately in `GameState.junctionStates`.

### 3.8 WaitingSlotDefinition

Fields:

- `slotId`
- `index`

Rules:

- index is unique within a level,
- lower index has higher deterministic boarding/assignment priority,
- waiting slots are outside Reactive Junction nodes,
- slot order never changes during a session.

### 3.9 StaticBlockerDefinition

Fields:

- `blockerId`
- `occupancyKeys[]`

Static blockers never move or change state in the MVP.

---

## 4. Runtime entities

### 4.1 VehicleState

Each vehicle has exactly one runtime location category:

```text
BOARD
WAITING_SLOT
DEPARTED
```

Conceptual union:

```text
VehicleState =
  | BoardVehicleState
  | WaitingVehicleState
  | DepartedVehicleState
```

#### BoardVehicleState

- `status = BOARD`
- `vehicleId`
- `nodeId`
- `occupiedKeys[]`
- `boardedPassengerIds[]`

For the first vertical slice, a board vehicle normally has zero boarded passengers.

#### WaitingVehicleState

- `status = WAITING_SLOT`
- `vehicleId`
- `slotId`
- `boardedPassengerIds[]`

#### DepartedVehicleState

- `status = DEPARTED`
- `vehicleId`
- `servedPassengerIds[]`

Departure is terminal for the MVP.

### 4.2 Passenger runtime partition

Passenger state is represented through three mutually exclusive locations:

- `remainingPassengerQueue[]` — ordered passenger IDs still waiting
- passenger IDs inside a waiting vehicle's `boardedPassengerIds[]`
- `servedPassengerIds[]` — passengers whose full vehicle departed

Every passenger must exist in exactly one of these partitions.

### 4.3 JunctionState

Fields:

- `junctionId`
- `stateId`

The state ID must belong to the corresponding two-state junction definition.

### 4.4 GameOutcome

Allowed values:

- `IN_PROGRESS`
- `WON`
- `DEADLOCKED`

`WON` and `DEADLOCKED` are terminal outcomes for the current attempt.

---

## 5. Interaction and derived value objects

### 5.1 InteractionState

Transient, non-authoritative UI interaction state.

Fields:

- `selectedVehicleId | null`

Changing selection does not mutate `GameState`.

### 5.2 MovePreview

Derived from:

- `LevelDefinition`
- current `GameState`
- selected `vehicleId`

Fields:

- `vehicleId`
- `routeSegmentIds[]`
- `routeOccupancyKeys[]`
- `assignedWaitingSlotId | null`
- `traversedJunctionIds[]`
- `junctionTransitions[]`
- `validation`

A preview is disposable and may be recomputed at any time.

### 5.3 MoveValidationResult

Conceptual shape:

```text
MoveValidationResult
├── valid: boolean
├── reasonCode
├── firstBlocker
└── destinationSlotId
```

Suggested reason codes:

- `NONE`
- `VEHICLE_NOT_ACTIVE`
- `NO_ROUTE`
- `PATH_OCCUPIED`
- `STATIC_BLOCKER`
- `NO_WAITING_SLOT`
- `AMBIGUOUS_ROUTE`

The domain must expose enough information for FR-004 blocking feedback.

---

## 6. Relationships / ความสัมพันธ์

```text
LevelDefinition
├── owns PathNetwork
│   ├── PathNodes
│   └── PathSegments
├── owns ReactiveJunctionDefinitions
│   └── references JUNCTION PathNode
├── owns WaitingSlotDefinitions
├── owns StaticBlockerDefinitions
├── owns VehicleDefinitions
│   ├── references start PathNode
│   └── references STAGING_ENTRY PathNode
└── owns PassengerDefinitions

GameState
├── vehicleStates[vehicleId]
├── remainingPassengerQueue[passengerId]
├── servedPassengerIds[passengerId]
├── junctionStates[junctionId]
└── outcome
```

No runtime entity owns a duplicate authoritative copy of a static definition.

---

## 7. Identity boundaries

All IDs are stable and unique within a `LevelDefinition`.

Required ID namespaces:

- LevelId
- VehicleId
- PassengerId
- NodeId
- SegmentId
- JunctionId
- WaitingSlotId
- BlockerId

Implementation may use strings, branded types, or another representation later; Phase 2 only requires stable identity semantics.

---

## 8. Derived indexes

The following values should be treated as derived, not independent mutable truth:

- waiting-slot occupancy — derived from `VehicleState.status === WAITING_SLOT`
- board occupancy — derived from active board vehicle occupied keys
- passenger location lookup — derived from queue/onboard/served partitions
- legal moves — derived from current GameState + LevelDefinition
- win/deadlock eligibility — derived after automatic resolution
- current route — derived from current path graph + junction states

This reduces duplicated state and prevents inconsistency.

---

## 9. MVP route determinism rule

For any active vehicle under a given valid GameState, route resolution to its staging entry must produce:

- exactly one valid route, or
- no valid route.

More than one simultaneously valid route is an invalid/ambiguous level state for the MVP.

Reactive Junction states are therefore allowed to change **which unique route exists**, but not to create player route-choice branching in the first vertical slice.

ภาษาไทย: ใน MVP ผู้เล่นเลือก “รถคันไหน” ไม่ได้เลือก “เส้นทางไหน” ระบบต้อง resolve route ได้เพียงเส้นเดียวหรือไม่มีเส้นทางเท่านั้น

---

## 10. Phase 2 decision summary

- Graph-based logical path model
- Logical occupancy keys instead of pixel collision
- Vehicle location modeled as an exclusive union: BOARD / WAITING_SLOT / DEPARTED
- Waiting-slot occupancy derived from vehicle state
- Passenger state modeled as an exclusive queue/onboard/served partition
- Reactive Junction runtime state separated from junction definition
- Selection/preview kept outside authoritative GameState
- Route resolution must be unique-or-none in the MVP


---

## File: docs/02-domain-data/game-state-model.md

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


---

## File: docs/02-domain-data/level-schema.md

# Phase 2 — Level Schema

## 1. Purpose

This document defines a technology-neutral logical schema capable of encoding the first playable vertical slice.

It is intentionally **not** a TypeScript interface, database schema, or final file format. Phase 3 may choose the concrete implementation representation.

---

## 2. Logical top-level schema

```text
LevelDefinition
├── schemaVersion
├── levelId
├── metadata
├── rules
│   └── vehicleCapacity
├── pathNetwork
│   ├── nodes[]
│   └── segments[]
├── reactiveJunctions[]
├── waitingSlots[]
├── staticBlockers[]
├── vehicles[]
├── passengers[]
└── initialJunctionStates
```

---

## 3. Field definitions

### metadata

Optional descriptive content:

- title
- difficultyLabel
- estimatedSessionSeconds

These values do not alter gameplay rules unless explicitly promoted into a rule in a later phase.

### rules.vehicleCapacity

Positive integer.

All passenger-carrying vehicles in the first vertical slice use this fixed capacity.

### pathNetwork.nodes[]

Each node:

```json
{
  "nodeId": "node-a",
  "kind": "ENTRY | WAYPOINT | JUNCTION | STAGING_ENTRY"
}
```

### pathNetwork.segments[]

Each directed segment:

```json
{
  "segmentId": "seg-a-b",
  "fromNodeId": "node-a",
  "toNodeId": "node-b",
  "occupancyKeys": ["cell-01", "cell-02"]
}
```

`occupancyKeys` must be stable logical identifiers.

### reactiveJunctions[]

```json
{
  "junctionId": "junction-01",
  "nodeId": "node-j1",
  "states": [
    {
      "stateId": "A",
      "enabledOutgoingSegmentIds": ["seg-j1-east"]
    },
    {
      "stateId": "B",
      "enabledOutgoingSegmentIds": ["seg-j1-west"]
    }
  ]
}
```

Exactly two states are allowed in the MVP.

### initialJunctionStates

```json
{
  "junction-01": "A"
}
```

### waitingSlots[]

```json
{
  "slotId": "wait-0",
  "index": 0
}
```

Indices must be unique.

### staticBlockers[]

```json
{
  "blockerId": "blocker-01",
  "occupancyKeys": ["cell-99"]
}
```

### vehicles[]

```json
{
  "vehicleId": "vehicle-red-01",
  "matchKey": "red",
  "startNodeId": "node-v1",
  "initialOccupancyKeys": ["cell-v1-a", "cell-v1-b"],
  "targetStagingEntryNodeId": "node-stage",
  "requiredForWin": true
}
```

### passengers[]

Passenger array order is the initial queue order.

```json
[
  { "passengerId": "p-001", "matchKey": "red" },
  { "passengerId": "p-002", "matchKey": "blue" },
  { "passengerId": "p-003", "matchKey": "red" }
]
```

---

## 4. Illustrative complete example

This example demonstrates representability; it is not the final tuned Level 001.

```json
{
  "schemaVersion": 1,
  "levelId": "vertical-slice-example",
  "metadata": {
    "title": "Reactive Lane Intro",
    "estimatedSessionSeconds": 180
  },
  "rules": {
    "vehicleCapacity": 2
  },
  "pathNetwork": {
    "nodes": [
      { "nodeId": "red-start", "kind": "ENTRY" },
      { "nodeId": "blue-start", "kind": "ENTRY" },
      { "nodeId": "j1", "kind": "JUNCTION" },
      { "nodeId": "east", "kind": "WAYPOINT" },
      { "nodeId": "west", "kind": "WAYPOINT" },
      { "nodeId": "stage", "kind": "STAGING_ENTRY" }
    ],
    "segments": [
      {
        "segmentId": "red-to-j1",
        "fromNodeId": "red-start",
        "toNodeId": "j1",
        "occupancyKeys": ["r1", "r2"]
      },
      {
        "segmentId": "blue-to-j1",
        "fromNodeId": "blue-start",
        "toNodeId": "j1",
        "occupancyKeys": ["b1", "b2"]
      },
      {
        "segmentId": "j1-east",
        "fromNodeId": "j1",
        "toNodeId": "east",
        "occupancyKeys": ["je1"]
      },
      {
        "segmentId": "east-stage",
        "fromNodeId": "east",
        "toNodeId": "stage",
        "occupancyKeys": ["e1"]
      },
      {
        "segmentId": "j1-west",
        "fromNodeId": "j1",
        "toNodeId": "west",
        "occupancyKeys": ["jw1"]
      },
      {
        "segmentId": "west-stage",
        "fromNodeId": "west",
        "toNodeId": "stage",
        "occupancyKeys": ["w1"]
      }
    ]
  },
  "reactiveJunctions": [
    {
      "junctionId": "junction-01",
      "nodeId": "j1",
      "states": [
        {
          "stateId": "A",
          "enabledOutgoingSegmentIds": ["j1-east"]
        },
        {
          "stateId": "B",
          "enabledOutgoingSegmentIds": ["j1-west"]
        }
      ]
    }
  ],
  "initialJunctionStates": {
    "junction-01": "A"
  },
  "waitingSlots": [
    { "slotId": "wait-0", "index": 0 },
    { "slotId": "wait-1", "index": 1 }
  ],
  "staticBlockers": [],
  "vehicles": [
    {
      "vehicleId": "vehicle-red-01",
      "matchKey": "red",
      "startNodeId": "red-start",
      "initialOccupancyKeys": ["red-body"],
      "targetStagingEntryNodeId": "stage",
      "requiredForWin": true
    },
    {
      "vehicleId": "vehicle-blue-01",
      "matchKey": "blue",
      "startNodeId": "blue-start",
      "initialOccupancyKeys": ["blue-body"],
      "targetStagingEntryNodeId": "stage",
      "requiredForWin": true
    }
  ],
  "passengers": [
    { "passengerId": "p-001", "matchKey": "red" },
    { "passengerId": "p-002", "matchKey": "red" },
    { "passengerId": "p-003", "matchKey": "blue" },
    { "passengerId": "p-004", "matchKey": "blue" }
  ]
}
```

---

## 5. Schema validation constraints

A valid MVP level must satisfy all of the following.

### Identity

- all IDs are non-empty,
- IDs are unique within their entity namespace,
- every reference resolves.

### Path network

- every segment references existing nodes,
- every vehicle start node exists,
- every target staging entry node exists and has kind `STAGING_ENTRY`,
- a Reactive Junction references a node of kind `JUNCTION`,
- no waiting slot or target staging entry is a Reactive Junction,
- route resolution must terminate,
- for each active vehicle and valid reachable junction state, route resolution is unique-or-none.

### Reactive Junctions

- exactly two states per junction,
- each state ID is unique within that junction,
- enabled segment IDs exist and originate from that junction node,
- every junction has a valid initial state.

### Waiting slots

- at least one waiting slot exists,
- slot indices are unique non-negative integers,
- deterministic priority is ascending index.

### Vehicles

- every vehicle has a match key,
- every vehicle has at least one initial occupancy key,
- initial board vehicle occupancy may not overlap another vehicle or static blocker,
- `requiredForWin` is explicit or defaults to true.

### Passengers

- passenger IDs are unique,
- passenger order is meaningful and preserved,
- every required passenger match key must have enough compatible required vehicle capacity for a solvable authored level.

The final solvability check may be implemented later as a level-validation tool; Phase 2 defines the invariant, not the algorithm.

### Capacity

- vehicle capacity is a positive integer,
- total required passenger count must not exceed total compatible capacity of required vehicles intended to serve them.

---

## 6. Static vs runtime boundary

The level file does **not** contain mutable runtime fields such as:

- selected vehicle,
- current waiting-slot occupancy,
- onboard passenger state,
- served passenger state,
- current turn number,
- current outcome.

Only initial junction state is part of the level because it defines the initial puzzle configuration.

Runtime state is created from the level at session start.


---

## File: docs/02-domain-data/state-transitions.md

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


---

## File: docs/02-domain-data/domain-invariants.md

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


---

## File: docs/02-domain-data/phase-2-handoff.md

# Phase 2 — Domain & Data Handoff

**Project:** Car Party Hobby  
**Status:** Gate 2 candidate for review  
**Primary Role:** System Architect  
**Specific Primary Agent:** ChatGPT: Work  
**Supporting Agents:** ChatGPT: Chat + Gemini: Chat  
**Previous Gate:** ✅ Gate 1 Approved  
**Human Gate:** Gate 2 — Product Owner

---

## 1. Purpose / เป้าหมาย

Translate the approved Phase 1 gameplay requirements into a technology-agnostic game domain model and deterministic state model.

แปลง gameplay requirements ที่ผ่าน Gate 1 แล้วให้เป็น Game Domain Model และ Game State Model ที่ไม่ผูกกับ rendering/UI library

This phase must define **what the game state means and how entities relate**, not how React Native renders them.

---

## 2. Authoritative inputs

Use these repository documents as source of truth:

- `docs/00-discovery/project-brief.md`
- `docs/01-requirements/requirements.md`
- `docs/framework/agent-routing-matrix.md`

Important approved rules include:

- lane/path-based movement
- atomic moves
- full-path validation
- Reactive Junction traversal trigger
- junction toggles after movement completes
- destinations/waiting slots cannot exist on Reactive Junctions
- visible ordered passenger queue
- boarding only from vehicles occupying waiting slots
- lowest-index waiting slot wins boarding ties
- fixed level-wide vehicle capacity for the first vertical slice
- full vehicle departure = immediate despawn/removal in MVP
- deterministic win/deadlock rules
- restart restores exact initial state
- no hidden RNG

---

## 3. Required Phase 2 artifacts

The System Architect should produce at minimum:

1. **Domain Model**
   - Level
   - Vehicle
   - Passenger / PassengerQueue
   - Lane / Path / PathSegment
   - ReactiveJunction
   - WaitingSlot
   - StaticBlocker
   - GameState
   - MovePreview / MoveValidationResult
   - GameOutcome

2. **Entity relationships**
   - Ownership/reference rules
   - IDs and identity boundaries
   - Which data is level definition vs mutable runtime state

3. **State model**
   - Initial state
   - Selected/preview state
   - Move transaction
   - Junction state transition
   - Boarding/departure automatic resolution
   - Win/deadlock outcome

4. **Level schema**
   - Technology-neutral structured representation
   - Validation constraints
   - Enough detail to encode the first vertical-slice level

5. **Domain invariants**
   - No vehicle final position on a Reactive Junction
   - Waiting-slot indices are stable and unique
   - A vehicle occupies at most one location/state category
   - Passenger queue order is stable except front removal
   - Departed vehicles cannot re-enter active state
   - Same input state + move produces same next state

6. **State-transition specification**
   - Must follow the canonical turn-resolution order from Phase 1

---

## 4. Explicit non-goals

Do **not** decide these in Phase 2 unless required to express the domain:

- Skia vs React Native Views
- Reanimated or other animation library
- Zustand/Redux/reducer choice
- Expo routing structure
- file/folder implementation layout
- audio framework
- visual art direction
- persistence library
- production build tooling

Those belong to Phase 3 Architecture or later.

---

## 5. Gate 2 exit criteria

Gate 2 may be considered for approval when:

- Every Phase 1 rule can be represented unambiguously in the domain.
- Static level definition and mutable runtime state are clearly separated.
- Reactive Junction state transitions are representable without UI assumptions.
- Waiting-slot and passenger-queue rules have deterministic representation.
- Win/deadlock can be derived from GameState.
- Restart can reconstruct the exact initial state.
- Level schema can encode the first vertical slice.
- Domain invariants are explicit and testable.
- No unnecessary backend/database ERD is introduced for the offline MVP.

## 6. Phase 2 artifacts completed

- [Domain Model](domain-model.md)
- [Game State Model](game-state-model.md)
- [Level Schema](level-schema.md)
- [State Transition Specification](state-transitions.md)
- [Domain Invariants](domain-invariants.md)

## 7. Gate 2 readiness assessment

| Exit criterion | Status |
|---|---|
| Every Phase 1 rule represented unambiguously | ✅ Pass |
| Static LevelDefinition separated from mutable GameState | ✅ Pass |
| Reactive Junction state transitions represented without UI assumptions | ✅ Pass |
| Waiting-slot and passenger-queue behavior deterministic | ✅ Pass |
| Win/deadlock derived from GameState | ✅ Pass |
| Restart reconstructs exact initial state | ✅ Pass |
| Level schema can encode vertical slice | ✅ Pass |
| Domain invariants explicit and testable | ✅ Pass |
| No unnecessary backend/database ERD introduced | ✅ Pass |

**Gate 2 status:** **CANDIDATE FOR REVIEW / PRODUCT OWNER APPROVAL**

Recommended review focus:

- route graph determinism and unique-or-none constraint,
- static/runtime ownership boundaries,
- passenger partition invariants,
- waiting-slot derivation,
- restart reproducibility,
- whether the Level Schema is sufficient for the vertical slice without leaking rendering concerns.

