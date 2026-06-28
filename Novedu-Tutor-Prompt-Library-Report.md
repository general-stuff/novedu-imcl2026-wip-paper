# Novedu Tutor Prompt Library — Cross-Subject Teacher Collaboration

This document describes our cross-disciplinary, teacher-authored tutor prompt library, the
content layer that feeds the Novedu platform. The library lives in a version-controlled
repository, *ai-as-tutor-prompts* (*"KI-Tutoren Prompt-Bibliothek"*), which at the time of
writing holds 34 commits spanning 2025-12-15 to 2026-06-14, contributed by a group of teachers
(see Section 4).

It is important to be honest about the state of this work: it is **explicitly unfinished.** The
build script (`build.py`) is not yet implemented, structure and conventions are still in flux,
and the content is a mix of polished and draft prompts. In our project this is the content
layer, and Rainer Stropek sees the library less as a finished artefact than as a visible record
of teachers from different fields collaborating to design AI tutors for general-education
subjects (*Allgemeinbildung*).

This document is a companion to the other materials accompanying the paper: the repository
history, the chat MVP seed, the Open WebUI to MVP transition, the teacher survey, the
literature review, the current MVP state, and the evaluation harness. Where those describe the
engineering and research work, this one covers the human and pedagogical content layer: the
library of tutor personas and didactic building blocks that the teachers author and that
ultimately feed the platform's tutors.

---

## 1. What It Is

A shared, version-controlled **library of system prompts for AI tutors**, written *by teachers,
for teachers*. The stated aim, as set out in the README, is that a teacher can set up a tutor
for their class **without having to write a prompt themselves.** It was originally intended for
use in **OpenWebUI workspaces** (with Azure-hosted models like Claude Opus or GPT-5.5), so it
began in the Open WebUI era and was later realigned toward the Novedu YAML composer (Section 3).

**The core idea, in one sentence.** Separate **ROLLE** (how the tutor behaves pedagogically,
reusable across subjects) from **STOFF** (what it talks about, swappable per topic or exam), and
compose the two, together with shared **Bausteine** (building blocks), into a finished prompt.
This is the configuration-over-training, reusable-modules thesis of the whole project, expressed
at the level teachers actually work at: editing text files.

---

## 2. How the Library Is Organized

The library uses a deliberately simple, file-based structure that mirrors the "assemble from
modules" model:

- **`_bausteine/`** — reusable snippets included via a `{{BAUSTEIN:name}}` placeholder, split
  into `didaktik/` (pedagogical rules such as Socratic "no solutions", error analysis, and
  language level by grade) and `format/` (output format, e.g. Markdown plus LaTeX for math).
  These are written **subject- and topic-neutral** so they slot into any tutor and act as the
  single source of truth for a policy like "never hand over the solution."
- **`<fach>/`** — one folder per subject, each with a uniform `rollen/` (tested pedagogy prompts
  with placeholders), `stoff/` (swappable per-topic content), and `tests/` (standard test
  questions to validate a role).
- **`_vorlagen/`** — copy templates and a **metadata schema** (YAML frontmatter: `titel`, `fach`,
  `typ`, `modell_getestet`, `autor`) that each role file must carry.
- **`prompts/`** — the generated, ready-to-paste prompts per subject.
- **`yamltemplate/`** — a VS Code-configured template for authoring **novedu-chat YAML** tutor
  and fragment files, the bridge to the platform (see Section 3).

**How a finished prompt is built:** pick a *role* (e.g. `mathematik/rollen/nachhilfelehrer.md`,
which contains placeholders like `{{BAUSTEIN:sokratisch-keine-loesungen}}` and `{{STOFF}}`),
optionally pick a *Stoff* file, and a build step resolves the placeholders into a clean prompt
under `prompts/`. The `build.py` is still to be implemented; for now placeholders are resolved
by hand, which is one marker of the work's unfinished state. Authoring rules, the test workflow
(does it *hint* instead of *solving*?), and contribution conventions are written up in
`CONTRIBUTING.md`.

