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
