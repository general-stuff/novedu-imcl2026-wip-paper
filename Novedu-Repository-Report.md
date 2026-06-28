# Novedu Repository Report

**Repository:** https://github.com/melanie-bauer/Novedu
**Commit referenced:** `a0f05da` (PR #62, 2026-06-15), `main` branch
**Last updated:** 2026-06-28

This document is a detailed reference accompanying the Novedu conference paper. It describes
the project's evolution over time (with dates) and its latest architecture. It deliberately
stays at the "bigger picture" level and avoids low-level implementation detail, in line with
the paper's scope.

Three companion documents go deeper on specific topics. The document on the teacher-authored
MVP seed covers that clean-slate seed in detail. The transition document narrates *why* the
project moved from the Open WebUI experiment to the custom MVP, including the role of the
product owner (Peter Bauer, Head of the Computer Science Department), the 2026-05-26
teacher-panel presentation, and the resulting decision to cut scope to a classroom-ready MVP.
The survey document covers the school-wide student/teacher survey that ran in parallel
(designed 2026-03, fielded 2026-04-16 to 05-12, analyzed 2026-05-27) and supplied the
empirical requirements evidence. The transition document is the best starting point for the
decision history; the present document is the reference for the repository facts.

---

## 1. What Novedu Is

Novedu is a teacher-configurable AI-tutoring platform developed at **HTBLA Leonding**
(Austria) as a cooperative software-engineering project. 4th-grade students build it as a
study project under my guidance as their teacher. The goal is to move a school from ad-hoc
use of generic chatbots (ChatGPT, Claude) toward an institutionally embedded platform where
teachers assemble subject-specific tutors that align with their instruction, while preserving
privacy, budget control, and oversight.

The repository spans **232 commits** from **2025-10-06** to **2026-06-15**. Development is
organized through feature branches merged into `dev` and then `main`, with numbered GitHub
issues (#6, #28, #29, #37, #47, etc.) used as the unit of work.

The single most important fact for understanding the project: **it went through two major
architectural eras**, an off-the-shelf open-source prototype (Open WebUI + LiteLLM), followed
by a custom-built platform, and the custom platform itself was re-founded once (a React/Vite
frontend prototype, then a full Next.js agent-driven MVP). See the timeline below.

---

## 2. Development Timeline (Phases with Dates)

### Phase 0 — Vision (2025-10-06 → 2025-10-07)
The project opens with a vision document (`docs/vision/project_vision.md`). It frames the
core tension public schools face: they must adopt AI, but cannot ethically or legally rely on
commercial data-collecting tools. It defines minimal goals (teachers edit system prompts and
pick an LLM per tutor; students consume but cannot reconfigure; budget control; LaTeX/code
rendering; European/self-hostable; open-source components) and extended goals (SSO via
Entra ID, multi-provider support, class-based tutor control, dashboards).

### Phase 1 — Infrastructure & Infrastructure-as-Code bootstrapping (2025-10-20 → 2025-11-09)
First infrastructure work. Azure **Bicep** templates for Infrastructure-as-Code
(`infra/bicep`), an Azure OpenAI module, a PostgreSQL database and VNet for the LiteLLM
connection, deployment scripts, and a GitHub Actions Azure deploy workflow. The platform was
*originally authored for Azure deployment*, which explains why the early infrastructure is
Azure-centric; it was later migrated (see Phase 5a).

### Phase 2 — Technology evaluation & requirements research (2025-11-18 → 2025-12-16)
A documentation-heavy phase establishing the "build vs. buy" rationale the project leans on.
Artifacts (in `docs/analysis` and `docs/planning`):
- **LLM gateway comparison** (issue #6): LiteLLM vs. OpenRouter vs. building an own gateway,
  with pricing, budget-control, and privacy criteria. LiteLLM selected.
- **Chat UI comparison**: Open WebUI vs. LibreChat. Open WebUI selected.
- **SWOT analysis**, **budget estimate spreadsheet**, **project structure plan (PSP)**, and
  early user stories.
- First meeting minutes appear (2025-12-04), establishing a recurring cadence.

### Phase 3 — Open WebUI + LiteLLM prototype (2025-12-28 → 2026-03-17)
This is the open-source prototype phase. It composes existing open-source products rather than
writing application code:
- **Microsoft Entra ID SSO** integrated into Open WebUI (issue around #18/#19/#21,
  2026-01-04 → 2026-01-12): OpenID config, OAuth signup, account merging by email,
  cookie/session hardening.
- **OAuth group management** for mapping school groups to Open WebUI groups.
- **User & role management utilities** (`util/OWUI-IAM`, 2026-01-24) including regex-safe
  username filtering so teachers can carry two names.
- **LiteLLM budget control**: default per-user budgets (2026-01-25) and a
  **weekly-reset budget assignment script** (2026-02-17, `util/LiteLLM_User_Migration`).
- A migration script (2026-01-26) that copies Open WebUI users into the LiteLLM database
  for internal usage/cost tracking.
- **Docker Compose** stack added 2026-03-17 (issue #34): Caddy reverse proxy, Postgres,
  LiteLLM, Open WebUI, optional pgAdmin, and a Python worker.

The prototype's defining trait: it is assembled from off-the-shelf components, with custom
work living in configuration, IAM glue scripts, and infrastructure.

### Phase 4 — Design of the custom platform (2026-02-07 → 2026-05-26)
In parallel with the prototype, we designed the dedicated Novedu platform. This phase was a
**stakeholder-governed design task**: building on the lived experience of the Open WebUI
experiment, the students were asked to define a feature set and a UX prototype, guided by the
project's **main product owner, Peter Bauer, Head of the Computer Science Department**
(*Abteilungsvorstand für Informatik*) at HTBLA Leonding. Two strands ran concurrently:
- **Data model / ERD (issue #28, 2026-02-07 → 2026-05-10):** a long, iterative design of the
  domain model (`docs/analysis/erd.puml`). Entities grew from User → Role/Permission/Class →
  Subject/LLMModel/AITutor/TutorClass → School-specific AI provider/model tables and
  `TutorConfig`/`TutorDocument`. Types were repeatedly refined (UUID → int keys, timestamptz,
  enums for budget periods and didactic modes). This encodes the intended platform: schools,
  classes, subjects, configurable tutors, per-tutor documents, and budget/cost tracking.
- **Requirements & API design:** a reworked requirements analysis (`docs/requirements`,
  issue #27), an application-routes suggestion, and API documentation for tutor/chat/class
  routes (issue #38, 2026-04-27).
- **UX click-dummy (issue #29, 2026-02-22 → 2026-05-26):** a **React + Vite + shadcn/ui**
  single-page app in `frontend/`. It is a **pure click-through prototype** driven entirely by
  one `mockData.ts` file with **no backend calls whatsoever** (verified: zero `fetch`/`axios`
  in `frontend/src`). It nonetheless renders the *full* intended surface: login, chat, tutor
  editor/overview/read-only, student home, and an admin area (dashboard, provider/user/group/
  budget/cost management, global prompt). It represents the **maximal scope** of the Novedu
  vision. Work happened in three bursts (Feb 22 kickoff → late-April rework "based on video"
  → a final May 25–26 polish), and the folder was **frozen after 2026-05-26**: its last
  commits are a morning burst (07:15–08:25) plus a 16:02 cleanup.

  **The presentation and its consequence (2026-05-26):** on this date the students presented
  the click-dummy to a **diverse teacher panel** (*Allgemeinbildung*, STEAM, Computer Science).
  It was **well received, but the scope was judged too large.** This triggered the project's
  pivotal decision, to cut scope to a classroom-ready MVP (see Phase 5b and the transition
  document). The presentation and the product-owner role are not recorded in the repository;
  they are documented here from my own recollection.

### Phase 5a — Deployment migration: Azure → in-school Proxmox (2026-04-07 → 2026-04-24)
A significant operational shift. Commits "update files based on proxmox deployment" and
"Add python worker" (issue #37) mark the **migration from Azure cloud deployment to an
in-school Proxmox / container-based solution**. This aligns the deployment with the vision's
"self-hostable, runs in European data centers, on-premises" requirement and reduces cloud
dependency. The Bicep/Azure assets remain in the repo but are no longer the primary
deployment target.

### Phase 5b — The MVP harness: agent-driven full rebuild (2026-06-06 → 2026-06-15)
The most consequential recent change, and a **direct response to the 2026-05-26 panel
verdict** that the designed platform was too large in scope. Tasked with boiling the vision
down to a minimal viable product usable in real classroom experiments much faster, I first
built a clean-slate MVP seed (in a 2026-06-08 workshop with the students; see the companion
document on the MVP seed) and handed it to the students on 2026-06-10. They then re-founded
the custom application as a **Next.js full-stack MVP** built through an **agent-driven
development harness**, replacing the Vite click-dummy as the live application:
- **2026-06-06 → 2026-06-08:** MVP ERD (issue #45) and "Novedu MVP harness" initialized.
- **2026-06-11 → 2026-06-15 (issues #47, #48, #44):** the working chat MVP lands —
  CoPilotKit chat UI, a Mastra tutor agent, AG-UI streaming, Markdown/KaTeX rendering,
  YAML-defined tutors, GitHub-backed tutor catalog loading, signed share-link verification,
  and Microsoft Entra ID login via Auth.js.
- **2026-06-14:** the `mvp-harness` folder is renamed to `application/`, becoming the
  canonical app. The Vite `frontend/` prototype is retained but superseded.
- **2026-06-15:** chat shell with uploads + logout, local YAML tutors wired into the
  runtime, tutor configs loaded from a GitHub catalog, math rendering. Final merges
  (#57–#62) bring it into `main`.

This phase introduces a distinctive methodology: development is driven by AI agents governed
by `AGENTS.md`, a five-stage state machine (`intake → reproduce → implement → verify →
close`), repo-local skills in `.agents/skills`, and an evidence/verification discipline
(Playwright recordings, before/after artifacts). This methodology is itself part of the
project's "cooperative software-engineering project" story.

---

## 2b. Contributors — A Student-Driven Project

The commit history makes clear that Novedu is **built by students, not by teachers**. Across
all branches there are **8 distinct author identities**, which consolidate into **5 people**
(several committed under more than one email/name):

| Contributor | Role | Commits | Share |
|---|---|---|---|
| Lukas Larndorfer (`llarndorfer` / `lukaslarndorfer`) | Student | 100 | ~40% |
| Melanie Bauer (repo owner) | Student | 100 | ~40% |
| André Wildberger | Student | 28 | ~11% |
| Konstantin Wetscher | Student | 19 | ~8% |
| **Rainer Stropek** | **Teacher (supervisor)** | **5** | **~2%** |

(~252 commits counted across all branches; student email addresses use the
`@students.htl-leonding.ac.at` domain.)

The four students account for roughly **98% of all commits**; I contributed about **2%** as
the supervising teacher, and those few commits are largely coordination-level (e.g. enabling
a CI `workflow_dispatch` trigger, config tweaks) rather than feature work. In other words, the
architecture, prototype, and MVP were designed and implemented by students, with me acting as
guide and project lead. This supports the framing of Novedu as a **cooperative
software-engineering project carried out by students**.

**One notable exception — the vision came from me, the teacher.** I authored the original
project vision document (`docs/vision/project_vision.md`) entirely myself (first draft
2025-10-06, expanded 2025-10-07; all three authoring commits are mine). The only later change
to the file was a folder restructure by a student (Lukas Larndorfer, 2026-02-03), which moved
but did not rewrite it. This is a clean division of labour: I **set the vision and goals**, and
the **students designed and built the system** that realizes them.

---

## 3. Architectural Transitions (Summary)

The project's evolution rests on four transitions:

1. **Off-the-shelf → custom.** Open WebUI + LiteLLM prototype (Phase 3) → purpose-built
   Novedu platform (Phases 4–5). The prototype proved acceptance and surfaced requirements;
   the custom platform was built to organize tutoring around educational workflows.
2. **Two custom-platform foundations.** A React/Vite/shadcn click-through frontend
   (Feb–May 2026) → a Next.js agent-runtime full-stack MVP (June 2026). The second is the
   live architecture.
3. **Persistence philosophy reversal.** The prototype centered on a LiteLLM **PostgreSQL**
   database (users, budgets, spend logs). The MVP deliberately **avoids a database for tutor
   configs**, instead using **GitHub-backed configuration**, and **stores no chat history,
   uploaded content, or secrets**, a privacy-first stance.
4. **Cloud → on-premises.** Azure Container Apps / Bicep (original) → in-school **Proxmox**
   container deployment + in-region **vLLM GPU** inference via an SCCH-operated endpoint.

---

## 4. Latest Architecture & Structure (as of 2026-06-15)

### Repository layout (top level)
- `application/` — **the live MVP**. Next.js App Router full-stack application.
- `frontend/` — earlier React/Vite/shadcn click-through prototype (superseded, retained).
- `infra/` — `bicep/` (legacy Azure IaC) and `docker/` (Compose stack for the
  Open WebUI + LiteLLM prototype: Caddy, Postgres, LiteLLM, Open WebUI, pgAdmin, py-worker).
- `util/` — IAM and migration scripts from the prototype era (`OWUI-IAM`,
  `LiteLLM_User_Migration`).
- `docs/` — vision, analysis (ERD, gateway/UI comparisons, SWOT), requirements, meetings,
  planning (budget, PSP), MVP user stories, harness operating model, presentations.
- `.agents/skills/`, `.codex/`, `AGENTS.md` — the agent-driven development harness.
- `.github/workflows/` — CI/deployment workflows.

### The live application (`application/`) — technology stack
From `application/package.json` and `AGENTS.md`:
- **Framework:** Next.js 16 (App Router), React 19, TypeScript.
- **Auth:** Auth.js / NextAuth with **Microsoft Entra ID** as the production target.
- **Chat UI:** CoPilotKit (`@copilotkit/react-core`, `@copilotkit/runtime`).
- **Agent runtime:** **Mastra** (`@mastra/core`) as the provider-independent agent layer.
- **Streaming contract:** **AG-UI** (`@ag-ui/*`) as the fixed frontend/backend stream
  contract; Mastra and provider choices stay behind backend adapters.
- **Inference provider:** an in-region **vLLM GPU** server operated by SCCH, exposing an
  OpenAI-compatible API (`SCCH_BASE_URL`/`SCCH_API_KEY`), wired via `@ai-sdk/openai`. Tutor YAMLs reference
  models hosted there (e.g. `cyankiwi/GLM-4.7-Flash-AWQ-4bit`).
- **Rendering:** react-markdown + remark-gfm + remark-math + rehype-katex (KaTeX) for math,
  react-syntax-highlighter for code; pdfjs-dist for document extraction.
- **Config & validation:** YAML tutor definitions parsed and validated with Zod.
- **Tooling/quality:** Biome (lint/format), Vitest (unit + browser), Playwright (E2E),
  with a `verify` chain (typecheck → lint → test → build → e2e).

### How tutors work in the MVP (the platform's core idea, concretely)
A tutor is a **YAML definition** (`application/tutors/*.yaml`) carrying an id, name,
description, an `llm` block (model + image-input flag), and a `prompt` block with reusable
instruction modules (e.g. a patient Socratic math tutor). Tutor definitions are loaded either
from local files or from a **GitHub catalog** (`lib/tutors/github-catalog.ts`, default path
`AI/` on `main`). At request time, the CopilotKit route resolves the active tutor, a single
Mastra agent loads and assembles its system prompt + model from the YAML, and answers stream
back over AG-UI. This realizes the project's "modular tutors assembled from reusable
text/prompt modules" idea. The MVP also supports a **signed share-link** flow for distributing
a tutor, alongside a direct authenticated `/chat` entry.

### Intended data model (design, not yet the live persistence)
The ERD work (issue #28, and the MVP ERD #45) defines Schools, Classes, Users,
Roles/Permissions, Subjects, LLM models, AI tutors with per-class assignments, tutor
documents, and budget/cost tracking. The live MVP intentionally implements only a slice of
this (no DB for tutor configs) and leaves the fuller model for later phases.

---

## 5. Status at Time of Analysis

Consistent with the structured abstract: the **open-source prototype phase is essentially
complete** (deployed, with SSO, budget control, and usage tracking), and the **custom
platform + broader evaluation are ongoing**. As of 2026-06-15 the custom platform exists as a
working Next.js MVP (chat with configurable YAML tutors, Entra login, math/code rendering,
GitHub-backed config) but does not yet implement the full admin/dashboard/class-management
surface designed in the Vite prototype and the ERD.

---

## 6. Quick Reference: Key Dates

| Date | Event |
|---|---|
| 2025-10-06 | Project vision document |
| 2025-10-20 → 11-09 | Azure Bicep IaC, OpenAI module, deploy workflow |
| 2025-11-18 → 12-16 | LLM gateway + chat UI comparisons, SWOT, PSP, budgets (LiteLLM + Open WebUI chosen) |
| 2026-01-04 → 01-12 | Entra ID SSO + OAuth group management in Open WebUI |
| 2026-01-24 → 02-17 | IAM utilities, LiteLLM budgets, weekly reset, user migration |
| 2026-02-07 → 05-10 | ERD design (issue #28) |
| 2026-02-22 → 05-26 | React/Vite/shadcn UX click-dummy (issue #29), guided by product owner Peter Bauer |
| 2026-05-26 | Click-dummy presented to the *Allgemeinbildung/STEAM/CS* teacher panel — well received, scope judged too large → decision to cut to an MVP |
| 2026-03-17 | Docker Compose stack for the prototype (issue #34) |
| 2026-04-07 → 04-24 | Migration from Azure to in-school Proxmox deployment (issue #37) |
| 2026-04-27 | API documentation for the custom platform (issue #38) |
| 2026-06-06 → 06-08 | MVP harness + MVP ERD initialized (issues #45, #47) |
| 2026-06-11 → 06-15 | Working Next.js MVP: CoPilotKit + Mastra + AG-UI + YAML tutors + Entra login + GitHub catalog |
