# Phase 3 — Module Boundaries

## 1. Proposed source structure

```text
src/
├── app/
│   ├── _layout.tsx
│   ├── index.tsx
│   └── game.tsx
│
├── game/
│   ├── domain/
│   │   ├── types.ts
│   │   ├── invariants.ts
│   │   └── errors.ts
│   │
│   ├── engine/
│   │   ├── createInitialState.ts
│   │   ├── routeResolver.ts
│   │   ├── movePreview.ts
│   │   ├── moveValidator.ts
│   │   ├── commitMove.ts
│   │   ├── autoResolution.ts
│   │   ├── outcome.ts
│   │   └── legalMoves.ts
│   │
│   ├── application/
│   │   ├── GameSessionProvider.tsx
│   │   ├── gameSessionReducer.ts
│   │   ├── commands.ts
│   │   └── selectors.ts
│   │
│   └── presentation/
│       ├── GameScreen.tsx
│       ├── board/
│       │   ├── GameBoard.tsx
│       │   ├── BoardCanvas.tsx
│       │   ├── RoutePreviewLayer.tsx
│       │   ├── VehicleLayer.tsx
│       │   └── JunctionLayer.tsx
│       ├── hud/
│       │   ├── PassengerQueue.tsx
│       │   ├── WaitingSlots.tsx
│       │   └── GameControls.tsx
│       └── animation/
│           ├── TransitionAnimator.ts
│           └── transitionVisualModel.ts
│
├── levels/
│   ├── data/
│   │   └── level-001.json
│   ├── schema/
│   │   └── levelDefinitionSchema.ts
│   ├── validateLevel.ts
│   └── levelRegistry.ts
│
└── shared/
    └── ui/
```

Exact filenames may change during Phase 5 planning, but dependency boundaries are normative.

---

## 2. Dependency rules

Allowed:

```text
app -> presentation
presentation -> application
application -> engine
engine -> domain

levels -> domain
levels -> schema validator

application -> validated levels
presentation -> transition events / selectors
```

Forbidden:

```text
domain -X-> React / RN / Expo / Skia / Reanimated
engine -X-> React / RN / Expo / Skia / Reanimated
engine -X-> application
engine -X-> presentation
presentation -X-> direct GameState mutation
animation -X-> domain mutation
level JSON -X-> runtime mutable state
```

---

## 3. Domain ownership

`game/domain` owns:

- domain type definitions,
- ID semantics,
- GameState shape,
- LevelDefinition shape,
- invariant terminology,
- domain errors.

It does not own algorithms that transform state.

---

## 4. Engine ownership

`game/engine` owns deterministic algorithms:

- initial-state construction,
- route resolution,
- path validation,
- move validation,
- move commit,
- junction toggling,
- waiting-slot assignment,
- passenger auto-resolution,
- vehicle departure,
- win/deadlock derivation,
- legal-move derivation.

Every engine API must be testable without React Native.

---

## 5. Application ownership

`game/application` owns session orchestration:

- current authoritative GameState,
- interaction selection,
- input lock,
- transition lifecycle,
- command dispatch,
- restart orchestration,
- presentation selectors.

It does not redefine gameplay algorithms.

---

## 6. Presentation ownership

`game/presentation` owns:

- board drawing,
- HUD,
- touch interpretation,
- previews/highlights,
- animation,
- visual error feedback,
- win/deadlock presentation.

It sends intent upward:

```text
vehicle tapped
vehicle confirmed
restart requested
```

It does not emit low-level mutations such as:

```text
setVehiclePosition
toggleJunction
removePassenger
setOutcome
```

---

## 7. Level module ownership

`levels` owns:

- raw authored data,
- structural schema,
- schema-version handling,
- semantic validation entry point,
- level registry.

It returns a validated `LevelDefinition`.

The engine never consumes raw unknown JSON.

---

## 8. Cross-boundary contracts

### Presentation → Application

Commands:

- `selectVehicle(vehicleId)`
- `confirmSelectedVehicle()`
- `restart()`

### Application → Engine

Queries/commands:

- `deriveMovePreview`
- `commitMove`
- `createInitialState`

### Engine → Application

Values:

- `MovePreview`
- `MoveValidationResult`
- `CommitResult`
- `GameState`
- `TransitionEvent[]`

### Application → Presentation

Selectors/view models:

- board vehicles,
- passenger queue,
- waiting slots,
- current junction states,
- selected vehicle,
- move preview,
- transition plan,
- outcome,
- input locked.
