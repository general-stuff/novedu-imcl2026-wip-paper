# Novedu School Survey

**Repository:** https://github.com/general-stuff/ai-tutor-questionaire
**Survey design and analysis dates:** questionnaire design and survey web app developed 2026-03-05 → 2026-05-26; analysis report dated 2026-05-27.

This document describes the school-wide requirements survey of students and teachers that ran in parallel with the entire Novedu development effort and supplied the empirical requirements evidence. It covers the survey's design, methodology, and web app, and it summarizes the 2026-05-27 analysis. The raw response database is to be released later for the paper's results chapter; this document describes the survey itself and the existing analysis, and is not itself the results chapter. For the related platform history, see the companion repository and prototype documents.

---

## 1. What the Survey Is

A two-variant questionnaire (one for **students**, one for **teachers**) run at HTBLA Leonding to elicit requirements, expectations, perceived benefits, concerns, and functional needs for an AI tutor. It is the empirical backbone of the requirements-driven design-and-evaluation approach behind Novedu: where the earlier Open WebUI experiment gave *lived* experience and the click-dummy gave a *design hypothesis*, the survey provides *quantitative and qualitative evidence* from the whole school population.

The repository contains four things:

1. **The questionnaire design** — eight thematic cluster files (`00_einleitung.md` … `08_cluster_rahmenbedingungen.md`) plus a consolidated `fragebogen.md`, documenting every question with its exact wording, answer type, options, and an internal rationale.
2. **A purpose-built survey web app** (`questionaire/`) that administered the questionnaire.
3. **An analysis pipeline** (`.analysis/`) — scripts and exported data (NDJSON/JSON) that turned database answers into aggregates.
4. **The analysis report** — `2026-05-27-report.md` / `.pdf`, a thorough write-up of results.

---

## 2. Authorship: A Cross-Disciplinary Teacher Panel

