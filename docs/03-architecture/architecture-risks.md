# Phase 3 — Architecture Risks

## RISK-01 — Canvas accessibility gap

**Risk:** Skia-rendered board elements are not automatically equivalent to standard native accessible controls.

**Impact:** Casual/all-ages audience may receive insufficient accessibility semantics if all interaction exists only inside the canvas.

**Mitigation:**
- keep HUD and essential status text in React Native components,
- expose accessibility labels/actions through presentation wrappers where feasible,
- do not use color as the only state indicator,
- treat accessibility verification as a Phase 4 UX requirement.

---

## RISK-02 — Expo Go becomes insufficient

**Risk:** Future native capability or production hardening may exceed Expo Go's supported environment.

**Impact:** Late development migration friction.

**Mitigation:**
- use Expo Go only as a prototype convenience,
- keep architecture compatible with Expo development builds,
- move to a development build before production hardening or immediately when unsupported native capability is required.

---

## RISK-03 — Visual state temporarily differs from authoritative state

**Risk:** Architecture commits normalized GameState before the transition animation finishes.

**Impact:** If presentation code incorrectly reads raw next-state geometry instead of transition view models, objects could visually jump.

**Mitigation:**
- create a dedicated immutable TransitionVisualModel from engine transition events,
- lock input during transitions,
- ensure Skia animation reads transition view state while gameplay queries read authoritative GameState,
- cancellation renders directly from committed GameState.

---

## RISK-04 — Level graph validation complexity

**Risk:** Referentially valid JSON can still encode ambiguous or impossible routes.

**Impact:** Runtime content defects or inconsistent move previews.

**Mitigation:**
- structural Zod validation first,
- semantic graph validator second,
- enforce unique-or-none route invariant,
- validate every level in CI,
- fail level loading before session creation.

---

## RISK-05 — Rendering stack complexity

**Risk:** Hybrid Skia + React Native + Reanimated introduces more concepts than a pure Views implementation.

**Impact:** Debugging and developer learning overhead.

**Mitigation:**
- keep Skia scope limited to game-board rendering,
- keep game logic completely outside rendering libraries,
- create small presentation adapters/layers,
- do not use Skia for ordinary HUD/content.

---

## RISK-06 — Premature external state-management dependency

**Risk:** Adding a global state library before the vertical slice needs it could blur domain/application ownership.

**Mitigation:**
- use React reducer/context for MVP,
- define engine/application interfaces independently,
- revisit only against explicit scaling triggers.

---

## RISK-07 — Domain event and animation event coupling

**Risk:** Presentation may begin relying on event ordering as a second source of game truth.

**Impact:** UI behavior can diverge from nextState.

**Mitigation:**
- `nextState` remains authoritative,
- transition events are descriptive,
- event consumers cannot dispatch low-level domain mutations,
- after interruption/reload, render solely from GameState.

---

## RISK-08 — Test suite over-focuses on UI snapshots

**Risk:** Canvas UI snapshots can be brittle while missing actual game-rule defects.

**Mitigation:**
- prioritize pure engine/invariant/state-transition tests,
- use component tests for interaction and accessibility,
- keep Skia visual testing thin,
- use E2E only for a few high-value player journeys.

---

## RISK-09 — JSON/domain type drift

**Risk:** Manually maintained domain interfaces and Zod boundary schemas can diverge.

**Mitigation:**
- colocate validation mapping tests,
- parse test fixtures through Zod then assert domain validator acceptance,
- require all production levels to exercise the same parser,
- treat validator/type mismatch as CI failure.

---

## Gate implication

None of these risks blocks Gate 3 provided the documented boundaries and mitigations are preserved in implementation planning.
