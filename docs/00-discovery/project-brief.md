# Phase 0 — Project Brief

**Project:** Car Party Hobby  
**Status:** Gate 0 candidate for approval  
**Primary Agent:** ChatGPT: Work — Business/System Analyst  
**Supporting Agent:** ChatGPT: Chat — Orchestrator / discussion  
**Product Owner:** Human

---

## 1. Project intent / เป้าหมายโครงการ

Build an original Android-only mobile puzzle game inspired by bus/traffic party puzzle mechanics.

สร้างเกม Puzzle บน Android ที่ได้รับแรงบันดาลใจจากเกมแนว bus / traffic party แต่พัฒนา gameplay presentation, level design, assets, progression และ visual identity ของเราเอง

This project must not directly copy another game's proprietary assets, levels, branding, UI composition, or exact visual identity.

โครงการนี้จะไม่คัดลอก assets, level, branding, UI composition หรือ visual identity ของเกมอื่นโดยตรง

## 2. Initial platform constraints / ข้อกำหนดเบื้องต้น

- Android only
- React Native
- TypeScript
- Expo ecosystem
- Expo Go is acceptable for prototype/MVP development
- Production build strategy is **TBD** during Architecture
- Rendering, state-management, animation, audio, and persistence libraries are **TBD** until the relevant architecture decision is approved

> React Native + Android-only + Expo Go acceptance are current product constraints. Libraries such as Skia, Reanimated, Zustand, or alternatives are not yet architecture-approved.

## 3. Target player and intended experience / กลุ่มผู้เล่นและประสบการณ์ที่ต้องการ

**Target player:** Casual players of all ages.

**กลุ่มเป้าหมาย:** ผู้เล่น Casual ทุกวัย

**Intended session length:** Approximately **2–5 minutes per level/session**.

**ระยะเวลาการเล่นเป้าหมาย:** ประมาณ **2–5 นาทีต่อด่าน/รอบ**

The game should be immediately understandable, mobile-friendly, and suitable for short play sessions while still providing meaningful ordering and planning decisions.

เกมควรเข้าใจได้รวดเร็ว เหมาะกับมือถือ เล่นเป็นช่วงสั้น ๆ ได้ แต่ยังคงมีการตัดสินใจเรื่องลำดับและการวางแผนที่มีความหมาย

## 4. Product hypothesis / สมมติฐานผลิตภัณฑ์

The core experience should be a short-session deterministic puzzle where players inspect a congested board, choose vehicle movement order, manage limited temporary capacity, and resolve matching/boarding constraints.

ประสบการณ์หลักควรเป็น Puzzle เล่นเป็นรอบสั้น ๆ ที่ผู้เล่นต้องอ่านสถานการณ์บนกระดาน เลือกลำดับการเคลื่อนรถ บริหารพื้นที่พักที่มีจำกัด และแก้เงื่อนไขการจับคู่/ขึ้นรถให้สำเร็จ

### Confirmed movement model

The intended movement model is **lane/path-based**, not free movement.

รูปแบบการเคลื่อนที่ที่ยืนยันแล้วคือ **lane/path-based** ไม่ใช่การเคลื่อนที่แบบอิสระ

Exact path rules, blocking rules, intersections, and exit behavior remain Phase 1 gameplay-requirement decisions.

รายละเอียด path, blocking, intersection และ exit rule จะถูกนิยามต่อใน Phase 1

## 5. Signature gameplay hook — Reactive Lanes

The defining gameplay hook is **Reactive Lanes**:

> **A successful vehicle move can change the road configuration for future vehicles.**

Selected junctions on the board behave as **flip junctions / route switches**. When a vehicle passes through a marked junction or completes its configured trigger, that junction changes state. This means each move does more than remove or advance one vehicle: it can open one future route while closing another.

จุดเด่นหลักของเกมคือ **Reactive Lanes**:

> **การเดินรถสำเร็จหนึ่งครั้งสามารถเปลี่ยนโครงสร้างเส้นทางสำหรับรถคันถัดไปได้**

ทางแยกบางจุดจะเป็น **Flip Junction / Route Switch** เมื่อรถผ่านหรือทำ trigger ตามที่กำหนด สถานะของทางแยกจะเปลี่ยน ทำให้การเดินหนึ่งครั้งไม่ได้แค่เคลียร์รถ แต่สามารถเปิดเส้นทางหนึ่งและปิดอีกเส้นทางสำหรับ move ถัดไป

### Why this hook fits the project

