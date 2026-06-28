# Novedu: IMCL 2026 Work-in-Progress Paper

This repository contains the materials for our work-in-progress paper submitted to
[IMCL 2026](https://imcl-conference.org/current/):

> **Work-in-Progress: From Generic Chatbots to Teacher-Configurable AI Tutors: The Design and
> Evaluation of Novedu in Secondary Education**
> Rainer Stropek and Andreas Probst, HTBLA Leonding (Austria)

*Novedu* is a teacher-configurable AI-tutoring platform developed at HTBLA Leonding as a
cooperative student software-engineering project. Its central idea is that a school can align
large-language-model tutors **institutionally** (through teacher-owned configuration, curricular
grounding, in-region inference, privacy defaults, and evaluation) rather than by training models.

Besides the paper itself, the repository collects the **detailed background documents** that the
paper draws on: the project history, the requirements survey and its results, the evaluation
harness, and the literature positioning. These are meant as a reference for readers who want more
detail than the 8-page paper can hold.

## Repository structure

```
.
├── wip-paper/                          The paper (LaTeX source + compiled PDF)
│   ├── novedu-wip.tex                  Main LaTeX source (Springer LNCS class)
│   ├── references.bib                  Bibliography
│   ├── novedu-wip.pdf                  Compiled paper
│   ├── llncs.cls, splncs03.bst, …      LNCS class files needed to build
│   └── .gitignore
│
├── Novedu-Structured-Abstract-Stropek.pdf   The accepted structured abstract
│
├── Novedu-*-Report.md                  Background reference documents (see below)
│
├── 2026-06-28-results/                 Raw survey data export (questionnaire results)
│   ├── README.md                       Describes the export format
│   ├── responses.json                  Primary source (per-respondent answers)
│   ├── questions.json                  Codebook (question texts, types, scales)
│   └── responses_{students,teachers}.csv
│
├── llncs2e/                            Original LNCS package from the organizers
│                                       (reference only; not needed for the build; gitignored)
└── README.md                           This file
```

## The paper

The source is in [`wip-paper/`](wip-paper/). To build the PDF you need only the files in that
folder (the top-level `llncs2e/` package is **not** required, as `wip-paper/` already contains its
own copies of the LNCS class files).

```bash
cd wip-paper
pdflatex novedu-wip
bibtex   novedu-wip
pdflatex novedu-wip
pdflatex novedu-wip
```

## Background reference documents

The nine Markdown reports give the full detail behind the paper. Roughly in the order the paper
unfolds:

| Document | What it covers |
|---|---|
| [`Novedu-Transition-Bridge-Report.md`](Novedu-Transition-Bridge-Report.md) | The decision history: how the project moved from an exploratory Open WebUI / LiteLLM experiment, through a stakeholder-reviewed UX prototype, to the scope-reduced classroom MVP. The "why it looks the way it does" narrative. |
| [`Novedu-Repository-Report.md`](Novedu-Repository-Report.md) | Full development history and architecture of the main, student-built Novedu repository: phases with dates, contributors, and the four architectural transitions. |
| [`Novedu-Chat-MVP-Prototype-Report.md`](Novedu-Chat-MVP-Prototype-Report.md) | The teacher-authored MVP *seed*: the clean-slate Next.js + Mastra + CopilotKit architecture and the modular YAML tutor model that all current development builds on. |
| [`Novedu-Current-State-Report.md`](Novedu-Current-State-Report.md) | The current state of the platform: how it grew from a single tutor chat into a multi-activity platform (tutors, AI-graded quizzes, a writing coach, an AI-assisted coding proxy) on one shared "Code" abstraction, plus the SCCH hosting and privacy posture. |
| [`Novedu-Survey-Report.md`](Novedu-Survey-Report.md) | The school-wide requirements survey: instrument design, methodology, and the "executable Markdown" way of working. |
| [`Novedu-Survey-Results-Report.md`](Novedu-Survey-Results-Report.md) | The empirical results chapter (n = 318: 284 students, 34 teachers) with full per-item statistics, cross-question findings, a critique of the instrument, and a ranked appendix of all Likert items. |
| [`Novedu-Eval-Approach-Report.md`](Novedu-Eval-Approach-Report.md) | The *SocraticTutorEval* evaluation harness: method, the model-vs-prompt-length experiment, and how it measures adherence to Socratic tutoring behaviour (not problem-solving ability). |
| [`Novedu-Tutor-Prompt-Library-Report.md`](Novedu-Tutor-Prompt-Library-Report.md) | The cross-disciplinary, teacher-authored library of tutor prompt fragments (the ROLE / CONTENT / building-block model) that supplies the platform's pedagogy. |
| [`Novedu-Citations-Relation-Report.md`](Novedu-Citations-Relation-Report.md) | How the cited literature relates to Novedu: where the project agrees with, departs from, or extends the work on pedagogical alignment and configurable tutoring. |

## Survey data

[`2026-06-28-results/`](2026-06-28-results/) holds a self-contained, pseudonymized export of the
questionnaire results, the same data behind `Novedu-Survey-Results-Report.md`. No database access
is needed: the question metadata (texts, types, answer options, scale labels) ships alongside the
answers. See [`2026-06-28-results/README.md`](2026-06-28-results/README.md) for the format. Question
texts and answer values are in German; the results report translates the key findings.

## A note on hosting

Model inference for Novedu runs on an in-region GPU server **operated by SCCH (Software Competence
Center Hagenberg)**, an Austrian applied-research centre, through its general-purpose *LLM:2go*
offering. SCCH provides it to the school at no cost through a government-funded research project;
it is not owned or hosted by the school. This keeps inference on open-weight models in-region rather
than on a commercial cloud LLM.

## Authors

Rainer Stropek and Andreas Probst, HTBLA Leonding (Höhere Technische Bundeslehranstalt Leonding),
Austria. Contact: `r.stropek@htl-leonding.ac.at`.
