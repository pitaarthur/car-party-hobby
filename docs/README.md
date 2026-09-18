# Project Documentation / เอกสารโครงการ

This repository follows the project's Standard Project Development Framework with phase gates and explicit agent routing.

Repository documents are the source of truth. Chat discussions can inform decisions, but approved decisions should be recorded here before downstream implementation.

รีโปนี้ใช้ Standard Project Development Framework แบบมี Phase Gate และกำหนด Agent ที่รับผิดชอบอย่างชัดเจน โดยเอกสารใน repository เป็น source of truth หลัก การสนทนาใน Chat ใช้ประกอบการตัดสินใจได้ แต่ข้อสรุปที่อนุมัติแล้วต้องถูกบันทึกใน repo ก่อนส่งต่อไปยัง Phase ถัดไป

## Current project state / สถานะโครงการ

| Item | Status |
|---|---|
| Current phase | **Phase 1 — Requirements** |
| Current gate | **Gate 1 — Candidate for Product Owner Approval** |
| Previous gate | **Gate 0 — Approved** |
| Product Owner | **Human / Repository owner** |
| Coding | **Blocked for gameplay implementation** |
| Primary Phase 1 agent | **ChatGPT: Work — Business/System Analyst** |
| Supporting / review | **ChatGPT: Chat + Gemini: Chat** |

## Documents

### Discovery
- [Project Brief — Gate 0 Approved](00-discovery/project-brief.md)

### Requirements
- [Phase 1 Requirements — Gate 1 Candidate](01-requirements/requirements.md)

### Framework
- [Project Development Phases](framework/project-development-phases.md)
- [Agent Routing Matrix](framework/agent-routing-matrix.md)

## Working principle / หลักการทำงาน

**Input → Analysis → Artifact → Review → Gate → Next Phase**

A specialist agent owns phase work, the orchestrator controls workflow, an independent reviewer checks where required, and the Product Owner owns final decisions.

**Specialist Agent รับผิดชอบงานตามขอบเขต, Orchestrator ควบคุม workflow, Reviewer ตรวจแบบอิสระเมื่อจำเป็น และ Product Owner เป็นผู้ตัดสินใจสุดท้าย**
