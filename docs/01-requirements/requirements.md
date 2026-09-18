# Phase 1 — Requirements

**Project:** Car Party Hobby  
**Status:** ✅ Gate 1 Approved  
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

---

## 3. Functional requirements

### FR-001 — Board inspection

The player must be able to inspect the complete current puzzle state required for a meaningful decision before committing a move.

ผู้เล่นต้องสามารถมองเห็นสถานะ puzzle ปัจจุบันที่จำเป็นต่อการตัดสินใจก่อน commit move

### FR-002 — Vehicle selection

The player must be able to tap/select a vehicle that remains active on the board.

ผู้เล่นต้องสามารถแตะ/เลือก vehicle ที่ยัง active อยู่บนกระดานได้

### FR-003 — Route preview

Selecting a vehicle must show its intended current path from its current position to its next destination/staging point.

เมื่อเลือก vehicle ระบบต้องแสดง path ปัจจุบันจากตำแหน่งรถไปยัง destination/staging point ถัดไป

### FR-004 — Blocking feedback

If the vehicle cannot move, the game must indicate the first blocking point or blocking reason.

หากรถเคลื่อนไม่ได้ ระบบต้องแสดงจุดแรกหรือเหตุผลแรกที่ทำให้ถูก block

### FR-005 — Move commit interaction

The MVP uses a two-step tap interaction:

1. First tap selects a vehicle and shows its preview.
2. Tapping the same selected vehicle again commits the move if valid.
3. Tapping another vehicle changes selection and preview.
4. An invalid move must not mutate gameplay state.

MVP ใช้ interaction แบบสองขั้น: แตะครั้งแรกเพื่อเลือกและ preview และแตะรถคันเดิมอีกครั้งเพื่อ commit move หาก valid

### FR-006 — Lane/path validation

Before a move can be committed, the game must validate the complete required path against:

- current vehicle occupancy,
- static blockers,
- current Reactive Junction state,
- destination/staging availability.

ก่อน commit ระบบต้องตรวจ path ทั้งเส้นที่จำเป็นกับ occupancy, blocker, junction state และ destination availability

### FR-007 — Reactive Junction state

At least one junction in the vertical slice must have exactly two deterministic states.

Reactive Junction ใน vertical slice อย่างน้อย 1 จุดต้องมี 2 state ที่ deterministic

### FR-008 — Junction change preview

Before committing a move, the player must be able to see whether that move will trigger a Reactive Junction and what its next state will be.

### FR-009 — Junction trigger resolution

A committed route that traverses a configured Reactive Junction must toggle that junction exactly once after the vehicle completes its movement.

รถที่ traverse Reactive Junction ตาม route ที่ commit แล้วจะ trigger junction หนึ่งครั้งหลัง movement เสร็จ

### FR-010 — Waiting/parking capacity

The level must include a finite number of staging/waiting slots. A board vehicle that requires staging may move only if it can legally reach an available slot.

### FR-011 — Passenger queue and matching

Passengers are represented as a fully visible ordered queue.

A vehicle may automatically board passengers only from the **front of the queue**, and only while the front passenger color/type matches that vehicle.

Passenger เป็น queue ที่มองเห็นลำดับทั้งหมด และรับขึ้นรถจากหัวคิวเท่านั้นเมื่อสี/ประเภทตรงกับรถ

### FR-012 — Vehicle capacity

For the first vertical slice, all passenger-carrying vehicles use a **fixed capacity defined by the level**.

The capacity value is visible when relevant. Per-vehicle variable capacities are deferred beyond the first vertical slice.

Vertical slice แรกใช้ capacity แบบค่าคงที่ระดับ level เพื่อลด complexity ส่วน capacity ต่างกันรายคันเลื่อนไปหลัง MVP

### FR-013 — Automatic boarding resolution

After every successful vehicle arrival and after every queue change, the game must automatically resolve all immediately available deterministic boarding events before accepting the next player move.

### FR-014 — Waiting vehicle departure

A waiting vehicle departs and frees its slot when it reaches its required capacity.

If it has not reached capacity, it remains in its slot and may automatically board additional matching passengers later when they reach the front of the queue.

### FR-015 — Win detection

The game must detect the win state when:

1. all required passengers have boarded and departed, **and**
2. all required active vehicles for the level have cleared the board/waiting area.

### FR-016 — Lose/deadlock detection

After automatic resolution is exhausted, the game must detect deadlock when:

1. the level is not already won, **and**
2. there is no legal player move that would mutate gameplay state.

A legal move is one that can complete its required path and legally resolve its destination/staging requirement under the current state.

### FR-017 — Restart

The player must be able to restart the level to its exact initial deterministic state, including:

- vehicle positions,
- passenger queue,
- waiting slots,
- Reactive Junction states,
- completion state.

### FR-018 — Local-only runtime

The MVP must function without account login, cloud services, or network/backend dependencies.

---

## 4. Approved gameplay rules

### GR-01 — Path occupancy