A concrete example of the modularity: the math *Nachhilfelehrer* role is almost entirely
composed of building blocks, `{{BAUSTEIN:sokratisch-keine-loesungen}}` +
`{{BAUSTEIN:fehleranalyse}}` + `{{BAUSTEIN:sprachniveau-jahrgang}}` +
`{{BAUSTEIN:markdown-mathe-latex}}`, with only the subject-specific edge cases written inline.
The Socratic building block itself is an explicit, reusable pedagogy policy ("Ask instead of
telling," "one hint per response," "no full solution, even under pressure," "NEVER GIVE AWAY THE
SOLUTION"), exactly the Socratic-tutor behaviour and instructional guidelines module that the
Novedu abstract names.

---

## 3. Relationship to the Platform (the bridge)

This library is the **content and authoring counterpart** to the engineering work described in
the other documents:

- The **ROLLE / STOFF / BAUSTEIN** split is the human-authored mirror of Novedu's **tutor plus
  reusable fragment** YAML model. The idea is the same, externalizing pedagogy into editable,
  reusable modules, expressed once for OpenWebUI/Markdown and once for the Novedu composer.
- On **2026-06-10** a "new structure" commit reorganized the repo into the `rollen/stoff/tests`
  plus `_bausteine` layout **and** added the `yamltemplate/` for the **novedu-chat YAML
  composer**. In other words, the library was realigned to feed the MVP just as the MVP was being
  seeded (also around 2026-06-08 to 06-10). The math YAML tutors here
  (`nachhilfelehrer-tutor.yaml`, `beispiel-creator-tutor.yaml`,
  `pruefungsvorbereitung-tutor.yaml`) are the **same tutors** that ship as samples in the
  chat MVP under `tutors/akornfellner/mathematik/`, direct evidence of content flowing from this
  teacher library into the platform.
- It is also the **source of substance** for the evaluation harness: the roles and didactic
  building blocks defined here are what SocraticTutorEval scores for model and prompt quality.

In short: the survey defines what tutors should do; this library holds the tutors' actual
pedagogy, authored by subject teachers; the MVP delivers them; and the eval harness checks
whether they work.

---

## 4. Authorship — Genuinely Cross-Disciplinary, Teacher-Driven

Unlike the other repositories in the project, where Stropek usually holds most of the commit
history, this one is committed by a **broad group of teachers**, which is precisely its point: it
documents collaboration, not a single author. Contributors and their subjects:

| Contributor | Commits | Subject area (Allgemeinbildung & more) |
|---|---|---|
| **Peter Bauer** (initiator; Head of CS Dept / product owner) | 10 | Repo setup, `Grundlegendes` basics, English baseline prompt |
| Katharina Povacz | 7 | Biomedicine / nutrition–metabolism (BMG: *Methabobuddy*, *Stammbaumanalyse*) |
| Alexander Kornfellner | 6 | Mathematics (Nachhilfelehrer, exam-prep *SA_Helper*, the 2026-06-10 restructure) |
| Katharina Gallner-Holzmann | 3 | German (*Stilmitteltrainer*, *Beistrichtrainer*) |
| Phil Mihl | 3 | Mathematics (upper-secondary math tutor) |
| Peter Frey | 2 | Programming/POSE (C-programming tutor, English variants; VS Code YAML setup) |
| Christina Kodre | 1 | English (future tenses) |
| Rainer Stropek | 2 | Titles, a tutor draft (a supporting role here) |

This spread, covering German, English, Mathematics, Biomedicine, and Programming, matches the
same cross-disciplinary make-up as our survey design panel and the May 26 review, and it is led
by the product owner, **Peter Bauer**, who initiated this repository on 2025-12-15. Stropek sees
this as the clearest single artefact showing that Novedu's pedagogy is being shaped by **teachers
across faculties**, not just the CS and development team.

---

## 5. Timeline (and how it interlocks)

| Date | Event | Concurrent with |
|---|---|---|
| 2025-12-15 | **Peter Bauer initiates** the library; `Grundlegendes` basics + README | Open WebUI + LiteLLM prototype era; tech-evaluation/design phase |
| 2026-02-17 | German tutors added (Gallner-Holzmann): Stilmitteltrainer, Beistrichtrainer | Survey design; ERD/requirements work |
| 2026-03-05 → 03-19 | Math (Kornfellner, Mihl), Biomedicine (Povacz: *Methabobuddy*), English (Bauer) | UX click-dummy build; survey fielding |
| 2026-04-30 | Math exam-prep helper (Kornfellner, *SA_Helper_5CHIF*) | Same day as the eval harness foundation and survey analysis prep |
| 2026-06-10 | **"New structure"** — `rollen/stoff/tests` + `_bausteine` + `yamltemplate` (novedu-chat YAML) | The MVP seed days (06-08…10); the library realigns to the platform |
| 2026-06-11 → 06-14 | YAML tutors (Povacz, Frey C-programming, Stropek drafts) | Early MVP feature development |

The interlock is telling: the library **predates the MVP by six months**, a teacher-content
effort running since December 2025, and at the **moment the MVP architecture crystallized in
June (the 8th to the 10th), the library was restructured to feed it** via the YAML composer. It
is the long-running, teacher-owned content stream that the platform was ultimately built to
serve.

---

## 6. Why the Library Matters to the Project

- **It is concrete proof of our central thesis.** Our argument is that tutors should be
  assembled from reusable pedagogical modules authored by teachers. This repository **is** that,
  in practice: real Socratic, error-analysis, and language-level building blocks, reused across
  real subject tutors, written by real subject teachers.
- **It shows institutional, cross-faculty buy-in.** Led by the Head of the CS Department and
  populated by German, English, Math, Biomedicine, and Programming teachers, it makes our claim
  of being institutionally embedded visible.
- **It is an honest work in progress.** The unfinished build tooling and draft prompts are worth
  reporting precisely because they show an authentic, ongoing collaborative process rather than a
  polished demo, which matches the work-in-progress framing of the project as a whole.
