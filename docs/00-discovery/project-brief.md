# Phase 0 — Project Brief

**Project:** Car Party Hobby  
**Status:** Draft — Gate 0 pending  
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

## 3. Product hypothesis / สมมติฐานผลิตภัณฑ์

The core experience should be a short-session deterministic puzzle where players inspect a congested board, choose vehicle movement order, manage limited temporary capacity, and resolve matching/boarding constraints.

ประสบการณ์หลักควรเป็น Puzzle เล่นเป็นรอบสั้น ๆ ที่ผู้เล่นต้องอ่านสถานการณ์บนกระดาน เลือกลำดับการเคลื่อนรถ บริหารพื้นที่พักที่มีจำกัด และแก้เงื่อนไขการจับคู่/ขึ้นรถให้สำเร็จ

## 4. Candidate core loop — discovery hypothesis only

The following is a hypothesis to validate in Phase 1, not an approved gameplay specification:

1. Player inspects the board.
2. Player selects/taps a vehicle.
3. Game validates whether the vehicle can move.
4. Valid vehicles move toward a waiting/boarding area.
5. Matching passengers/units are resolved.
6. Capacity/slot state changes.
7. Game checks continue / win / lose conditions.

สมมติฐานเบื้องต้น:

1. ผู้เล่นอ่านสถานการณ์บนกระดาน
2. เลือก/แตะรถ
3. ระบบตรวจว่ารถเคลื่อนที่ได้หรือไม่
4. รถที่เคลื่อนได้เข้าสู่พื้นที่พัก/รับผู้โดยสาร
5. ระบบจับคู่ผู้โดยสาร/ยูนิตที่ตรงเงื่อนไข
6. อัปเดตจำนวนที่นั่งหรือช่องพัก
7. ตรวจ Continue / Win / Lose

## 5. MVP discovery target / เป้าหมาย MVP ในระดับ Discovery

The first meaningful milestone should be a **single playable vertical slice** proving the core puzzle loop before building large quantities of content.

Milestone แรกควรเป็น **Playable Vertical Slice 1 ด่าน** ที่พิสูจน์ว่า core loop เล่นได้และสนุกพอ ก่อนลงทุนสร้างด่านจำนวนมากหรือระบบ meta-game

Candidate MVP scope to validate in Phase 1:

- One playable level
- Tap/select vehicle
- Movement validation
- Limited waiting/parking capacity
- Matching/boarding resolution
- Win condition
- Lose/deadlock condition
- Restart level

Out of scope until later validation:

- Ads
- IAP
- Cloud account
- Leaderboard
- Multiplayer
- Large level catalogue
- LiveOps
- Complex backend
- Production analytics

## 6. Design principles / หลักการออกแบบ

1. **Deterministic rules first** — gameplay state must be explainable and testable.
2. **Game engine separated from presentation where practical** — UI must not become the source of gameplay truth.
3. **Data-driven levels** — levels should eventually be representable as structured data.
4. **Fast iteration** — prioritize a playable prototype before polish.
5. **Original identity** — inspiration is acceptable; direct cloning is not the product goal.
6. **Mobile-first interaction** — tap targets, feedback, and readability must suit Android phones.

ภาษาไทย:

1. กติกาต้อง deterministic และ test ได้
2. แยก game logic ออกจาก presentation เท่าที่เหมาะสม
3. Level ควรขับเคลื่อนด้วย structured data
4. ทำ playable prototype ให้เร็ว ก่อน visual polish
5. เกมต้องมี identity ของเราเอง
6. Interaction ต้องออกแบบสำหรับมือถือ Android เป็นหลัก

## 7. Discovery questions for Phase 0 / คำถามที่ต้องปิดก่อน Gate 0

- Who is the target player?
- What makes the game meaningfully different from existing bus-party puzzles?
- What is the intended session length?
- What is the minimum fun loop?
- Is movement grid-based, path-based, or lane-based?
- What information is visible to the player before a move?
- What constitutes an unrecoverable/deadlock state?
- Is the MVP strictly offline/local?
- What is explicitly out of scope for V1?

## 8. Gate 0 exit criteria / เกณฑ์ผ่าน Gate 0

Gate 0 can be approved when:

- Project objective is clear.
- Target player and intended experience are defined.
- Inspiration vs original differentiation is documented.
- MVP boundary is understood.
- Major discovery questions are answered or deliberately deferred.
- No implementation architecture has been prematurely locked beyond user-provided constraints.

**Next after approval:** Phase 1 — Requirements.