**Decision: occupied-footprint blocking + full-path validation.**

Idle vehicles block only the lane/path footprint they currently occupy.

Before a selected vehicle commits, the game validates its complete required route. Because MVP movement is sequential and atomic, there is no persistent "reserved next segment" state between player turns.

รถที่ยังไม่เคลื่อน block เฉพาะพื้นที่ที่ครอบครองจริง ระบบตรวจเส้นทางทั้งหมดก่อน commit และไม่มีการ reserve segment ข้าม turn

**Reasoning:** keeps the state deterministic without introducing traffic-simulation concurrency.

---

### GR-02 — Intersection priority

**Decision: no simultaneous right-of-way system in MVP.**

Only the currently selected vehicle is evaluated for movement. An intersection is traversable when:

- the selected route permits it,
- its current Reactive Junction state permits it,
- no blocking vehicle/static obstacle occupies the required path.

There is no traffic-light timing, speed comparison, or simultaneous vehicle priority in the first vertical slice.

**Reasoning:** the puzzle is about sequence selection, not real-time traffic arbitration.

---

### GR-03 — Move atomicity

**Decision: moves are atomic.**

A committed vehicle moves from its current location to its resolved next destination/staging point as one gameplay transaction.

A vehicle cannot be intentionally stopped mid-path by the player.

Animation may visually show travel through path segments, but the underlying rule resolves one atomic move.

**Reasoning:** visual animation must not create intermediate gameplay states that change the puzzle rules.

---

### GR-04 — Reactive Junction trigger

**Decision: traversal trigger.**

A Reactive Junction triggers when the committed route **traverses** that junction.

For the MVP:

- one route traversal causes at most one toggle of that junction,
- merely selecting/previewing a route does not trigger it,
- an invalid/blocked move does not trigger it.

---

### GR-05 — Junction timing

**Decision: toggle after vehicle movement completes.**

The junction state used to calculate the current vehicle's route remains fixed for that move.

After the vehicle reaches its destination/staging point, each triggered Reactive Junction toggles before the next player decision.

**Reasoning:** the current vehicle cannot reroute itself halfway through the move; Reactive Lanes affect future moves.

---

### GR-06 — Passenger boarding order

**Decision: visible front-of-queue automatic boarding.**

Passengers form an ordered queue visible to the player.

For each boarding resolution:

1. inspect the front passenger,
2. if a waiting/arriving vehicle of the matching color/type is eligible, board that passenger,
3. repeat while the next front passenger can board deterministically,
4. stop when the front passenger has no eligible vehicle.

If multiple waiting vehicles are equally eligible for the same front passenger, the vehicle occupying the **earliest waiting-slot index** receives boarding priority.

**Reasoning:** this provides a deterministic tie-breaker and makes queue order strategically meaningful.

---

### GR-07 — Waiting slot behavior

**Decision: first-available slot + stay-until-full.**

- A vehicle that reaches staging occupies the lowest-index available waiting slot.
- Boarding resolves immediately after arrival.
- A vehicle remains in that slot until its fixed capacity is reached.
- When full, it departs automatically and frees the slot.
- Slot ordering remains stable; remaining vehicles do not need to compact/reorder in the MVP.

**Reasoning:** slot state is visible, stable, and easy to predict.

---

### GR-08 — Deadlock definition

**Decision: no legal state-changing move after automatic resolution.**

The game first resolves all automatic boarding/departure events.

Then:

```
deadlock =
  !win &&
  legalPlayerMoves.length === 0
```

A legal player move must:

- have a valid complete path,
- satisfy current junction/path constraints,
- have a valid destination or available waiting slot,
- cause an actual state transition.

This definition intentionally avoids solver-based look-ahead in the MVP.

**Example deadlock:** all required staging slots are occupied, the front passenger matches none of the waiting vehicles, and every remaining board vehicle is blocked or has no legal destination.

---

### GR-09 — Win condition

**Decision: passengers served + required vehicles cleared.**

The level is won only when:

1. there are no unserved required passengers remaining,
2. no required vehicle remains active on the board,
3. no required vehicle remains in a waiting slot.

This prevents a "win" while unresolved vehicles still occupy the gameplay state.

---

## 5. Canonical turn-resolution order

Every committed move follows this exact logical sequence:

1. **Select** vehicle.
2. **Preview** route, blocker status, destination, and predicted Reactive Junction changes.
3. **Validate** the complete move.
4. **Commit** the move.
5. **Move** vehicle atomically to its destination/staging point.
6. **Toggle** any Reactive Junction traversed by the committed route.
7. **Assign waiting slot** if required.
8. **Resolve automatic passenger boarding** from the visible queue.
9. **Depart full vehicles** and free their slots.
10. Repeat automatic boarding/departure until no further automatic event is possible.
11. **Evaluate win**.
12. If not won, generate legal player moves.
13. If zero legal moves remain, **evaluate deadlock/lose**.
14. Otherwise return control to the player.

ภาษาไทยโดยสรุป:

