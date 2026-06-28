# Novedu Chat-MVP Prototype

**Repository:** https://github.com/Teaching-HTL-Leonding/novedu-chat-mvp
**Reference commit:** `82615f9` ("Migrate mathematics tutor files from prompt-database to new format", 2026-06-10), the point in time described throughout this document.
**Also present as:** `melanie-bauer/Novedu`, branch `feat/chat-window`.

This document describes the seed repository I built for the new-architecture Novedu MVP. It is a companion to the document on the full `melanie-bauer/Novedu` history, which covers what that document calls "Phase 5b, the MVP harness." This prototype is the seed of that phase. For the decision history that explains why this MVP exists at all (the Open WebUI experiment, the stakeholder-governed design phase under product owner Peter Bauer, the 2026-05-26 teacher-panel presentation, and the resulting "scope is too large, cut to an MVP" verdict), see the companion document on the transition between the two architectures. The present document explains where the current architecture came from; the transition document explains why it was scoped the way it was.

**Why this MVP was deliberately small.** This is not a from-scratch reimagining of the whole vision, it is a scope-reduced one. On 2026-05-26 the students presented a full UX prototype (the click-dummy from the design phase) to a diverse teacher panel. It was well received but judged too broad. The task that produced this repository was explicitly to boil that vision down to a minimum viable product that could be used in classroom experiments much faster. That framing explains the feature choices below: a working teacher-configurable tutor chat, and almost nothing institutional (no admin console, budgets, or dashboards). Those were intentionally deferred, not forgotten.

---

## 1. Why This Repository Matters

The full project history describes two architectural eras: an off-the-shelf **Open WebUI + LiteLLM** prototype, then a **custom-built platform**. The custom platform itself was re-founded once. This repository is that re-founding, the clean-slate restart of custom development with a new framework stack and a new architecture, which is the basis for all current Novedu development.

The defining facts:

- It is a **complete, from-scratch rewrite**: not Open WebUI, not the earlier React/Vite click-through prototype from the design phase, but a real full-stack Next.js application with working chat, auth, and a modular tutor system.
- It establishes the **architecture the live Novedu MVP now runs on** (Next.js + Mastra + CoPilotKit + AG-UI + YAML tutors + Microsoft Entra ID).
- I initiated it, and the students continued it. See §2.

---

## 2. Authorship and Timeline: I Initiate, Students Continue

This is the cleanest illustration in the whole project of the teacher-initiator / student-builder division that runs throughout Novedu.

I authored every one of the **26 commits** in this repository (2026-06-08 to 2026-06-10), committing under `rainer@software-architects.at` and `rainer@timecockpit.com`. There are no student commits in this seed repository.

The timeline was:

- **2026-06-08, workshop.** We worked out the prototype in a workshop I ran with the students. The commits of this day lay the foundation: app skeleton, the chat feature, the Markdown/KaTeX renderer, the first sample tutors, and the tutor validation/building system.
- **2026-06-09 to 2026-06-10, hardening the seed.** I added Microsoft Entra ID authentication and session management, teacher-role resolution, navigation/status-bar UI, student mode, the share-link feature, Docker packaging, a Docker Hub publish workflow, Azure App Service deployment, and a teacher-only `/health` page. The final reference commit (`82615f9`) migrates the mathematics tutor files into the new format.
- **2026-06-10, handover.** I gave the students the code, and they started working on it. From here the story continues in `melanie-bauer/Novedu` (Phase 5b, where student commits begin 2026-06-11 and the folder is later renamed to `application/`).

So the overall sequence is: I set the vision (Oct 2025), the students built the click-through prototype and ran the design phase, I single-handedly built the new-architecture seed in a workshop (this repo, 2026-06-08 to 06-10), and the students then took over and continued it (Phase 5b onward).

---

## 3. The New Architecture

A single, framework-agnostic idea sits at the center: **a tutor is a YAML file, and the app is just a configurable shell around it.** A user opens a chat via a signed link carrying a tutor-definition URL. The app fetches, validates, and assembles that YAML into a system prompt, then runs an LLM configured entirely by the tutor file. Its persona, rules, and model all come from the YAML, not from the app code.

### 3.1 Technology stack

| Layer | Choice |
|---|---|
| Framework | **Next.js 16** (App Router), **React 19**, TypeScript, Node.js 24 |
| Chat UI | **CoPilotKit** v2 (`@copilotkit/react-core`) |
| Stream contract | **AG-UI** (`@ag-ui/*`) between UI and backend |
| Agent runtime | **Mastra** (`@mastra/core`), a single `tutor` agent configured per request |
| Inference | In-region **vLLM** GPU server operated by SCCH, OpenAI-compatible; API key stays server-side |
| Auth | **Auth.js / NextAuth v5** with **Microsoft Entra ID**; JWT/JWE sessions, no DB adapter |
| Agent memory | **Azure SQL** via `@mastra/mssql`, authenticated via Microsoft Entra (no SQL password; `az login` locally, Managed Identity on Azure) |
| Tutor assembly | YAML to Zod schema validation to consistency check to **Handlebars** templating |
| Quality | **Biome** (lint/format), **Vitest** (unit + component), **Playwright** (e2e) |
| Packaging/deploy | Multi-stage **Docker** (standalone, non-root) to **Docker Hub** to **Azure App Service** (Austria East, `novedu-chat-mvp-at`) |

