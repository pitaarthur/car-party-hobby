# Phase 3 — Runtime Flow

## 1. Session startup

```mermaid
sequenceDiagram
    participant Route as Expo Router
    participant Levels as Level Loader
    participant App as GameSessionProvider
    participant Engine as Pure Engine
    participant UI as Presentation

    Route->>Levels: load(levelId)
    Levels->>Levels: Zod structural validation
    Levels->>Levels: semantic/domain validation
    Levels-->>Route: Validated LevelDefinition
    Route->>App: create session(level)
    App->>Engine: createInitialState(level)
    Engine-->>App: GameState
    App-->>UI: render session
```

Invalid levels never create an active game session.

---

## 2. Vehicle selection

```mermaid
sequenceDiagram
    participant User
    participant UI as Skia/RN UI
    participant App as Application
    participant Engine

    User->>UI: tap vehicle
    UI->>App: selectVehicle(id)
    App->>Engine: deriveMovePreview(level,state,id)
    Engine-->>App: MovePreview
    App-->>UI: selection + preview
```

GameState is unchanged.

---

## 3. Confirmed valid move

```mermaid
sequenceDiagram
    participant User
    participant UI
    participant App as Application
    participant Engine
    participant Anim as Animator

    User->>UI: tap selected vehicle again
    UI->>App: confirmSelectedVehicle()
    App->>Engine: commitMove(level,state,id)
    Engine-->>App: nextState + transitionEvents
    App->>App: commit nextState
    App->>App: lock input
    App-->>UI: render committed domain state
    App->>Anim: play transitionEvents
    Anim-->>UI: visual interpolation
    Anim-->>App: animation complete
    App->>App: clear transition + unlock if IN_PROGRESS
```

The engine does not wait for animation.

---

## 4. Invalid move

```text
Tap selected vehicle
   │
   ▼
validate / preview says invalid
   │
   ├── GameState unchanged
   ├── turnNumber unchanged
   ├── no junction toggle
   ├── no boarding
   └── presentation shows blocker/reason
```

---

## 5. Automatic resolution

The engine owns the complete automatic phase inside `commitMove`:

```text
board vehicle arrives
    │
    ▼
junction toggles
    │
    ▼
waiting-slot assigned
    │
    ▼
front passenger matching loop
    │
    ├── board
    ├── vehicle becomes full
    └── departure/despawn
    │
    ▼
repeat until fixed point
    │
    ▼
win check
    │
    ▼
legal moves / deadlock check
```

Presentation receives descriptive events after the engine already knows the normalized result.

---

## 6. Restart flow

```mermaid
sequenceDiagram
    participant User
    participant App as Application
    participant Engine
    participant UI

    User->>App: restart()
    App->>Engine: createInitialState(level)
    Engine-->>App: exact initial GameState
    App->>App: clear selection + transition state
    App-->>UI: render initial puzzle
```

Restart does not replay history backward.

---

## 7. Animation interruption

If app lifecycle or rendering interrupts an animation:

- authoritative GameState remains the already committed normalized state,
- visual transition may be cancelled,
- presentation re-renders directly from current GameState,
- no engine command is replayed automatically.

This guarantees visual failure cannot duplicate a move.

---

## 8. Input lock

Input is locked while a committed transition is being visualized.

Allowed during input lock:

- application lifecycle handling,
- rendering,
- animation cancellation/recovery.

Not allowed:

- selecting another vehicle,
- confirming another move,
- mutating GameState from presentation.

Restart behavior during an active animation is an Application-layer policy; MVP should cancel the visual transition and reconstruct initial GameState atomically.
