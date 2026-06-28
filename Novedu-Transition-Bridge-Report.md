# From Open WebUI to a Teacher-Configurable MVP — The Transition

This document narrates the decision history that connects the two architectural eras of
Novedu: how our school moved from exploratory use of generic AI tools toward an
institutionally embedded, teacher-configurable tutoring platform. It is the bigger picture
that the conference paper is fundamentally about.

It is meant to be read alongside two companion documents:

- The repository history and architecture of the `melanie-bauer/Novedu` repository, covering
  the experiment, the design phase, and the student-built MVP harness.
- The teacher-authored chat-MVP seed (`Teaching-HTL-Leonding/novedu-chat-mvp`) that the
  current architecture is built on.

This document ties them together: it explains why the project moved from one era to the next,
who drove each decision, and what was kept, cut, or deferred.

---

## 1. The People and Their Roles

The transition only makes sense once the roles are clear:

- **Peter Bauer — Head of the Computer Science Department** (*Abteilungsvorstand für
  Informatik*) at HTBLA Leonding. He acted as the **main product owner** and the senior
  school stakeholder steering the design work: he guided the students as they turned the
  Open WebUI experience into a feature set and a UX prototype.
- **Rainer Stropek — teacher and technical lead.** Stropek set the original project vision
  in October 2025, supervised the cooperative development, and later personally authored the
  MVP seed architecture.
- **The student team** — Melanie Bauer, Lukas Larndorfer, Konstantin Wetscher, André
  Wildberger (4th grade, HTBLA Leonding). They built the Open WebUI experiment, produced the
  requirements and the UX click-dummy, and then continued the MVP.
- **The teacher panel** — a deliberately diverse review audience drawn from *Allgemeinbildung*
  (general-education subjects), *STEAM*, and *Computer Science*. They were the evaluators at
  the decisive May 26 presentation.

The recurring pattern across the whole project: **stakeholders set direction (Stropek provided
the vision, Bauer the product), students do the building.**

---

## 2. The Transition in Five Moves

### Move 1 — The Open WebUI + LiteLLM experiment (Dec 2025 → early 2026)
The students stood up a working tutoring environment by composing existing open-source
products: **Open WebUI** as the chat front end, **LiteLLM** as the model gateway, with
Microsoft Entra ID SSO, OAuth group mapping, per-user budgets, and usage tracking. This was
never the end goal. It was a way to *gain lived experience*: what teachers and students
actually need, where a generic chatbot UI falls short pedagogically, and which institutional
concerns (privacy, budgets, class oversight) matter in practice.

### Move 2 — The design task: features + a UX prototype, owned by the product owner (Jan → May 2026)
On the strength of that experience, the students were given a concrete design brief:
**define the feature set and design a UX prototype for a purpose-built platform**, guided by
product owner **Peter Bauer**. This produced two complementary artifacts:

