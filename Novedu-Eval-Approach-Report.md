# Novedu Evaluation Approach — SocraticTutorEval

SocraticTutorEval (Python package **SocraticTutorEval**, `socratic-tutor-eval` CLI, school-internal 
GitHub repository https://github.com/general-stuff/prompt-model-eval) is the
evaluation harness Rainer Stropek built to score tutor system prompts and models for Socratic behaviour. He
authored all 12 commits in its repository between 2026-04-30 and 2026-06-08. It is the foundation
of our evaluation effort and currently carries only a few test scenarios; Stropek designed it to become
the basis for a structured analysis carried out by multiple teachers over the coming months. He
has shared the approach with the involved teachers, and first experiments across the group have
begun.

The harness is the evaluation sub-project of Novedu. Where the survey and the literature review
establish what tutors should do, and the MVP shows how they are built, this harness measures which
models and which system prompts make Novedu's tutors actually behave Socratically.

---

## 1. What It Is and Why It Exists

SocraticTutorEval is a standalone evaluation harness for Socratic tutor system prompts. Given a
partial student-tutor conversation that ends on a student turn, it generates the next tutor reply
with several LLMs and has an LLM judge score each reply against a per-scenario rubric. Stropek designed
it to answer two questions that are decisive for a platform like Novedu that relies on in-region,
open-weight inference rather than a commercial cloud LLM, both rooted in our cost and privacy
constraints:

1. **Which models can we use, and how small can we go before tutoring behaviour degrades?** Novedu
   serves inference from a GPU server operated by SCCH, an external Austrian research centre rather
   than the school's own hardware. Smaller open-weight models are cheaper and faster, but only
   useful if their tutoring behaviour holds up. The harness quantifies that trade-off.
2. **How detailed must an AI-tutor system prompt be?** Novedu's whole design bet is
   configuration-over-training: pedagogy lives in teacher-authored prompts. So it matters
   empirically how much prompt detail a model needs to behave Socratically. Can a short prompt
   suffice, or is a long, carefully engineered one required?

A point worth stating clearly at the outset, because it shapes how the model results should be
read: the harness measures adherence to the intended Socratic tutoring behaviour, not raw
problem-solving ability. A failing reply does not mean the model gave a wrong answer. It means the
reply departed from the behaviour the teacher specified, primarily by abandoning the Socratic
stance and volunteering the full solution. This distinction runs through the whole evaluation and
recurs in the model comparison below.

---

## 2. How the Harness Works (method)

A run is assembled from three artefacts, each a plain file teachers can author:

- **System prompts** — `system-prompts/<subject>/*.md`, the tutor personas under test (one per
  topic, written in the subject's language).
- **Scenarios** — `data/<subject>/*.yaml`: a partial conversation ending on a student turn, plus
  a rubric (`goal`, `positive_criteria`, `negative_criteria`), an `applicable_prompts` list (which
  prompts the scenario is valid against), and an optional `canonical_prompt`.
- **Models** — `llms/llms.yaml`: OpenAI-compatible servers each with a model list, plus a separate
  **Judge** block selecting the grader. Entries can be toggled with `enabled: false`.

Two run modes cleanly separate the two research questions:

- `--mode models` (default) compares models. It holds each scenario's canonical prompt fixed and
  lets every selected model generate the reply. Matrix: `1 prompt × N scenarios × M models`. This
  answers Question 1.
- `--mode prompts` compares prompt variants. For each scenario it runs one test per entry in the
  scenario's `applicable_prompts` (optionally pinned to a single model with `--model`). Matrix:
  `K prompts × N scenarios × M models`. This answers Question 2.

For every `(scenario, prompt, model)` triple the harness validates inputs (Pydantic schemas plus
a cross-file linter, for example the rule that the conversation must end on the student), sends
`[system_prompt, …conversation]` to the model under test, then calls the judge model with
structured output. The judge returns, per criterion, a `met`/`violated` verdict with quoted
evidence and reasoning, a `goal_alignment` score, an overall `pass | partial | fail`, and a list
of `socratic_violations` drawn from a fixed enum: `gave_answer`, `multiple_questions`, `lectured`,
`judgmental`, `off_topic`. A row passes if and only if no negative criterion is violated and at
least 70 % of positive criteria are met. Divergence between the mechanical pass/fail and the
judge's own verdict (or a low goal-alignment on a passing row) is surfaced as a warning, a signal
that a rubric or the judge prompt needs tightening.

A few engineering choices are worth noting. The harness pre-expands `(scenario, prompt)` pairs
itself so invalid pairings cannot be evaluated by construction. **promptfoo** orchestrates the
matrix (parallelism, caching, an HTML report viewer) while the model and judge calls go through
the project's own Python provider and grader, which add retry (exponential backoff on
timeouts/429/5xx) and a circuit breaker: a flaky `(server, model)` pair is dropped after failing
two scenarios, and the rest of the matrix continues. This resilience matters because external,
third-party-operated servers like the SCCH backend can be unreliable. The stack is Python (uv, Pydantic,
Ruff, Pyright) plus promptfoo (Node).

---

## 3. The Two Experiments, Concretely

### 3.1 Model comparison — "how small can we go?"

The model matrix (`llms/llms.yaml`) spans the relevant cost and sovereignty spectrum:

- **SCCH (in-region, the production target):** `RedHatAI/gemma-4-31B-it-FP8-Dynamic`,
  `Qwen/Qwen3.6-27B-FP8`. These are quantized, open-weight models of roughly 27-31B parameters
  that run on the SCCH-operated GPU server.
- **Frontier / cloud baselines:** OpenAI and Azure OpenAI `gpt-5.4` (medium and high reasoning
  effort) and `gpt-5.4-mini`; OpenRouter `gpt-oss-120b` and `deepseek-v4-flash`.
- **Judge:** Azure OpenAI `gpt-5.4` at high effort (structured output).

Running `--mode models` scores the small in-region (SCCH) models against frontier baselines on
identical scenarios. The result that needs careful interpretation is that frontier models do not
score lower because they are weaker. They are more capable, and that capability works against them
here, because their characteristic failure mode is over-responding: when in doubt, a strong model
tends to volunteer the full, correct solution, which is exactly the behaviour the Socratic rubric
penalizes. In practice Stropek found it easier to steer a small model into Socratic behaviour than to
restrain a large one from over-helping. So the comparison is not "smaller models are simply as
good". It is a measure of how reliably a given model can be held to the teacher-specified stance,
and it tells us which models we can realistically host while keeping the intended tutoring
behaviour intact.

### 3.2 Prompt-detail comparison — "how much instruction does a model need?"

The Programming subject ships three length variants of the same linked-lists tutor prompt:
`linked-lists-minimal.md` (6 lines), `linked-lists-short.md` (84 lines), and `linked-lists.md`
(172 lines). The minimal prompt's own description states that it is "deliberately very short, to
test how much precise instruction models even need to produce the desired answers." Running
`--mode prompts` over these variants quantifies the prompt-engineering burden placed on teachers,
a practical question for a platform whose users are teachers, not prompt engineers.

---

## 4. Test Data (the seed corpus)

The current corpus is deliberately small but broad across subjects: 11 system prompts and 28
scenarios across five subjects, mirroring the survey's cross-disciplinary spirit.

| Subject | Scenarios | Notes |
|---|---|---|
| Programmieren (Programming) | 13 | Richest set — linked-lists variants incl. memory-leak, use-after-free, recursion/stack-overflow, "do my whole assignment," and an `off_topic`/`violence` probe |
| Mathematik | 4 | Linear & quadratic functions; sign errors, applied word problems, emotional pressure |
| Deutsch | 4 | Essay (Erörterung) & text analysis; claims without evidence, "just give me the solution" |
| English | 4 | Present-perfect vs. past-simple, business email; informal tone, German-switching |
| Rechnungswesen (Accounting) | 4 | Double-entry bookkeeping, VAT; swapped debit/credit, resignation/emotion |
| **Total** | **28** | |

Scenarios encode realistic, hard tutoring moments, not just wrong answers but emotional pressure,
demands for the full solution, and off-topic or abuse attempts, so the rubric tests whether a
tutor holds the Socratic line. A companion `scenario-feedback.md` provides a ready-made prompt for
having a frontier LLM review a new scenario for rubric and goal consistency before it is committed,
a quality-control step for teacher-authored test data.

---

## 5. Timeline (and how it interlocks with the other sub-projects)

| Date | Event | Concurrent with |
|---|---|---|
| 2026-04-30 | Foundation built in one day: app scaffolding, eval v0, promptfoo split by subject, judge logic, first Math/Deutsch/English/Accounting scenarios + coding examples | The survey field period and the UX click-dummy design phase, before the MVP rebuild existed |
| 2026-05-01 | Linked-lists scenarios deepened (the programming corpus) | Just after the survey's main response waves |
| 2026-06-08 | Promptfoo evals skill + cheatsheet; refined evaluation criteria & goal-alignment warnings | The day of the MVP architecture workshop |
| 2026-06 → coming months | Rolled out to the teacher group; first cross-teacher experiments started; structured analysis to follow | Parallel to ongoing MVP feature development |

The interlock matters: Rainer Stropek started the evaluation harness during the requirements and design phase,
alongside the survey, matured it on the same day the MVP was seeded, and now run it in parallel
with platform development. The project therefore has a feedback loop between what tutors should do
(the survey), how to build them (the MVP), and whether a given model and prompt actually does it
(this harness).

---

## 6. How It Connects to the Rest of Novedu

- **It closes the survey's self-report gap.** The survey shows that students want scaffolding, not
  answers; this harness measures whether a deployed model and prompt actually withholds the answer.
  Its `socratic_violations.gave_answer` is the operational counterpart to the survey's stated
  preference and to Sonkar et al.'s scaffolding-vs-direct-answer metric.
- **It validates the configuration-over-training bet.** By testing prompt-detail variants, it
  directly probes whether teacher-authored prompts, rather than model fine-tuning, can elicit
  Socratic behaviour, which is the central methodological claim of the whole project.
- **It de-risks the self-hosting decision.** The model matrix tells us which small, in-region,
  cost-free SCCH-hosted models can be held to the intended tutoring behaviour, grounding the
  infrastructure choice in evidence rather than assumption.
- **It shares the platform's design DNA.** Plain-file, YAML/Markdown authoring; schema validation;
  a CLI; an OpenAI-compatible model seam; the SCCH server as the inference backend. This is the
  same philosophy as the MVP, so scenarios and prompts proven here transfer directly into Novedu
  tutors.
