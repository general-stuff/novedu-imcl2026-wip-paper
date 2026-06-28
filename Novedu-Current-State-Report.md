# Novedu MVP — Current State of Development

**Repository:** https://github.com/Teaching-HTL-Leonding/novedu-chat-mvp

This document describes the current state of the Novedu MVP as of 2026-06-28. It is the lead/reference implementation that Rainer Stropek built and maintains; he authored all 115 commits in this repository. A companion document covers the seed version handed over on 2026-06-10, and a separate student continuation line (the `application/` folder in `melanie-bauer/Novedu`) is described in its own companion documents. The codebase ~18 days and ~90 commits later than the seed is the subject of this report. Development is PR-based, with CODEOWNERS-enforced review and a Docker-building CI/CD pipeline, and is agent-driven (an `AGENTS.md` router plus `.agents/skills`).

---

## 1. Headline: From One Activity to a Small Platform of Activities

At the June 10 seed the app did essentially one thing: a teacher-configurable **tutor chat** delivered through a signed share link. As of 2026-06-28 it has grown into a **multi-activity learning platform** built on the same spine (Next.js 16, Mastra agents, CopilotKit/AG-UI, Entra ID, SCCH inference, Azure SQL). The unifying abstraction is now the **Code**: a teacher mints a short code pointing at an activity's YAML, hands out `https://<host>/<code>`, and the same gating/stats machinery serves whichever activity type the code names. Three activity types exist today (tutors, quizzes, writing), and a fourth, AI-assisted coding, is in active development (see §5).

Major additions since the seed (selected, with dates):

- **Tutor Codes** replacing signed share links, backed by Azure SQL + Drizzle ORM (2026-06-13), later generalized into a **module-neutral Codes subsystem** that backs every activity type (2026-06-23). Codes carry an availability window (both bounds nullable, allowing open-ended windows), a note, and a frozen anonymity flag; the stored row is the security boundary, re-checked on every request.
- **Tutor Code Stats**, anonymous and read-only conversation statistics for a code (2026-06-13), with anonymity enforced at the data layer (2026-06-14).
- **App-hosted YAML files** (`/files`): teachers author and version tutor/quiz/writing YAML **inside the app** (no GitHub needed), validated on save and served publicly at `/api/files/<name>` (2026-06-15 onward).
- **`novedu-tutor` CLI** (`@novedu/cli`, published to npm with provenance) that validates tutor/quiz/writing YAML from terminal/CI (2026-06-13 onward).
- **Quizzes**, LLM-graded open-ended quizzes (2026-06-21). See §3.
- **Image management**, app-hosted images plus content images on questions (2026-06-24).
- **Writing module**, a split-screen AI writing coach with teacher review (2026-06-24 to 26). See §4.
- **Telemetry** via Azure Monitor / Application Insights over OpenTelemetry, **carrying no message or PII content** (2026-06-15).
- **Authoring validation**, structural validation for quiz and writing YAMLs with authoring docs (2026-06-27).

Every activity keeps the project's founding idea: the activity is **defined by a teacher-authored YAML**, and the app is a configurable shell around it. Teachers need no code. If you can edit a structured text file, you can build a tutor, quiz, or writing activity.

---

## 2. Tutors (the original activity, now richer)

Tutors are unchanged in essence (YAML-defined persona/rules/model, Socratic by default, math and code rendering), with usability additions: a **welcome screen** showing the tutor's title and description, and **clickable example questions** to seed a conversation. Conversations are **anonymous by default** (a tutor can opt out with `anonymous: false` to attribute usage); memory is scoped per code. Teachers get aggregate, anonymous **stats** per code.

---

## 3. Quizzes — AI-Graded, Open-Ended (how they work)

A quiz is a YAML list of **open-ended questions**, deliberately with no multiple choice. For each question the teacher writes two parts:

- a **`question`**, the Markdown the student sees, and
- an **`evaluation`**, a **server-only grading prompt** that states the expected answer and a rubric mapping answers to one of three verdicts.

A student answers in their own words; an **LLM grader** scores the answer **correct / partial / incorrect** and returns written feedback shown immediately. After feedback, the student can open a short **per-question discussion chat** to talk through the answer. A few design properties:

- The grader is a **stateless agent** (no memory); quiz sessions are not recorded as chats, and the grading prompt **never reaches the browser**. The grader agent is also **not web-reachable** (only the server-side `submitAnswer` action can invoke it).
- Quizzes are **anonymous by default** (aggregate stats only) but can be set `anonymous: false` for graded work; questions can be **shuffled** and can carry a **content image**.
- The model returns a **structured verdict** (`{result, feedback}`) via the endpoint's OpenAI-compatible JSON-schema output.

In short, the quiz turns the same "configurable prompt" idea into **automated formative assessment with feedback**, where the teacher's rubric *is* the grader.

---

## 4. Writing — An AI Coach That Can't Write For You (how it works)

A writing activity is a **split screen**: the student writes Markdown in a left-hand editor; an AI **writing coach** sits on the right. The defining design choice:

