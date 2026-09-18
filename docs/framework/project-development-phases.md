# Project Development Phases

This project uses a gated lifecycle. Lightweight phases may be reduced when they add no value, but skipping a phase must be an explicit decision rather than an accidental omission.

โครงการนี้ใช้ lifecycle แบบมี Gate โดยสามารถลด ceremony ของบาง Phase ได้เมื่อไม่สร้างคุณค่า แต่การข้ามต้องเป็นการตัดสินใจที่ชัดเจน ไม่ใช่ข้ามโดยไม่ตั้งใจ

| Phase | Purpose / เป้าหมาย | Primary Role | Human Gate |
|---|---|---|---|
| **0 Discovery** | Define problem, player, opportunity, scope / นิยามปัญหา ผู้เล่น โอกาส และขอบเขต | Business/System Analyst | ✅ Gate 0 |
| **1 Requirements** | Define functional requirements, gameplay rules, acceptance criteria / กำหนด requirement, gameplay rule และ acceptance criteria | Business/System Analyst | ✅ Gate 1 |
| **2 Domain & Data** | Define game domain, state model, level schema / ออกแบบ domain, game state และ level schema | System Architect | ✅ Gate 2 |
| **3 Architecture** | Lock approved technical architecture and ADRs / อนุมัติ architecture และ ADR | System Architect | ✅ Gate 3 |
| **4 UX/UI** | Define game flow, HUD, interaction, visual direction / ออกแบบ flow, HUD, interaction และ visual direction | UX/UI Agent | ✅ Gate 4 |
| **5 Implementation Planning** | Convert approved design into executable tasks / แตกงานเป็น implementation plan ที่ลงมือได้ | Planning Agent | ✅ Gate 5 |
| **6 Development** | Implement scoped tasks / พัฒนาตาม task ที่อนุมัติ | Frontend/Game Dev Agent | — |
| **7 Testing & QA** | Verify engine, integration, level integrity, regression / ตรวจ logic, integration, level และ regression | QA Agent | ✅ Gate 6 |
| **8 UAT** | Validate product behavior with Product Owner / ทดสอบการใช้งานจริงกับ Product Owner | QA/UAT Agent | ✅ Product Owner |
| **9 Deployment** | Build and release controlled artifacts / build และ release อย่างควบคุม | Release/DevOps Agent | ✅ Gate 8 |
| **10 Operation & Iteration** | Observe, prioritize, iterate / ติดตามผล จัดลำดับ และปรับปรุง | Orchestrator | Human decision for major change |

## Standard handoff

**Input → Analysis → Artifact → Review → Gate → Handoff**

Each phase must consume approved artifacts from prior phases. Downstream agents must not silently redefine upstream decisions.

แต่ละ Phase ต้องใช้ artifact ที่อนุมัติจาก Phase ก่อนหน้า และ Agent ปลายทางห้ามเปลี่ยน decision จากต้นทางเองโดยไม่ส่งกลับไป review

## Change control

If implementation discovers a conflict with an approved architecture or requirement:

1. Stop the affected scope.
2. Record the conflict.
3. Route it to the owning upstream role.
4. Review the proposed change.
5. Re-approve the affected gate if the change is material.

หากพบว่าการ implement ขัดกับ requirement หรือ architecture ที่อนุมัติแล้ว ให้หยุดเฉพาะ scope ที่เกี่ยวข้อง บันทึกปัญหา ส่งกลับ role เจ้าของ decision และ re-approve Gate เมื่อเป็นการเปลี่ยนแปลงสาระสำคัญ