The survey is a teacher-led workstream, distinct from the student-built platform development. The questionnaire was not authored by one person but designed by a diverse panel of teachers across the same three faculties later represented at the May 26 review: general-education subjects (*Allgemeinbildung*), STEAM, and Computer Science. The panel included Rainer Stropek, Andreas Probst, and Peter Bauer (the project's product owner and Head of the Computer Science Department). We worked out the questions together over several collaborative design sessions. The cross-disciplinary make-up was deliberate, so the instrument would be meaningful beyond just computer-science classes.

The git committers are not the full author list. The version history records only the two people who edited files directly; the broader panel's input arrived through the design sessions and informal collection (see §3) rather than as commits. The recorded commits run 2026-03-05 → 2026-05-26:

| Committer | Role | Commits | Recorded contribution |
|---|---|---|---|
| **Rainer Stropek** | Teacher / project lead | ~18 | Survey app (built with AI support), survey logic, Likert redesign, analysis, report |
| **Andreas Probst** (`Andreas.Probst@eduhi.at`) | Teacher (panel member) | 6 | Edited cluster content (demographics, features, intro) |

Peter Bauer and other panel members contributed through the design sessions, not via commits.

Where this sits relative to the rest of the project:

- **2026-03-05** — Stropek committed the questionnaire draft and a first survey-app prototype. This fell during the custom-platform design phase and overlapped the late Open WebUI prototype era.
- **2026-03-08** — Andreas Probst refined cluster wording, a second teacher's input.
- **2026-03-23** — Major questionnaire revision: Likert scales were unified to a 4-point format (no neutral midpoint, which is important for interpreting every mean later), professional-context options were reworked, and "Novedu" was added to the tools list in the teacher questionnaire, so the survey itself name-checks the platform being built.
- **2026-04-16 → 2026-05-12** — Field period: responses collected, largely in supervised in-class sessions (about 60 % of answers fell on four days: 04–06 May and 12 May).
- **2026-04-30** — Content tweaks during the field period (for example, renaming "Programmieren/POSE" to "Programmierunterricht", adding a "KI-Tutor explanation" section).
- **2026-05-26** — Final repository activity. The analysis is dated 2026-05-27, the day after the click-dummy was presented to the teacher panel. The survey results and the prototype feedback thus landed together, both feeding the late-May/early-June decision to cut scope to an MVP.

So the survey ran alongside the entire arc: designed during the Open WebUI era, fielded during the design phase, and analyzed exactly as the project pivoted to the MVP.

---

## 3. Methodology: Making Markdown "Executable"

The survey is itself a small case study of the project's modern, AI-assisted way of working, because the *method* mirrors the platform's philosophy of human-authored intent and AI-assisted execution. The workflow had five stages:

1. **Informal collection.** We gathered rough questions informally across the teacher panel via Excel and email (the repo still carries `FragebogenFunktionalität.xlsx`, an early functionality/question spreadsheet, from 2026-03-05). No tooling yet, just capturing ideas from every faculty.
2. **AI-assisted phrasing into Markdown.** Those rough inputs were turned into precise question wording with AI support, targeting a structured Markdown representation: the per-cluster files (`0X_cluster_*.md`) that document each question's exact wording, answer type, options, and internal rationale. Markdown is the human-readable source of truth.
3. **Manual rework.** We manually refined the Markdown until the questions were right (for example, the 2026-03-23 revision unifying Likert scales to 4 points, reworking option sets, and resolving redundancies). Humans stay in control of the content; AI accelerates but does not decide.
4. **Harness engineering ("vibe coding"): Markdown becomes a website.** Rather than feeding the questions into an off-the-shelf survey tool, Stropek turned the Markdown into a custom questionnaire website (built with AI coding agents) that renders the questions, gates access behind school SSO, persists answers, and ships built-in results (charts and tables; see the `recharts` dashboard and the `.analysis/` pipeline). The Markdown is, in effect, made executable.
5. **Iterative fine-tuning with a coding agent.** As the panel kept refining questions, Stropek regenerated and updated the website with the coding agent to match, across multiple iterations where a change to the Markdown intent propagated into the live app, keeping source and instrument in sync.

The core idea is "executable Markdown." The questionnaire's canonical form is a set of human-authored Markdown files; the running website is a generated artifact over that source. This yields three concrete advantages:

- **Better UI/UX** than a generic survey tool: purpose-built rendering, branching, and a results dashboard tailored to the questions.
- **No dependency on an external SaaS:** the school owns the app, the data, and the hosting (a recurring requirement throughout the project: privacy, European/self-hostable).
- **Authentication integrated into the school's identity/SSO** (Microsoft Entra ID), so responses are tied to authenticated students and teachers and routed to the right variant, with no third-party accounts.

This is the same build philosophy and roughly the same stack Stropek used days later for the chat-MVP seed: AI-assisted, harness-driven engineering producing a self-hosted, SSO-gated Next.js app, making the survey both a requirements instrument and a methodological dry run for the MVP.

---

## 4. Questionnaire Design

The questionnaire is organized into **8 thematic clusters**, each issued in a student (`S`) and teacher (`L`) variant, with a stable ID scheme `[S/L][cluster].[number]` (for example, `S3.2`):

| Cluster | Theme |
|---|---|
| 0 | Demographics & context |
| 1 | Current AI usage (status quo) |
| 2 | Willingness to use & deployment scenarios |
| 3 | Functional requirements & features |
| 4 | Learning effectiveness & pedagogical value |
| 5 | Risks, misuse & fairness |
| 6 | Data protection, transparency & trust |
| 7 | Framework conditions, future & open questions |

Design choices documented in `00_einleitung.md`: about 35 questions per variant (target fill time 10–15 min); 4-point Likert throughout (deliberately no neutral midpoint, theoretical mean 2.5); ranking questions capped at "pick top 3"; students addressed informally (*du*), teachers formally (*Sie*); each question carries an internal rationale not shown to respondents; and an explicit redundancy check resolving overlaps (for example, status-quo versus intended use). The framing in the intro already describes the target system as teacher-built "Workspaces" (lessons) with custom materials and system prompts, the configurable-tutor concept the platform implements.

---

## 5. Survey App Architecture

A small, purpose-built Next.js app, built on the same stack Stropek later used for the chat-MVP seed, which makes the survey app an architectural precursor:

- **Next.js 16** (App Router), **React 19**, TypeScript, Tailwind 4.
- **Auth.js / NextAuth v5 (beta)** with **Microsoft Entra ID** sign-in (`auth.ts` + `proxy.ts`, the same Next-16 `proxy` middleware convention as the chat-MVP). Role (`is_teacher`) and class are derived from the signed-in identity, which routes each user to the student or teacher variant.
- **Azure SQL** via `mssql`, with a minimal schema (see §6).
- **Recharts** for the results/admin dashboard.
- **Docker** image published to Docker Hub (`rstropek/ai-tutor-questionaire`).

Answers are saved incrementally after each section (hence a mild ~6 % drop-off from early to late questions, and "registered but no answer" users).

---

## 6. Headline Findings (from the 2026-05-27 analysis)

Full detail is in `2026-05-27-report.md`; the database for re-analysis comes later. Summary:

- **Sample:** 299 saved questionnaires, comprising 265 students and 34 teachers (317 registered, 18 without answers). This is an HTL population with a strong informatics bias (73 % of students in the IT branch); results are technology-leaning and not 1:1 generalizable to a general-education school.
- **#1 product signal: curricular grounding.** The single strongest, most consistent wish (students S3.4 mean **3.53**, the highest of any student Likert item; 27 % of free-text) is that the tutor must know *their* concrete subject matter / curriculum / the teacher's materials. This points technically toward RAG over Moodle and teaching materials (one student literally asks for "an MCP for Moodle").
- **Pedagogy vs. convenience tension.** In closed questions, students favor guided, step-by-step help over ready-made solutions (S3.3, S4.1, S4.3); in free text many simultaneously demand fast answers and no limits. This is resolved by configurable pedagogy modes, which match teachers' wish for per-Workspace configurability (L3.3, L4.4).
- **Correctness is the shared red line.** Hallucinations are students' top "no-go" (24 %) and teachers' top requirement (85 % rate factual correctness most important), yet only 36 % of students check answers regularly. Factual fidelity, visible sources, and an honest "I don't know" form an *acceptance* criterion, not a comfort feature.
- **Cost/limits as a standalone value driver.** 77 % of students have no paid account; free-tool limits are a major frustration, so a school-provided, unlimited tool has value on its own.
- **Teachers willing but unprepared.** 100 % would use the tutor at least occasionally, but feel pedagogically and legally under-prepared (only 6 % "well informed" on the legal side) and want training. Willingness to build Workspaces is high but conditional on low effort and templates (creation under 15 min).
- **Privacy expectations are compatible when done right.** Students mostly dislike teachers reading along (56 % uncomfortable even when anonymized); teachers mainly want aggregated statistics (76 %), not individual chats (6 %). The product direction is aggregated, anonymous class insights, not surveillance.

These findings directly validate the platform's core bets (teacher-configurable tutors, curricular grounding, configurable Socratic behavior, factual correctness, privacy-by-design) and reinforce the MVP scoping decision.

---

## 7. Database Schema (for the forthcoming results chapter)

The Azure SQL schema (`questionaire/migrations/001_initial.sql`) is deliberately minimal:

```sql
users(   oid PK, name, class_name NULL, is_teacher BIT, last_sign_in )
answers( user_oid PK/FK→users.oid, answers JSON, updated_at )
```

All responses live as a JSON blob per user in `answers.answers`, keyed by question ID (`S0.1`, `L3.2`, …); `users` carries role and class. Question metadata and labels for joining are in `.analysis/questions.json`, and the existing flattened exports are in `.analysis/` (`data.ndjson`, `analysis.json`, `readable.txt`) with the aggregation scripts (`analyze.cjs`, `themes.cjs`, `format.cjs`). When the live database is provided, these are the starting points for the results chapter; note that teachers have empty `class_name` by design.

A few caveats matter when reporting from this data. The 4-point Likert scale (1–4, no neutral midpoint, 4 = strongest) should be stated whenever means are reported, since there is no "5". The sample is informatics-heavy (HTL), which limits external validity, and the n=34 teacher percentages should be read as small-sample tendencies (roughly 3 people per percentage point).

---

## 8. Key Dates

| Date | Event |
|---|---|
| 2026-03-05 | Questionnaire draft + survey-app prototype (Stropek) |
| 2026-03-08 | Cluster content edits (Andreas Probst, 2nd teacher) |
| 2026-03-23 | Likert scales unified to 4-point; "Novedu" added to teacher tool list |
| 2026-04-16 → 05-12 | Field period — responses collected (mostly supervised class sessions) |
| 2026-05-26 | Final repo activity; click-dummy presented to teacher panel |
| 2026-05-27 | Analysis report produced (299 responses: 265 students, 34 teachers) |
