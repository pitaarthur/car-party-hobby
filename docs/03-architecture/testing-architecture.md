# Phase 3 — Testing Architecture

## 1. Testing objective

The highest-value tests protect deterministic domain behavior rather than visual implementation detail.

Test pyramid:

```text
           Device / E2E
          ─────────────
        UI integration tests
       ───────────────────
     Application/session tests
    ─────────────────────────
  Pure engine + invariant tests
 ───────────────────────────────
       Level validation tests
```

The widest layers are pure and fast.

---

## 2. Test tooling

### Selected baseline

- Jest
- `jest-expo` for Expo project integration
- React Native Testing Library for component interaction tests

Device/E2E automation is deferred until the vertical slice is stable.

---

## 3. Level validation tests

Test:

- invalid references,
- duplicate IDs,
- duplicate waiting-slot indices,
- invalid initial junction state,
- junction with != 2 states,
- staging target on junction,
- overlapping initial occupancy,
- unknown enabled segment,
- cyclic/non-terminating invalid authored route,
- multiple valid route ambiguity,
- invalid capacity.

Every authored level must pass validation in CI.

---

## 4. Pure engine unit tests

Core targets:

- `createInitialState()`
- `resolveRoute()`
- `deriveMovePreview()`
- `validateMove()`
- `commitMove()`
- automatic boarding
- departure
- legal-move derivation
- outcome derivation.

These tests do not render React components.

---

## 5. Invariant tests

Every Phase 2 domain invariant should map to at least one automated test or validation assertion.

Priority invariants:

- same state + same move = same next state,
- vehicle exclusive location,
- passenger partition exclusivity,
- front-only queue removal,
- waiting-slot uniqueness,
- lowest-index boarding tie-break,
- no stop on Reactive Junction,
- departed is terminal,
- capacity bound,
- preview purity,
- win before deadlock,
- restart reproducibility.

---

## 6. Canonical transition scenario tests

Use table-driven state fixtures.

Example:

```text
Given:
  junction = A
  red vehicle route crosses junction
  wait-0 free
  front passengers = red, red

When:
  red vehicle commits

Then:
  red vehicle enters wait-0
  junction = B
  two red passengers board
  vehicle departs
  passengers become served
  wait-0 becomes free
  outcome is derived
```

The same fixture must always produce the same result.

---

## 7. Application-layer tests

Test orchestration rather than domain rules:

- first tap selects,
- second tap confirms,
- switching selection changes preview only,
- invalid confirm does not change turn,
- valid commit stores returned nextState,
- valid commit locks input during transition,
- animation completion unlocks input,
- terminal outcome keeps gameplay input disabled,
- restart cancels transition and restores initial state.

Mock the animation completion boundary; do not test animation physics here.

---

## 8. Presentation/component tests

Use React Native Testing Library for:

- visible queue content,
- restart button behavior,
- win/deadlock overlays,
- blocker feedback,
- accessibility labels,
- selected/invalid status where represented by standard components.

Avoid snapshot-heavy testing of Skia board internals.

Board correctness should come primarily from engine/view-model tests.

---

## 9. Animation tests

Do not assert frame-by-frame visual positions as core correctness.

Test contracts:

- transition events map to a visual plan,
- input remains locked during visual transition,
- completion callback fires once,
- cancellation does not modify GameState,
- re-render from committed GameState recovers from interruption.

---

## 10. E2E boundary

After vertical slice stability, add Android device E2E coverage for:

1. open game,
2. select vehicle,
3. confirm a known valid move,
4. observe board update,
5. reach win,
6. restart,
7. reproduce known deadlock path.

The architecture does not mandate the final E2E tool in Gate 3.

---

## 11. CI quality gates proposed for Phase 6+

Before merge of gameplay changes:

```text
typecheck
level validation
engine tests
invariant tests
application tests
selected UI integration tests
```

E2E can initially run on release/UAT workflows rather than every local commit.