- Easy to explain visually: players can see a junction flip.
- Deterministic: no RNG is required.
- Creates planning depth without requiring complex controls.
- Works naturally with lane/path-based movement.
- Makes move order strategically important.
- Can scale gradually across levels.

### MVP form

The first vertical slice should use the smallest useful form:

- At least one reactive junction.
- Two visible junction states.
- A successful move can toggle the junction.
- The player can preview the current route before committing.
- The player can see which junction will change after the selected move.
- No hidden or random junction changes.

More advanced variants such as color-triggered switches, chained switches, timed gates, or multi-switch networks are **not required for V1** and remain future design options.

## 6. Candidate core loop — discovery hypothesis only

The following is a hypothesis to validate and formalize in Phase 1:

1. Player inspects the board, passengers, waiting capacity, and current junction states.
2. Player selects/taps a vehicle.
3. Game previews the vehicle's current lane/path and any blocking condition.
4. Game indicates whether the move will trigger a reactive junction.
5. Player commits the move.
6. Valid vehicles move toward the waiting/boarding area.
7. Matching passengers/units are resolved.
8. Capacity/slot state changes.
9. Any triggered junction updates to its next state.
10. Game checks continue / win / lose conditions.

สมมติฐาน core loop:

1. ผู้เล่นอ่านกระดาน ผู้โดยสาร ช่องพัก และสถานะทางแยก
2. เลือก/แตะรถ
3. ระบบแสดงเส้นทางปัจจุบันและจุดที่ถูกบล็อก
4. ระบบแสดงว่าการเดินนี้จะ trigger Reactive Junction หรือไม่
5. ผู้เล่นยืนยันการเดิน
6. รถเคลื่อนเข้าสู่พื้นที่พัก/รับผู้โดยสาร
7. ระบบจับคู่ผู้โดยสาร/ยูนิต
8. อัปเดต capacity/slot
9. ทางแยกที่ถูก trigger เปลี่ยนสถานะ
10. ตรวจ Continue / Win / Lose

## 7. Visible information model / ข้อมูลที่ผู้เล่นมองเห็น

Car Party Hobby uses a **full-information deterministic puzzle model** for the MVP.

MVP ของ Car Party Hobby ใช้แนวคิด **Full-information deterministic puzzle** คือข้อมูลสำคัญต่อการตัดสินใจต้องมองเห็นได้ และไม่มีผลลัพธ์สุ่มที่ซ่อนอยู่

### Always visible

The player can see:

- Vehicle color/type.
- Vehicle orientation and current lane/path position.
- Passenger groups/colors required by the current level.
- Waiting/parking slots and current occupancy.
- Current state of all reactive junctions.
- Obvious physical blockers on the board.

ผู้เล่นมองเห็นตลอดเวลา:

- สี/ประเภทของรถ
- ทิศทางและตำแหน่งบน lane/path
- กลุ่ม/สีของผู้โดยสารที่เกี่ยวข้อง
- จำนวน waiting/parking slots และช่องที่ถูกใช้งาน
- สถานะปัจจุบันของ Reactive Junction ทุกจุด
- สิ่งกีดขวางที่มีผลต่อเส้นทาง

### Visible on vehicle selection

When a vehicle is tapped/selected, the game should reveal:

- The vehicle's intended route to the next destination/boarding area.
- The first blocking point if the route is unavailable.
- Vehicle capacity when capacity is relevant to the level.
- Which reactive junction(s), if any, will change as a consequence of the move.
- A clear preview of the affected junction's next state.

เมื่อแตะรถ ระบบควรแสดง:

- เส้นทางที่รถจะวิ่งไป
- จุดแรกที่บล็อก หากเส้นทางไปไม่ได้
- ความจุรถ เมื่อ mechanic นี้ถูกใช้ในด่าน
- Reactive Junction ที่จะเปลี่ยนจาก move นี้
- Preview ของสถานะทางแยกหลัง move

### Intentionally not shown

The MVP does **not** reveal:

- The correct move.
- A full solution sequence.
- Automatic strategic recommendations.
- Hidden random outcomes, because V1 should not contain them.

เป้าหมายคือให้ผู้เล่นมีข้อมูลเพียงพอในการคิดเอง โดยเกมไม่เฉลยลำดับคำตอบให้

## 8. MVP discovery target / เป้าหมาย MVP ในระดับ Discovery

The first meaningful milestone should be a **single playable vertical slice** proving both the base puzzle loop and the Reactive Lanes hook before building large quantities of content.

