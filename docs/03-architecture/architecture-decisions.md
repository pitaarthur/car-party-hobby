# Phase 3 — Architecture Decisions

This file records explicit Phase 3 ADR-style decisions.

---

## ADR-001 — Pure TypeScript game engine

**Status:** Accepted

### Decision

Implement gameplay logic as a pure TypeScript engine with no React/React Native/Expo dependencies.

### Rationale

- preserves Gate 2 determinism,
- enables fast unit tests,
- prevents animation/UI from becoming gameplay truth,
- keeps engine portable and inspectable.

### Consequence

Presentation must call the engine through an application adapter rather than modifying GameState directly.

---

## ADR-002 — Hybrid rendering: Skia board + React Native HUD

**Status:** Accepted

### Decision

Use React Native Skia for the puzzle board and standard React Native components for screens/HUD.

### Rationale

The board requires custom spatial drawing and animation. Standard UI remains more maintainable and accessible with native React Native primitives.

### Expo constraint

Current Expo documentation lists `@shopify/react-native-skia` as included in Expo Go.

Implementation must use `npx expo install` so the package version matches the selected Expo SDK.

---

## ADR-003 — Reanimated for visual transition timing

**Status:** Accepted

### Decision

Use Reanimated for high-frequency animation/shared-value work when coordinating board transitions.

### Constraint

Reanimated is presentation infrastructure only.

It cannot:

- decide a valid move,
- advance passenger state,
- toggle junction state,
- calculate win/deadlock,
- modify GameState.

Current Expo documentation lists Reanimated as included in Expo Go.

---

## ADR-004 — React useReducer + Context for MVP session coordination

**Status:** Accepted

### Decision

Do not introduce Zustand/Redux for the first vertical slice.

Use `useReducer` + Context in the Application layer.

### Rationale

- one active game session,
- one main game screen,
- no backend/shared remote cache,
- deterministic reducer-style flow,
- smaller dependency surface.

### Revisit trigger

Reconsider only when state must be shared across substantial route boundaries, persistence/progression grows, or reducer ergonomics become a measurable maintenance issue.

---

## ADR-005 — Expo Router

**Status:** Accepted

### Decision

Use Expo Router for app navigation.

### Rationale

- first-party Expo integration,
- conventional file-based structure,
- minimal routing needed now,
- leaves clean growth path for future screens.

### MVP routes

- `/` — Home
- `/game` — Puzzle session

---

## ADR-006 — Versioned JSON levels + Zod structural validation

**Status:** Accepted

### Decision

Author level data as JSON-compatible files with `schemaVersion`.

Use Zod to validate structural shape before semantic domain validation.

### Important boundary

The Engine and Domain layers do not import Zod.

### Validation stages

1. structural parse,
2. reference checks,
3. domain-invariant checks,
4. graph/routing checks,
5. accepted `LevelDefinition`.

---

## ADR-007 — No persistence dependency for the first vertical slice

**Status:** Accepted

### Decision

Do not install AsyncStorage/SQLite solely for MVP gameplay.

### Rationale

No approved requirement needs persisted game progress.

### Future extension

Introduce a `ProgressRepository` or `SettingsRepository` adapter later if product requirements justify persistence.

---

## ADR-008 — Commit domain state before visual animation

**Status:** Accepted

### Decision

A successful engine command returns the complete normalized `nextState` and descriptive `transitionEvents`.

Application commits `nextState` immediately, locks input, then presentation animates the event sequence.

### Rationale

Visual timing cannot create partial gameplay state.

### Constraint

Animation completion may clear presentation transition state and unlock input, but cannot alter domain results.

---

## ADR-009 — Expo Go for prototype, development builds for production-oriented work

**Status:** Accepted

### Decision

Expo Go remains acceptable during early vertical-slice development because the selected Skia/Reanimated/Router dependencies are currently available there.

Do not treat Expo Go as the production runtime contract.

Move to Expo development builds before production hardening or whenever a required native capability exceeds Expo Go.

---

## ADR-010 — Jest-based test stack

**Status:** Accepted

### Decision

Use Jest for engine/application tests and `jest-expo` for Expo/React Native test integration.

Use React Native Testing Library for component-level behavior tests.

E2E/device automation is deferred until the vertical slice is stable.

### Rationale

Expo currently documents `jest-expo` as the supported baseline for Expo unit testing.
