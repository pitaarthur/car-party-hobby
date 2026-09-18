# Phase 1 — Requirements

**Project:** Car Party Hobby  
**Status:** In progress — Gate 1 open  
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

## 3. Functional requirements

### FR-001 — Board inspection
The player must be able to inspect the complete current puzzle state before committing a move.

ผู้เล่นต้องสามารถมองเห็นสถานะ puzzle ปัจจุบันที่จำเป็นต่อการตัดสินใจก่อน commit move

### FR-002 — Vehicle selection
The player must be able to tap/select an eligible vehicle.

ผู้เล่นต้องสามารถแตะ/เลือก vehicle ที่อยู่ในด่านได้

### FR-003 — Route preview
Selecting a vehicle must show its intended current path to the next destination or staging area.

เมื่อเลือก vehicle ระบบต้องแสดง path ปัจจุบันที่จะใช้ไปยัง destination/staging area

### FR-004 — Blocking feedback
If the vehicle cannot move, the game must indicate the first blocking point or blocking reason.

หากรถเคลื่อนไม่ได้ ระบบต้องแสดงจุดหรือเหตุผลแรกที่ทำให้ถูก block

### FR-005 — Move commit
A valid move must be committed only after the player selects a vehicle and confirms the intended move through the defined interaction pattern.

### FR-006 — Lane/path validation
The game must validate the full required path segment before movement is resolved.

### FR-007 — Reactive Junction state
At least one junction in the vertical slice must have two deterministic states.

### FR-008 — Junction change preview
Before committing a move, the player must be able to see whether that move will change a Reactive Junction and what its next state will be.

### FR-009 — Junction trigger resolution
After a successful move that triggers a Reactive Junction, the junction must change state deterministically.

### FR-010 — Waiting/parking capacity
The level must include a limited staging/waiting capacity that can constrain future moves.

### FR-011 — Passenger matching
Vehicles and passenger groups must resolve using explicit matching rules defined in this phase.

### FR-012 — Vehicle capacity
Vehicle capacity must be represented only if the level uses it as a meaningful rule; otherwise the vertical slice may use a fixed/simple capacity model.

### FR-013 — Win detection
The game must detect when the level objective has been fully completed.

### FR-014 — Lose/deadlock detection
The game must detect an unrecoverable state according to the formal deadlock definition.

### FR-015 — Restart
The player must be able to restart the level to its initial deterministic state.

### FR-016 — Local-only runtime
The MVP must function without account login or network/backend dependencies.

## 4. Gameplay rules requiring closure in Gate 1

### GR-01 — Path occupancy
Define whether a vehicle blocks only its occupied segment or also reserves the next segment during resolution.

### GR-02 — Intersection priority
Define how two potential routes sharing an intersection are evaluated.

### GR-03 — Move atomicity
Define whether a move resolves atomically from source to destination or can stop mid-path.

### GR-04 — Reactive Junction trigger
Define the exact trigger: entering, crossing, exiting, or completing a move through a junction.

### GR-05 — Junction timing
Define whether the junction flips immediately when crossed or only after the vehicle reaches its destination.

### GR-06 — Passenger boarding order
Define whether boarding is automatic by visible queue order, color group, or another deterministic rule.

### GR-07 — Waiting slot behavior
Define when a vehicle occupies or leaves a waiting slot.

### GR-08 — Deadlock
Define the exact state that makes the level unrecoverable.

### GR-09 — Win condition
Define whether the level ends when all passengers are served, all vehicles are cleared, or both.

## 5. Non-functional requirements

### NFR-001 — Responsiveness
Tap feedback and route preview should feel immediate on typical Android phones targeted by the project.

### NFR-002 — Determinism
The same initial level state and same move sequence must produce the same result.

### NFR-003 — Explainability
A player should be able to understand why a move is valid or blocked from visible game feedback.

### NFR-004 — Short-session design
The first level should be completable within the intended 2–5 minute session window after the player understands the controls.

### NFR-005 — Offline operation
The vertical slice must remain playable with no network connection.

### NFR-006 — Testability
Core gameplay rules must be definable independently enough to support automated logic tests in later phases.

## 6. MVP acceptance criteria — draft

The Phase 1 vertical-slice requirements will be considered complete when the approved specification can support a level where:

1. A player can inspect the full relevant board state.
2. At least two vehicles create a meaningful ordering decision.
3. At least one move is blocked or constrained by path occupancy.
4. At least one Reactive Junction changes a future route.
5. The junction change is previewable before commitment.
6. Waiting/parking capacity affects at least one decision.
7. Passenger matching resolves deterministically.
8. The level has a testable win state.
9. The level has a testable deadlock/lose state.
10. Restart restores the exact initial state.
11. The complete experience works offline.
12. No hidden RNG is required.

## 7. Gate 1 decision register

| Decision | Status |
|---|---|
| Path occupancy rule | ❌ Open |
| Intersection priority | ❌ Open |
| Move atomicity | ❌ Open |
| Reactive Junction trigger | ❌ Open |
| Junction timing | ❌ Open |
| Boarding order | ❌ Open |
| Waiting slot behavior | ❌ Open |
| Deadlock definition | ❌ Open |
| Win condition | ❌ Open |

## 8. Gate 1 exit criteria

Gate 1 can be approved when:

- Functional requirements are complete enough for domain modeling.
- All core gameplay rules above are closed or explicitly deferred.
- Win and deadlock states are unambiguous.
- Reactive Lanes behavior is deterministic.
- Vertical-slice acceptance criteria are testable.
- No architecture/library choices are introduced unless they are actual product constraints.

**Gate 1 status:** OPEN

**Next after approval:** Phase 2 — Domain & Data.