**Preview → Validate → Commit → Move → Toggle Junction → Waiting Slot → Boarding → Departure → Win/Deadlock → Next Turn**

This order is normative for Phase 2 domain modeling and later automated tests.

---

## 6. Non-functional requirements

### NFR-001 — Responsiveness

Selection feedback and route preview should appear without perceptible delay on typical Android phones targeted by the project.

### NFR-002 — Determinism

The same initial level state and same committed move sequence must produce the same gameplay result.

### NFR-003 — Explainability

A player must be able to understand why a move is valid or blocked using visible feedback.

### NFR-004 — Short-session design

The first level should be completable within the intended 2–5 minute session window after the player understands the controls.

### NFR-005 — Offline operation

The vertical slice must remain fully playable with no network connection.

### NFR-006 — Testability

Core rules must be representable independently of rendering so later phases can execute automated state-transition tests.

### NFR-007 — No hidden RNG

The vertical slice must not use hidden random values that alter route validity, passenger order, junction behavior, win state, or deadlock state.

### NFR-008 — State integrity

A visual animation must not independently mutate authoritative gameplay state outside the approved turn-resolution rules.

---

## 7. Vertical-slice acceptance criteria

The Phase 1 specification must support one level satisfying all of the following:

1. The player can inspect all information required to make a deterministic decision.
2. At least two vehicles create a meaningful ordering decision.
3. Selecting a vehicle previews its complete current route.
4. A blocked vehicle clearly identifies its first blocker/reason.
5. A valid move uses the two-step select → commit interaction.
6. At least one move is constrained by path occupancy.
7. At least one Reactive Junction has two visible states.
8. At least one successful move traverses and toggles that junction.
9. The junction's predicted next state is visible before commit.
10. The toggled junction changes the legal route or move availability of a later vehicle.
11. Waiting capacity is finite and affects at least one decision.
12. Passengers are shown as a visible ordered queue.
13. Boarding occurs only from the front of that queue using deterministic color/type matching.
14. At least one vehicle must wait because it cannot yet become full.
15. A full waiting vehicle departs automatically and frees its slot.
16. The level has a reproducible win state.
17. The level has a reproducible deadlock/lose state.
18. Restart restores the exact initial state.
19. Replaying the same move sequence produces the same result.
20. The complete experience works offline.
21. No hidden RNG is required.
22. A typical successful playthrough can fit the intended 2–5 minute session target.

---

## 8. Gate 1 decision register

| Decision | Status | Approved rule |
|---|---|---|
| Path occupancy rule | ✅ Closed | Idle vehicles block occupied footprint; full path validated before atomic move |
| Intersection priority | ✅ Closed | No simultaneous priority; selected vehicle evaluated against current state |
| Move atomicity | ✅ Closed | Atomic source → destination/staging move |
| Reactive Junction trigger | ✅ Closed | Toggle on successful traversal |
| Junction timing | ✅ Closed | Toggle after movement completes, before next player decision |
| Boarding order | ✅ Closed | Visible queue; front-only deterministic matching; earliest waiting slot wins ties |
| Waiting slot behavior | ✅ Closed | Lowest-index free slot; vehicle stays until full; full vehicle departs |
| Deadlock definition | ✅ Closed | Not won + no legal player move after automatic resolution |
| Win condition | ✅ Closed | All required passengers served + all required vehicles cleared |

---

## 9. Explicitly deferred beyond Phase 1

The following are intentionally **not** required to pass Gate 1:

- exact numeric board dimensions,
- exact number of passenger colors,
- exact fixed vehicle capacity value,
- data/schema representation of lanes and junctions,
- rendering library,
- animation library,
- state-management library,
- audio behavior,
- art direction,
- advanced Reactive Junction variants,
- variable vehicle capacities,
- chained/timed/random junctions,
- level progression/meta-game.

These belong to Phase 2–4 or later.

---

## 10. Gate 1 exit criteria

| Criterion | Status |
|---|---|
| Functional requirements complete enough for domain modeling | ✅ Pass |
| Core gameplay rules closed or explicitly deferred | ✅ Pass |
| Win state unambiguous | ✅ Pass |
| Deadlock state unambiguous | ✅ Pass |
| Reactive Lanes behavior deterministic | ✅ Pass |
| Turn-resolution order defined | ✅ Pass |
| Vertical-slice acceptance criteria testable | ✅ Pass |
| Architecture/library choices avoided | ✅ Pass |

## 11. Independent review / ผลการ Review

**Reviewer:** Gemini: Chat  
**Result:** ✅ PASS  
**Reported by:** Product Owner

No blocking defect was reported. The requirements are accepted for handoff to Phase 2 — Domain & Data.

ไม่มี blocking defect จาก Independent Review และ requirement ชุดนี้ได้รับการยอมรับให้ส่งต่อไป Phase 2 — Domain & Data

---

**Gate 1 status:** ✅ **APPROVED**

**Next after approval:** Phase 2 — Domain & Data.
