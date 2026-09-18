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


---

## 8. Independent Review / ผลการ Review

**Reviewer:** Gemini: Chat  
**Result:** ✅ **PASS WITH MINOR FIXES / SAFE TO HAND OFF**  
**Reported by:** Product Owner

Reviewer conclusion:

> The domain model is safe to hand off to Phase 3 Architecture. The static-schema / mutable-state separation and passenger queue partitioning provide a stable, deterministic foundation.

The reviewer noted only minor implementation-level details and no structural domain flaws. Those details are non-blocking for Gate 2 and may be resolved during Phase 3 Architecture / Phase 5 Implementation Planning as appropriate.

### Gate 2 final result

| Item | Status |
|---|---|
| Structural domain correctness | ✅ Pass |
| Phase 1 requirement coverage | ✅ Pass |
| Static/runtime separation | ✅ Pass |
| Determinism | ✅ Pass |
| Safe for Architecture handoff | ✅ Yes |
| Blocking findings | ✅ None |

**Gate 2 status:** ✅ **APPROVED**

**Next phase:** Phase 3 — System Architecture.
