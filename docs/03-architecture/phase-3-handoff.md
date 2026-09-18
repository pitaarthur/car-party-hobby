# Phase 3 — System Architecture Handoff

**Project:** Car Party Hobby  
**Status:** Gate 3 candidate for review  
**Primary Role:** System Architect  
**Specific Primary Agent:** ChatGPT: Work  
**Independent Reviewer:** Gemini: Chat  
**Previous Gate:** ✅ Gate 2 Approved  
**Human Gate:** Gate 3 — Product Owner

---

## 1. Purpose / เป้าหมาย

Translate the approved deterministic domain model into a concrete application architecture for the Android React Native + TypeScript implementation.

Phase 3 defines **how the system will be structured**, while preserving all Gate 1 gameplay requirements and Gate 2 domain invariants.

---

## 2. Authoritative inputs

Use these repository documents as source of truth:

- `docs/01-requirements/requirements.md`
- `docs/02-domain-data/domain-model.md`
- `docs/02-domain-data/game-state-model.md`
- `docs/02-domain-data/level-schema.md`
- `docs/02-domain-data/state-transitions.md`
- `docs/02-domain-data/domain-invariants.md`

---

## 3. Architecture decisions required

Phase 3 must decide at minimum:

1. **Rendering approach**
   - React Native Views/SVG
   - React Native Skia
   - Hybrid approach
   - Decision must consider Expo Go prototype support and mobile performance.

2. **Game engine boundary**
   - Pure TypeScript deterministic domain engine
   - Rendering/UI must not own gameplay truth
   - Exact interfaces between engine and presentation

3. **State management**
   - Local reducer / Zustand / alternative
   - Must preserve authoritative GameState ownership
   - Derived preview/interaction state must remain separate

4. **Animation architecture**
   - Animation must visualize committed transitions
   - Animation must not independently mutate authoritative GameState

5. **Level data loading and validation**
   - Concrete representation for LevelDefinition
   - Runtime validation strategy
   - Schema-version handling

6. **Testing architecture**
   - Unit tests for pure domain engine
   - State-transition tests
   - Invariant tests
   - Level validation tests
   - UI/integration coverage boundary

7. **Expo / React Native application structure**
   - App entry and screen structure
   - Game module boundaries
   - Asset/data organization
   - Android-only assumptions

8. **Persistence boundary**
   - MVP runtime is offline/local-first
   - Decide whether restart-only vertical slice needs persistence at all
   - Do not introduce backend/cloud requirements

9. **Error/failure handling**
   - Invalid level definition
   - Impossible/ambiguous route
   - Broken domain invariant
   - Recoverable UI errors vs development defects

---

## 4. Required Phase 3 artifacts

At minimum produce:

- `architecture.md` — overall system architecture
- `architecture-decisions.md` — explicit ADR-style decisions and rationale
- `module-boundaries.md` — ownership/dependency rules
- `testing-architecture.md` — test layers and responsibilities
- `runtime-flow.md` — UI → engine → transition → animation flow
- architecture diagram(s) in Mermaid or text form

---

## 5. Non-negotiable constraints inherited from previous gates

- Android only
- React Native + TypeScript
- Expo Go acceptable for prototype/MVP
- Deterministic pure gameplay rules
- Same state + same move = same result
- No hidden RNG
- Atomic move semantics
- GameState is authoritative
- MovePreview is derived/non-authoritative
- Rendering/animation cannot mutate gameplay rules
- Offline/local-first MVP
- No backend/login/ads/IAP/leaderboard/multiplayer in V1
- Reactive Junction behavior must remain deterministic
- Restart must reconstruct the exact initial state

---

## 6. Gate 3 exit criteria

Gate 3 can be approved when:

- Rendering approach is selected and justified.
- Game engine and presentation boundaries are explicit.
- Authoritative state ownership is unambiguous.
- Animation cannot violate domain invariants.
- Concrete LevelDefinition loading/validation strategy exists.
- Testing architecture covers the deterministic engine and invariants.
- Project/module boundaries are clear enough for implementation planning.
- Expo/Android constraints are respected.
- No unnecessary backend architecture is introduced.
- Major technical risks are documented with mitigation.

## 7. Phase 3 artifacts completed

- [System Architecture](architecture.md)
- [Architecture Decisions](architecture-decisions.md)
- [Module Boundaries](module-boundaries.md)
- [Runtime Flow](runtime-flow.md)
- [Testing Architecture](testing-architecture.md)
- [Architecture Risks](architecture-risks.md)

## 8. Gate 3 readiness assessment

| Exit criterion | Status |
|---|---|
| Rendering approach selected and justified | ✅ Pass — Hybrid Skia + RN |
| Engine/presentation boundary explicit | ✅ Pass |
| Authoritative state ownership unambiguous | ✅ Pass |
| Animation cannot own/mutate gameplay state | ✅ Pass |
| Level loading/validation strategy defined | ✅ Pass |
| Testing architecture covers engine/invariants | ✅ Pass |
| Project/module boundaries defined | ✅ Pass |
| Expo/Android constraints respected | ✅ Pass |
| No unnecessary backend architecture | ✅ Pass |
| Major technical risks documented | ✅ Pass |

**Gate 3 status:** **CANDIDATE FOR INDEPENDENT REVIEW / PRODUCT OWNER APPROVAL**

Recommended independent-review focus:

- commit-before-animation correctness,
- reducer/context ownership vs pure engine,
- Hybrid Skia/RN boundary,
- level schema validation boundary,
- dependency direction violations,
- whether any presentation technology can accidentally become gameplay truth.
