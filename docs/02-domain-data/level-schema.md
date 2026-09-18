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