Milestone แรกควรเป็น **Playable Vertical Slice 1 ด่าน** ที่พิสูจน์ทั้ง core puzzle loop และ Reactive Lanes ก่อนลงทุนสร้างด่านจำนวนมากหรือ meta-game

### Confirmed MVP boundary

V1/MVP is confirmed as **offline/local-first only**.

MVP/V1 ยืนยันให้เป็น **offline/local-first**

Included candidate MVP scope:

- One playable level
- Tap/select vehicle
- Lane/path movement validation
- Route preview
- At least one Reactive Junction
- Junction-state preview before move
- Limited waiting/parking capacity
- Matching/boarding resolution
- Win condition
- Lose/deadlock condition
- Restart level
- Local runtime state only

Explicitly out of scope for V1:

- Login/account system
- Cloud backend
- Ads
- IAP
- Leaderboard
- Multiplayer
- Large level catalogue
- LiveOps
- Complex backend
- Production analytics
- Random/hidden junction behavior
- Advanced switch networks

## 9. Design principles / หลักการออกแบบ

1. **Deterministic rules first** — gameplay state must be explainable and testable.
2. **Game engine separated from presentation where practical** — UI must not become the source of gameplay truth.
3. **Data-driven levels** — levels should eventually be representable as structured data.
4. **Fast iteration** — prioritize a playable prototype before polish.
5. **Original identity** — inspiration is acceptable; direct cloning is not the product goal.
6. **Mobile-first interaction** — tap targets, feedback, and readability must suit Android phones.
7. **Short-session clarity** — decisions should be readable enough for casual players within a 2–5 minute session.
8. **Full-information fairness** — important consequences should be previewable before a committed move.
9. **Every move may reshape the puzzle** — Reactive Lanes should make sequence planning meaningful.

ภาษาไทย:

1. กติกาต้อง deterministic และ test ได้
2. แยก game logic ออกจาก presentation เท่าที่เหมาะสม
3. Level ควรขับเคลื่อนด้วย structured data
4. ทำ playable prototype ให้เร็ว ก่อน visual polish
5. เกมต้องมี identity ของเราเอง
6. Interaction ต้องออกแบบสำหรับมือถือ Android เป็นหลัก
7. การตัดสินใจในเกมต้องอ่านสถานการณ์ได้ง่ายพอสำหรับผู้เล่น Casual ในรอบ 2–5 นาที
8. ผลสำคัญของ move ต้อง preview ได้ก่อน commit
9. แต่ละ move สามารถเปลี่ยน puzzle state ผ่าน Reactive Lanes ได้

## 10. Gate 0 decision register / บันทึกการตัดสินใจ Gate 0

| Decision | Status | Product Owner / delegated decision |
|---|---|---|
| Target player | ✅ Confirmed | Casual players of all ages |
| Intended session length | ✅ Confirmed | 2–5 minutes |
| Movement model | ✅ Confirmed | Lane/path-based |
| MVP boundary | ✅ Confirmed | Offline/local-first; no backend/login/ads/IAP/leaderboard/multiplayer in V1 |
| Unique gameplay hook / differentiation | ✅ Proposed under delegated design choice | **Reactive Lanes — successful moves can change future lane/junction states** |
| Visible information before a move | ✅ Proposed under delegated design choice | **Full-information model + route/blocker/junction-change preview on selection** |

## 11. Deferred to Phase 1 / เลื่อนไปกำหนดใน Phase 1

The following details are intentionally deferred to Requirements:

- Exact path/blocking rules
- Exact reactive-junction trigger rules
- Minimum fun loop acceptance criteria
- Formal deadlock definition
- Exact win/lose rule specification
- Vehicle capacity rules
- Passenger boarding order
- Level schema details

## 12. Gate 0 exit criteria / เกณฑ์ผ่าน Gate 0

| Criterion | Status |
|---|---|
| Project objective is clear | ✅ Pass |
| Target player and intended experience are defined | ✅ Pass |
| Inspiration vs original differentiation is documented | ✅ Pass — Reactive Lanes |
| MVP boundary is understood | ✅ Pass |
| Major discovery questions are answered or deliberately deferred | ✅ Pass |
| No implementation architecture has been prematurely locked beyond user-provided constraints | ✅ Pass |

**Gate 0 status:** **CANDIDATE FOR PRODUCT OWNER APPROVAL**

**Next after approval:** Phase 1 — Requirements.