- **A requirements specification** — `docs/requirements/requirements_analysis.adoc`
  (*Anforderungsbeschreibung Novedu*, dated 2026-01-27): a comprehensive German-language
  feature catalogue covering login/roles/classes, the chat surface, files and multimodality,
  tutor configuration, budgets, admin workflows, Entra synchronisation, and more. Paired with
  the data model work (the ERD, issue #28).
- **A UX click-dummy** — the React + Vite + shadcn/ui single-page app in `frontend/`
  (issue #29, 2026-02-22 → 2026-05-26). It is a pure mock-data prototype (one `mockData.ts`,
  **no backend calls**) but it renders the *entire* envisioned product: student chat, tutor
  editor/overview, and a full admin suite (dashboard, user/group/provider management, budget
  and cost dashboards, global prompt).

This is the high-water mark of *scope*: the click-dummy shows the maximal vision of Novedu as
a complete institutional platform.

**Running underneath all of this — the school-wide survey.** In parallel (designed from
2026-03, fielded 2026-04-16 → 05-12, analyzed 2026-05-27), a teacher colleague and Stropek ran a
299-respondent student/teacher survey to ground the requirements in evidence. Its findings,
curricular grounding as the #1 wish, configurable pedagogy modes, factual correctness as the
shared red line, and privacy-as-aggregated-insight, both validated the configurable-tutor
direction and, landing the day after the May 26 presentation, fed the same scope-cutting
decision.

### Move 3 — The May 26 presentation and its verdict (2026-05-26)
On **26 May 2026** the students presented the click-dummy to the **diverse teacher panel**
(*Allgemeinbildung*, STEAM, Computer Science). The reception was **positive, but the panel
judged the scope too large.** A full institutional platform (admin consoles, budgeting,
cost dashboards, provider management) was more than what was needed to start putting AI
tutors in front of students.

The commit record corroborates the date precisely: May 26 is the **last day the `frontend/`
click-dummy was ever touched**, with a tight morning burst of finishing commits (07:15–08:25)
and a final cleanup at 16:02, after which the prototype was frozen for good.

### Move 4 — The decision: cut scope to a classroom-ready MVP (late May → early Jun 2026)
The panel's feedback set the next task for Stropek and the students together:
**boil the broad prototype down to a minimal viable product** that could be **used in real
classroom experiments much faster.** The guiding question shifted from "what should the full
platform contain?" to "what is the smallest thing a teacher can actually put in front of a
class next week?"

The answer that survived the cut was the project's irreducible core: **a teacher-configurable
tutor, delivered as a chat, that a student can open and use**, with everything institutional
(admin, budgets, dashboards, provider management) deferred.

### Move 5 — The MVP seed and handover (2026-06-08 → 2026-06-10)
Stropek translated that reduced scope into a new architecture in a **workshop with the
students on 8 June 2026**, then hardened it over the next two days and **handed the code to
the students on 10 June** to continue. The MVP is a clean-slate rewrite, neither Open WebUI
nor the click-dummy's codebase, built on Next.js + Mastra + CoPilotKit + AG-UI, with the
**modular YAML tutor model** as its heart. From here the story rejoins the repository history,
where the student commits resume on 11 June.

---

## 3. What Was Kept, Cut, and Deferred

The MVP is best understood as a deliberate *subset* of the May 26 vision. The table makes the
scope reduction concrete:

| Capability (in the click-dummy / requirements) | In the MVP seed? | Notes |
|---|---|---|
| Teacher-configurable tutors | **Kept** (reimagined) | Now modular **YAML tutor + fragment** files, authorable by non-programmers |
| Student chat with math/code rendering | **Kept** | CoPilotKit chat, KaTeX + syntax highlighting |
| Microsoft Entra ID sign-in | **Kept** | Auth.js v5; teacher role via Entra group; student mode |
| Tutor distribution to students | **Kept** (simplified) | Signed, time-boxed **share links** instead of full class management |
| Admin dashboard | **Deferred** | Present in click-dummy, absent in MVP |
| User / group / class management | **Deferred** | Designed in requirements + ERD, not built in MVP |
| Provider / model management UI | **Deferred** | MVP resolves models from the tutor YAML + a fixed SCCH endpoint |
| Budget & cost dashboards | **Deferred** | A core Open WebUI/LiteLLM strength, intentionally out of MVP scope |
| Per-school / multi-tenant data model | **Deferred** | Full ERD designed (issue #28); MVP implements only a thin slice |

**Carried-through principles** (constant across all eras): pedagogical alignment over generic
answers, privacy and European/self-hostable hosting, teacher oversight, and open-source
components. The scope shrank; the values did not.

---

## 4. Why the Transition Matters

The story we are telling is the *transition itself*, from generic chatbots to a
teacher-configurable platform. The causal spine has four parts:

1. **Experience first.** The Open WebUI prototype was an instrument for learning requirements,
   not a throwaway. Its evaluation (acceptance, usefulness, pedagogical fit) is what justified
   building something custom.
2. **Stakeholder-governed design.** A senior product owner (the Head of the CS Department)
   and a cross-disciplinary teacher panel, not just the developers, shaped the product. This
   is the "institutionally embedded" character of the project made concrete.
3. **Scope discipline as the pivotal decision.** The single most consequential moment was not
   a technology choice but a **scoping choice**: a well-received but oversized vision was cut
   to an MVP so that *real classroom use could begin quickly*. This is the heart of moving
   from exploratory use toward an embedded platform.
4. **Authentic software engineering.** The arc, experiment → requirements → UX prototype →
   stakeholder review → MVP, mirrors professional product development, which is itself part
   of the educational value of the project.

---

## 5. Consolidated Timeline (the bridge in one view)

| Date | Move | Event |
|---|---|---|
| 2025-10-06/07 | — | Stropek authors the project vision |
| 2025-12 → 2026-02 | Move 1 | Open WebUI + LiteLLM experiment in real use |
| 2026-01-27 | Move 2 | Requirements specification (*Anforderungsbeschreibung*) finalized |
| 2026-02-07 → 05-10 | Move 2 | ERD / data-model design (issue #28) |
| 2026-02-22 → 05-26 | Move 2 | UX click-dummy built (React/Vite/shadcn, issue #29), guided by product owner Peter Bauer |
| 2026-05-26 | Move 3 | Students present the prototype to the *Allgemeinbildung / STEAM / CS* teacher panel — well received, scope judged too large |
| late May → early Jun | Move 4 | Decision (Stropek + students): cut to a classroom-ready MVP |
| 2026-06-08 | Move 5 | MVP architecture worked out in a workshop (Stropek + students) |
| 2026-06-10 | Move 5 | Stropek hands the MVP seed to the students; they continue |

---

## 6. A Note on Sources

Some details deserve a word on provenance. Peter Bauer's title is rendered here as "Head of
the Computer Science Department" for *Abteilungsvorstand für Informatik*. The May 26
presentation and Mr. Bauer's product-owner role are external events not recorded in the
repository; Stropek describes them from his own account rather than from any commit. The MVP's
current status beyond the 2026-06-10 seed is the subject of a forthcoming discussion and is
intentionally not detailed here.