### 3.2 The modular tutor model (the platform's core concept, concretely)

This is the realization of the "tutors assembled from reusable text and prompt modules" idea. Two kinds of plain-YAML files:

- **Fragment libraries**, collections of reusable prompt pieces (a persona, ground rules, a safety policy, a topic description). Each fragment has a `version`, a `priority` (controls assembly order), and an `input_schema` (JSON-Schema-style, with required and optional fields, the latter carrying **defaults**). Maintained centrally.
- **Tutor files**, which select fragments, fill in their inputs, and add final instructions. Written per subject. A tutor references fragment libraries by public URL (absolute `http(s)` or a path **relative** to the tutor YAML), and references a model in its `llm` block.

The pipeline in `lib/tutors/` is deliberately framework-agnostic: **fetch, parse YAML, Zod schema-validate, consistency-check, assemble with Handlebars**, returning a structured `BuildResult` that never throws. The authoring guide (`tutors/README.md`) is explicitly written for **teachers who are not programmers**: if you can edit a structured text file, you can build a tutor. The repo ships real sample tutors, including a German-language mathematics set under `tutors/akornfellner/mathematik/` (Nachhilfelehrer, Prüfungsvorbereitung, Beispiel-Creator).

### 3.3 Auth, roles, and student mode

- Microsoft Entra ID gates the whole app (`auth.ts` + `proxy.ts`; note that Next 16 renamed the `middleware` convention to `proxy`). Any signed-in Entra user passes the gate.
- **Teacher role** is derived from Entra **group membership** (`TEACHER_GROUP_ID`), resolved once at sign-in into `session.user.isTeacher`.
- **Student mode** lets a real teacher view the app as a student; it only ever restricts access, never grants it.

### 3.4 Tutor share links (chat distribution model)

The chat surface is reachable **only** through a **signed deep link** (`/?tutor=<url>&start=<unix>&end=<unix>&sig=<hmac>`): HMAC-SHA256 over the parameters with a server-only `SHARE_LINK_SECRET`, carrying an **inclusive time window**. Teachers generate these on a teacher-only `/share-tutor` page. The signature and window are **re-verified on every runtime request** in the CopilotKit route, so an open chat stops accepting messages once the window closes. Per-user Mastra memory is scoped by the Entra user id.

### 3.5 Agent-driven development methodology

Like the later MVP harness, this repo is built to be worked on by AI coding agents: an `AGENTS.md` (symlinked as `CLAUDE.md`) with hard-won setup notes, a `.agents/skills/` library (Mastra, CopilotKit, Next.js, Microsoft-docs, and others), and a `skills-lock.json`. The `AGENTS.md` opens with a warning that Next.js 16 and Mastra differ from training data and that agents must read the local docs/skills first.

---

## 4. How This Differs From What Came Before (and After)

**vs. the Open WebUI + LiteLLM prototype:** that era composed off-the-shelf products and put all custom work in configuration and IAM glue. This repo is **application code**, a bespoke chat app with its own tutor model, auth, and UI.

**vs. the React/Vite click-through prototype (design phase, issue #29):** that was a mock-data SPA covering the full admin surface but not wired to a backend. This is a **working full-stack app** with a narrower but real feature set (chat with configurable tutors, auth, share links).

**vs. the students' continuation (Phase 5b / `application/`):** the students carried this architecture forward but made some divergent choices. Most notably, this seed persists Mastra agent memory to **Azure SQL** (`@mastra/mssql`, Entra-authenticated), whereas the students' MVP harness moved to a **no-database / filesystem** stance for the MVP and an explicit privacy rule against storing chat history. The students' version also pinned **NextAuth v4** rather than the v5 beta used here. Deployment also differs: this seed targets **Azure App Service**, consistent with the project's original Azure orientation, while the broader Novedu deployment had already begun migrating to **in-school Proxmox**.

---

## 5. Quick Reference: Key Dates

| Date | Event |
|---|---|
| 2026-06-08 | Workshop (teacher + students). Skeleton, chat feature, Markdown/KaTeX renderer, first sample tutors, tutor validation/assembly system. |
| 2026-06-09 | Mastra `tutor` agent integration; Microsoft Entra ID auth + session management. |
| 2026-06-10 | Teacher role resolution; navigation/status bar; student mode; share-link feature; Docker packaging + Docker Hub publish; Azure App Service deploy (Austria East); teacher-only `/health` page; mathematics tutors migrated to new format (commit `82615f9`). |
| 2026-06-10 | **Handover:** I give the students the code; they begin continuing it (student commits from 2026-06-11). |

---

## 6. Key References and Known Caveats

- The authoritative authoring format for tutors and fragments is `tutors/README.md` in this repo (priorities, `input_schema`, defaults, relative fragment URLs).
- The tutor pipeline lives in `lib/tutors/` and is framework-agnostic, so it can be reused rather than re-implemented (fetch, parse, validate, assemble).
- Auth, roles, student mode, and share links are documented in detail in this repo's `AGENTS.md`.
- Known prototype caveats (from the README): `/api/validate-tutor` fetches arbitrary user-supplied URLs server-side (an SSRF concern; production should allow-list hosts and block private IPs); any authenticated Entra user is allowed (no fine-grained authorization beyond the teacher group); and agent memory previously used a single hard-coded resource id before per-user scoping was added.