- The coach can **read** the student's live draft (through a read-only `getCurrentText` tool) but is **structurally incapable of editing it**. It has no write tool, so even if its instructions asked it to rewrite, it physically cannot. It can only advise, ask guiding questions, and point at what to improve.
- The teacher configures the task (the `description` the student sees) and the coach's behaviour (a server-only `instructions` system prompt).
- When satisfied, the student presses **Save**, which stores **one text per (activity, student)**; a teacher can later **review** submissions (a "savers-first" review surface with display names).
- Because review and Save need to know whose text it is, writing **defaults to attributed** (`anonymous: false`), the deliberate divergence from tutors and quizzes; an anonymous writing code disables saving.

This makes writing a **process-oriented** activity (coaching the draft) rather than an answer-delivery one, directly echoing the survey's "guide, don't hand over the solution" finding.

---

## 5. AI-Assisted Coding — Current Development (not yet in `main`)

The next activity under development extends Novedu beyond the browser chat into students' **coding tools**. The plan: the app exposes an **OpenAI-compatible proxy** so students can point lightweight coding agents/CLIs (e.g. **pi**, **little-coder**) at the school's endpoint and code with AI assistance under the school's account. Crucially, **teachers can influence the system prompt** the proxy injects, for example tailoring the coding agent's answers to the students' level (more scaffolding for beginners, less hand-holding for advanced classes). This carries the teacher-configurable-pedagogy idea into the IDE/CLI, and matches the original `project_vision.md` goal that programming students "connect AI coding tools … to the LLM API provided by this project," with usage counted against budgets. This work is in progress and not present in the current `main`.

---

## 6. Infrastructure, Hosting & Compliance (SCCH)

- **Inference — SCCH GPU server.** All models are served by a **vLLM GPU server operated by SCCH (Software Competence Center Hagenberg)**, an external Austrian research centre, through its **LLM:2go** offering. The server exposes an **OpenAI-compatible API**, and the app talks to it server-side only (the API key never reaches the browser). Models in use are open-weight, e.g. `RedHatAI/gemma-4-31B-it-FP8-Dynamic`, GLM, and Qwen variants. The same endpoint backs tutors, the quiz grader/discussion, and the writing coach.
- **In-region, government-funded, cost-free to the school.** The GPU server is operated by SCCH (it is not owned or hosted by our school) and is **hosted in-region** in Austria. It is provided to us at no cost through a **government-funded research project**, so we incur **no inference cost** for using it.
- **Compliance posture.** Because inference runs on in-region infrastructure operated by an Austrian research centre rather than a commercial cloud LLM, it sidesteps the privacy concerns that motivated the whole project. It is positioned as free of the **GDPR / EU AI Act** problems of the kind that rule out sending student data to external SaaS LLMs. This realizes the vision document's "European/self-hostable, open components, store only what's necessary" requirements.
- **Identity & data.** Sign-in is **Microsoft Entra ID** (school SSO); teacher-only actions are gated by Entra group membership. App data (codes, users, saved writings, Mastra memory) lives in **Azure SQL**, with **passwordless Entra/Managed Identity in production** (no SQL password). **Privacy by default:** activities are anonymous unless a teacher opts out, the quiz grader records nothing, and **telemetry carries no message or PII content**.

---

## 7. Limitations (it is still a learning prototype)

Stropek wants to state these plainly. The value here is the approach and the learning, not a finished product:

- **Unpolished UI.** The interface is functional, not designed; usability and visual polish are not yet a focus.
- **Not smartphone-ready.** The app is desktop-oriented and **not yet usable on phones**, a real gap since smartphones are a primary platform for students.
- **Single-tenant, Leonding-only.** It is built for **HTBLA Leonding specifically**; there is **no multi-tenancy and no large-scale configurability** (e.g. per-school provisioning, org-level administration). Configuration is by editing YAML and environment, not through an admin console.
- **Still a prototype for learning.** This is an authentic software-engineering learning vehicle and an experimental classroom tool, not a production platform. Several institutional features Stropek envisioned in the design phase (admin dashboards, budgets, user/group management) remain deliberately out of scope.

---

## 8. Key Dates (this development phase)

| Date | Milestone |
|---|---|
| 2026-06-10 | Seed handed over (baseline) |
| 2026-06-13 | Tutor Codes (Azure SQL + Drizzle); `novedu-tutor` CLI; code stats |
| 2026-06-15 | App-hosted YAML files; OpenTelemetry/App Insights telemetry |
| 2026-06-21 | **Quizzes** (LLM-graded open-ended, with discussions) |
| 2026-06-23 | Codes generalized to a module-neutral subsystem (tutor/quiz/writing) |
| 2026-06-24 | Image management; **Writing module** introduced |
| 2026-06-25/26 | Shared ModuleChat primitive; writing teacher-review; display names |
| 2026-06-27 | Structural validation for quiz & writing YAMLs + authoring docs |
| 2026-06-28 | Open-ended (nullable) code availability windows |
| (in progress) | **AI-assisted coding** via OpenAI-compatible proxy (pi / little-coder) |
