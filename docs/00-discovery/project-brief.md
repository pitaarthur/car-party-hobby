# Phase 0 — Project Brief

**Project:** Car Party Hobby  
**Status:** Draft — Gate 0 open (2 decisions remaining)  
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

## 5. Candidate core loop — discovery hypothesis only

The following is a hypothesis to validate in Phase 1, not an approved gameplay specification:

1. Player inspects the board.
2. Player selects/taps a vehicle.
3. Game validates whether the vehicle can move along its lane/path.
4. Valid vehicles move toward a waiting/boarding area.
5. Matching passengers/units are resolved.
6. Capacity/slot state changes.
7. Game checks continue / win / lose conditions.

สมมติฐานเบื้องต้น:

1. ผู้เล่นอ่านสถานการณ์บนกระดาน
2. เลือก/แตะรถ
3. ระบบตรวจว่ารถเคลื่อนที่ตาม lane/path ได้หรือไม่
4. รถที่เคลื่อนได้เข้าสู่พื้นที่พัก/รับผู้โดยสาร
5. ระบบจับคู่ผู้โดยสาร/ยูนิตที่ตรงเงื่อนไข
6. อัปเดตจำนวนที่นั่งหรือช่องพัก
7. ตรวจ Continue / Win / Lose

## 6. MVP discovery target / เป้าหมาย MVP ในระดับ Discovery

The first meaningful milestone should be a **single playable vertical slice** proving the core puzzle loop before building large quantities of content.

Milestone แรกควรเป็น **Playable Vertical Slice 1 ด่าน** ที่พิสูจน์ว่า core loop เล่นได้และสนุกพอ ก่อนลงทุนสร้างด่านจำนวนมากหรือระบบ meta-game

### Confirmed MVP boundary

V1/MVP is confirmed as **offline/local-first only**.

MVP/V1 ยืนยันให้เป็น **offline/local-first**

Included candidate MVP scope:

- One playable level
- Tap/select vehicle
- Lane/path movement validation
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

## 7. Design principles / หลักการออกแบบ

1. **Deterministic rules first** — gameplay state must be explainable and testable.
2. **Game engine separated from presentation where practical** — UI must not become the source of gameplay truth.
3. **Data-driven levels** — levels should eventually be representable as structured data.
4. **Fast iteration** — prioritize a playable prototype before polish.
5. **Original identity** — inspiration is acceptable; direct cloning is not the product goal.
6. **Mobile-first interaction** — tap targets, feedback, and readability must suit Android phones.
7. **Short-session clarity** — decisions should be readable enough for casual players within a 2–5 minute session.

ภาษาไทย:

1. กติกาต้อง deterministic และ test ได้
2. แยก game logic ออกจาก presentation เท่าที่เหมาะสม
3. Level ควรขับเคลื่อนด้วย structured data
4. ทำ playable prototype ให้เร็ว ก่อน visual polish
5. เกมต้องมี identity ของเราเอง
6. Interaction ต้องออกแบบสำหรับมือถือ Android เป็นหลัก
7. การตัดสินใจในเกมต้องอ่านสถานการณ์ได้ง่ายพอสำหรับผู้เล่น Casual ในรอบ 2–5 นาที

## 8. Gate 0 decision register / บันทึกการตัดสินใจ Gate 0

| Decision | Status | Product Owner decision |
|---|---|---|
| Target player | ✅ Confirmed | Casual players of all ages |
| Intended session length | ✅ Confirmed | 2–5 minutes |
| Movement model | ✅ Confirmed | Lane/path-based |
| MVP boundary | ✅ Confirmed | Offline/local-first; no backend/login/ads/IAP/leaderboard/multiplayer in V1 |
| Unique gameplay hook / differentiation | ❌ Open | TBD |
| Visible information before a move | ❌ Open | TBD |

## 9. Remaining Discovery questions / คำถามที่ยังเปิดอยู่

### D-01 — Unique Hook / Differentiation

What is the defining mechanic or combination of mechanics that makes **Car Party Hobby** meaningfully different from existing bus-party puzzle games?

อะไรคือ mechanic หลัก หรือชุด mechanic ที่ทำให้ **Car Party Hobby** แตกต่างจากเกม bus-party ที่มีอยู่จริงอย่างชัดเจน

### D-02 — Visible Information

What information can the player see before making a move?

ผู้เล่นสามารถเห็นข้อมูลใดก่อนตัดสินใจกดรถ เช่น สีรถ, capacity, passenger queue, waiting slots, path blocking หรือข้อมูลบางส่วนเท่านั้น

The following details are intentionally deferred to Phase 1 after these two discovery decisions are closed:

- Exact path/blocking rules
- Exact minimum fun loop
- Formal deadlock definition
- Win/lose rule specification
- Level schema details

## 10. Gate 0 exit criteria / เกณฑ์ผ่าน Gate 0

| Criterion | Status |
|---|---|
| Project objective is clear | ✅ Pass |
| Target player and intended experience are defined | ✅ Pass |
| Inspiration vs original differentiation is documented | ❌ Open — unique hook required |
| MVP boundary is understood | ✅ Pass |
| Major discovery questions are answered or deliberately deferred | 🟡 Partial — D-01 and D-02 remain |
| No implementation architecture has been prematurely locked beyond user-provided constraints | ✅ Pass |

**Gate 0 status:** **OPEN — 2 decisions remaining**

**Next after approval:** Phase 1 — Requirements.
