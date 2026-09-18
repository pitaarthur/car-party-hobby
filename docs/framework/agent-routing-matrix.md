# Agent Routing Matrix

This matrix maps project roles to the specific AI surface that should normally execute or review each phase.

ตารางนี้กำหนดว่าแต่ละ Phase ใช้ Role ใด และควรส่งงานไปยัง AI/Surface ใดเป็นค่าเริ่มต้น

| Phase | Primary Role | Specific Primary Agent | Supporting / Review | Specific Supporting Agent | Human Gate |
|---|---|---|---|---|---|
| **0 Discovery** | Business/System Analyst | **ChatGPT: Work** | Orchestrator | **ChatGPT: Chat** | ✅ Gate 0 |
| **1 Requirements** | Business/System Analyst | **ChatGPT: Work** | Orchestrator + Reviewer | **ChatGPT: Chat + Gemini: Chat** | ✅ Gate 1 |
| **2 Domain & Data** | System Architect | **ChatGPT: Work** | Analyst + Game Logic Agent | **ChatGPT: Chat + Gemini: Chat** | ✅ Gate 2 |
| **3 Architecture** | System Architect | **ChatGPT: Work** | Independent Reviewer | **Gemini: Chat** | ✅ Gate 3 |
| **4 UX/UI** | UX/UI Agent | **Gemini: AGY / IDE** | Game Designer + Reviewer | **ChatGPT: Work + Gemini: Chat** | ✅ Gate 4 |
| **5 Implementation Planning** | Planning Agent | **ChatGPT: Work** | Architect + Orchestrator | **ChatGPT: Chat** | ✅ Gate 5 |
| **6 Development** | Frontend / Game Dev Agent | **ChatGPT: Codex** | Implementation support | **Gemini: AGY / IDE** | — |
| **7 Testing & QA** | QA Agent | **ChatGPT: Codex** | Independent Reviewer | **Gemini: CLI / Chat** | ✅ Gate 6 |
| **8 UAT** | QA/UAT Agent | **ChatGPT: Work** | Orchestrator | **ChatGPT: Chat** | ✅ Product Owner |
| **9 Deployment** | Release / DevOps Agent | **ChatGPT: Codex + CLI** | QA + Reviewer | **Gemini: CLI + ChatGPT: Chat** | ✅ Gate 8 |
| **10 Operation & Iteration** | Orchestrator | **ChatGPT: Work** | Analyst + QA | **ChatGPT: Chat + Codex** | Human decision for major change |

## Surface responsibilities / หน้าที่ของแต่ละ Surface

### ChatGPT

**Work**
- Project brain and persistent working context
- Requirements, architecture, planning, UAT artifacts
- Cross-document synthesis
- Orchestration of substantial project work

**Chat**
- Product Owner discussion
- Requirement grilling
- Decision support
- Gate review
- Fast architectural/design discussion

**Codex**
- Repository implementation
- Multi-file code changes
- Test implementation
- Refactoring within approved scope
- Release preparation

**CLI**
- Git execution
- Expo/EAS commands
- Package management
- Test/lint/build execution
- Deployment commands

### Gemini

**AGY / Antigravity**
- Rapid visual/frontend implementation
- Interactive UI iteration
- Animation and layout experimentation
- Focused game-screen implementation

**IDE**
- Local contextual pair programming
- Focused debugging
- Small refactors
- Current-file/current-screen implementation

**CLI**
- Independent repository inspection
- Build/test diagnostics
- Secondary QA
- Independent implementation review

**Chat**
- Second-opinion reasoning
- Requirement review
- Architecture review
- Game-design critique
- UX critique

## Operating rules / กติกาการทำงาน

1. **Orchestrator owns workflow. Specialized agents own scoped work. Product Owner owns decisions.**
2. **Implementer must not be the final independent reviewer.**
3. Choose agents by responsibility, not by brand.
4. Do not use a second agent unless it adds genuine separation of concerns, independent review, or useful specialization.
5. A downstream agent must not silently alter an approved upstream decision.
6. CLI is primarily an execution surface, not the owner of product or architecture decisions.
7. For small MVP work, avoid unnecessary agent fragmentation; split Game Engine and Game UI agents only when coordination cost is justified.
8. Repository artifacts are the source of truth after approval.

ภาษาไทยโดยสรุป:

- Orchestrator ควบคุม workflow
- Specialist Agent รับผิดชอบงานตาม scope
- Product Owner เป็นผู้ตัดสินใจสุดท้าย
- Implementer กับ Final Reviewer ต้องไม่เป็น agent ตัวเดียวกัน
- เลือก Agent ตามหน้าที่ ไม่ใช่ตามค่าย
- ห้าม Agent ปลายทางแก้ decision ที่อนุมัติแล้วเองโดยเงียบ ๆ
- หลังอนุมัติ ให้เอกสารใน repository เป็น source of truth

## Current routing

**Current phase:** Phase 0 — Discovery  
**Primary:** ChatGPT: Work — Business/System Analyst  
**Supporting:** ChatGPT: Chat — Orchestrator / Product Owner discussion  
**Coding:** Blocked  
**Next gate:** Gate 0
