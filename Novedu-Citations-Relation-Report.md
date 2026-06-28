# How the Cited Literature Relates to the Novedu Work

This document explains how the four works Rainer Stropek cites in the structured abstract *"From Generic
Chatbots to Teacher-Configurable AI Tutors: The Design and Evaluation of Novedu in Secondary
Education"* (IMCL) relate to Novedu. It is a positioning piece: it sets out where Novedu agrees
with the literature, where we deliberately take a different route, and where the literature
predicts or corroborates our own findings. The quantitative figures throughout come from our
school survey, reported in full in the companion results document (n = 318: 284 students, 34
teachers; 4-point Likert, no neutral midpoint, theoretical mean 2.5). For each cited paper Stropek
splits the survey evidence into where the data confirm the paper, where they complicate it, and
what the survey shows that the paper does not address.

---

## 1. The Through-Line: "Pedagogical Alignment"

All four citations circle one idea, which is also the thesis of the Novedu abstract and the
motivation behind the project: a general-purpose LLM, used as-is, behaves as a generic
information provider that hands over direct answers, whereas effective teaching requires
scaffolded, Socratic guidance. The literature calls closing that gap *pedagogical alignment*.
The papers differ mainly in *how* they propose to achieve it, and that "how" is exactly where
we stake out our own position.

A useful axis for reading all four against Novedu:

- **Alignment by model training** (change the weights): Sonkar et al. (RLHF/preference
  learning), Song et al. (RL with a reward model).
- **Alignment by prompt/configuration** (change the instructions, not the model): Hu et al.
  (a structured JSON tutoring prompt), Gabrovšek & Rihtaršič (configurable custom tutors with
  prompt strategies).

Novedu sits firmly on the prompt/configuration side. Its core mechanism is a
teacher-authored, reusable prompt/text module assembled into a system prompt (the YAML tutor
plus fragment model). We treat pedagogical alignment as a *configuration and authoring* problem
owned by teachers, not a model-training problem owned by ML engineers. Stropek uses the citations to
justify *why* alignment matters; Novedu's contribution is an *institutional,
teacher-configurable delivery* of it. This is the single most important relationship discussed
here.

---

## 2. Paper-by-Paper

### 2.1 Sonkar, Ni, Chaudhary & Baraniuk (2024) — *Pedagogical Alignment of Large Language Models* (EMNLP 2024 Findings)

**What it says.** Coins "pedagogical alignment": training LLMs to *guide* rather than to give
immediate answers. Uses Learning-from-Human-Preferences (RLHF-style) methods, builds a
large synthetic preference dataset, and shows preference-learning beats plain supervised
fine-tuning (+13.1% / +8.7% on Llama / Mistral). Introduces perplexity-based metrics that
quantify a model's tendency toward scaffolded guidance versus direct answers (code:
Tutorbot-Spock).

**Relation to Novedu.**
- It supplies the abstract's central claim, that unadapted models "give direct answers rather
  than the scaffolded guidance that supports learning," and our premise that generic tools are
  pedagogically insufficient.
- *Method contrast:* Sonkar achieves scaffolding by *retraining the model*. We achieve it by
  *configuring the prompt* (for example a "Socratic-tutor behaviour" fragment; our sample math
  tutor YAML literally instructs "explain in small steps, ask follow-up questions, give no final
  solution without derivation"). Same target behaviour, opposite layer of the stack, which is
  appropriate for a school that cannot train models but can author prompts and self-host
  inference.
- *Useful import:* Sonkar's scaffolding-versus-direct-answer metric is a ready-made way for us
  to *evaluate* whether our configured tutors actually scaffold, directly relevant to the
  evaluation half of the work and to the survey finding that students want step-by-step help.

**Survey evidence (the scaffolding premise, tested locally).**
- *Confirms.* Students explicitly want scaffolding over hand-over-the-answer behaviour:
  "explain step by step" is the single most-chosen important function (S3.1, **72.4 %**); when
  stuck, a combined **71.5 %** prefer guided help (ask-where-I'm-stuck 42.3 % + a hint 29.2 %)
  versus only **15.0 %** wanting the full solution (S3.3); and the item *"the tutor should make
  me think for myself rather than give finished answers"* runs strongly positive (S4.3,
  **M = 3.03, 79.9 % top-2**). Teachers agree: **60.6 %** endorse a tutor that deliberately asks
  back to foster critical thinking (L4.4), and only **3 %** reject it. This is direct local
  evidence for the behaviour Sonkar's training targets.
- *Complicates.* The behaviour Sonkar fixes, defaulting to direct answers, is visibly present
  *despite* the stated preference. Only **36.3 %** of students verify AI answers often/always
  (S5.3), **51.9 %** report a real temptation to let the tutor solve tasks outright (S5.1), and
  endorsing "make me think" (S4.3) is statistically *independent of* actually using AI deeply
  rather than as a shortcut (S4.1 ↔ S4.3, *r* = −0.20). The aspiration–behaviour gap is exactly
  why scaffolding may need to be *enforced* by the system (whether by training, as in Sonkar, or
  by a locked prompt mode, as in Novedu) rather than left to learner choice.
- *Gap.* Sonkar contributes a perplexity-based metric that *quantifies* scaffolding; we so
  far have only self-reported preferences, no behavioural measure. Adopting a Sonkar-style metric
  is the concrete way to close the survey's self-report limitation in the evaluation phase.

### 2.2 Hu, Xu, Tong & Graesser (2025) — *Generative AI in Education: From Foundational Insights to the Socratic Playground for Learning* (arXiv:2501.06682)

**What it says.** Reviews AutoTutor (an early Intelligent Tutoring System) and its limits, then
proposes the **Socratic Playground**, a transformer-based ITS for personalized, adaptive,
Socratic tutoring. Notably, it operationalizes tutoring with a structured JSON tutoring prompt
that guides learner reflection and tracks misconceptions.

**Relation to Novedu.**
- This is the closest *architectural* analogue to Novedu's mechanism: pedagogy encoded as a
  structured, declarative prompt artifact. Hu et al. use JSON; we use YAML tutor plus
  fragment files. Both externalize teaching strategy into editable configuration rather than
  model weights.
- The "Socratic" framing maps onto our reusable Socratic-tutor behaviour module named
  in the abstract and present in the MVP, and onto our emphasis on guidance over answers.
- *Difference in ambition/scope:* the Socratic Playground is a research ITS with misconception
  tracking and reflection modeling; our MVP is deliberately scope-reduced and does *not* yet
  model misconceptions or learner state. It configures persona, rules, and model. Hu et al.
  therefore reads as a *roadmap* for where our configurable tutors could go.

**Survey evidence (declarative pedagogy + misconception tracking, tested locally).**
- *Confirms.* The "pedagogy-as-editable-artifact" stance matches what both groups ask for:
  students' top explanation preference is *"it depends on the topic — I want to choose"*
  (S3.2, **52.0 %**), and teachers want help behaviour to be configurable per Workspace
  (L3.3, 33.3 %; L4.4 "yes, if configured in the Workspace" a further 24.2 %). Neither group
  wants one hard-coded mode; they want the declarative, settable pedagogy Hu's structured
  prompt embodies. Hu's *misconception-tracking* idea also has clear demand on the teacher side:
  **75.8 %** want *aggregated* usage insight of exactly the "70 % of the class had questions on
  topic X" form (L6.2), class-level misconception detection in all but name.
- *Complicates.* Hu's model tracks the *individual* learner's state and reflection; our students
  impose a hard privacy boundary against that. **57.2 %** find a teacher reading even
  *anonymised* class chats uncomfortable (S6.2) and **41.6 %** would permit *no* read-along
  purpose at all (S6.3). Learner-state modeling à la Hu is acceptable here only if it stays
  aggregated and anonymised; the individualised reflection model would meet resistance in
  this population.
- *Gap.* The MVP models neither misconceptions nor learner state; the survey shows the demand is
  specifically for a *lighter, aggregated* version of Hu's vision, which usefully bounds how much
  of the Socratic Playground is worth importing in a school setting.

### 2.3 Song, Liu, Lu et al. (2025) — *EduAlign: Cultivating Helpful, Personalized, and Creative AI Tutors via Reinforcement Learning* (arXiv:2507.20335)

**What it says.** Argues standard LLMs are "generic information providers" misaligned with
pedagogy. Curates ~8k educational interactions annotated on three dimensions — **Helpfulness,
Personalization, Creativity (HPC)** — trains a multi-dimensional reward model (HPC-RM), and uses
RL (GRPO) to produce EduAlign-LLM.

**Relation to Novedu.**
- It provides the abstract's exact phrasing, generic chatbots as "generic information
  providers," and reinforces the personalization argument behind our teacher-built tutors.
- *Method contrast (again, training versus configuration):* EduAlign bakes
  helpfulness/personalization/creativity into *one fine-tuned model*. We obtain personalization a
  different way, through many teacher-configured tutors, each specialized to a topic/unit/class,
  over a shared, in-region base model. Personalization lives in the *configuration layer and
  the curricular grounding*, not in trained weights.
- *Survey link:* EduAlign's "personalization" dimension is precisely what our survey
  found to be the #1 demand: curricular grounding in the teacher's own materials. The paper
  motivates the goal; the survey quantifies the local appetite for it.

**Survey evidence (the HPC dimensions, tested locally).**
- *Confirms — Personalization, emphatically.* This is the strongest single result in the survey:
  *"how important is it that the tutor knows exactly your lesson content"* is the **highest-rated
  student Likert item, M = 3.53, 90.1 % top-2** (S3.4), and it is uniform across branches
  (Informatik 3.54, other 3.69, sciences 3.16), not an IT-majority artefact. Reinforcing items:
  level-matched exercises (S3.1, 53.1 %), adapt to my pace/level (S3.1, 35.6 %; S4.5, 39.2 %), and
  on the teacher side personalised learning (L4.3, 63.6 %) and curriculum alignment (L3.2,
  M = 2.91, 69.7 % top-2). *Confirms — Helpfulness:* "instant help, also outside class" is the
  top perceived benefit (S4.5, **65.6 %**).
- *Complicates — Creativity has essentially no local demand signal.* EduAlign's third HPC pillar
  does not surface as a priority: "motivation and variety" is the **least-chosen** benefit
  (S4.5, **12.8 %**), and creativity appears in neither the top functions (S3.1) nor the no-gos
  (S7.1) nor the value free-text (S7.2). In this population, correctness and curricular fit
  crowd out creativity; the C in HPC is the weakest-corroborated dimension here.
- *Gap / extension.* EduAlign bakes personalization into *one fine-tuned model*; the survey shows
  the personalization students actually want is content-specific ("know *my* lessons / the
  teacher's materials," repeated verbatim in S2.5/S3.6 free text, incl. explicit Moodle access).
  A single trained model cannot supply per-class curricular grounding, so the data point past
  EduAlign's method toward configuration plus retrieval over local materials (RAG), which is
  Novedu's route. The survey thus extends EduAlign: it specifies *what kind* of personalization
  a school needs, which the reward-model framing leaves open.

### 2.4 Gabrovšek & Rihtaršič (2025) — *Custom Generative AI Tutors in Action: An Experimental Evaluation of Prompt Strategies in STEM Education* (Sustainability 17(21):9508)

**What it says.** Builds a configurable GenAI tutoring prototype, deploys it in a real
undergraduate **electrical-engineering lab**, and analyzes **208 student–tutor interactions**
(mixed methods). Findings: student prompts are mostly procedural/factual, with little
conceptual/metacognitive engagement; structured prompt styles produced clearer responses and
were rated highest by students, while reasoning/reflection-oriented styles were valued more
by the research team than by students.

**Relation to Novedu.**
- This is the most directly comparable work: a custom, configurable, prompt-strategy-based
  tutor evaluated *in an authentic STEM classroom*, methodologically a sibling of Novedu's
  requirements-driven design-and-evaluation in a real school.
- It validates our central design bet, that prompt configuration (not model training) is
  a viable lever for custom tutors, and supplies a comparable evaluation design (interaction
  logs plus learner feedback) that we can mirror once our MVP is in classrooms.
- *A productive tension:* Gabrovšek finds students *prefer structured, answer-like
  responses* over reflection-oriented ones, echoing our own survey "pedagogy-versus-convenience"
  tension (students endorse guided learning in closed questions but demand fast answers in free
  text). Both point to the same design conclusion we reached: make the pedagogical mode
  configurable per lesson/tutor, with a Socratic default and a teacher override.

**Survey evidence (the sibling study, tested locally).**
- *Confirms — structured beats purely reflective.* Gabrovšek's headline (students rate
  structured prompt styles highest, reflective styles valued more by researchers than learners)
  reappears almost exactly: students' top explanation choices are *"let me choose"* (S3.2, 52.0 %)
  and step-by-step (27.3 %), both structured, and they accept an occasionally-wrong tutor if it
  is usually helpful (**S5.2, 66.3 % top-2**). The reflective ideal is endorsed in the abstract
  (S4.3, 79.9 %) but undercut in behaviour (only 36.3 % verify answers, S5.3), the same
  researcher-versus-learner gap Gabrovšek measured.
- *Confirms — prompt configuration is a viable, willing lever.* No teacher rules it out:
  **0 %** say they would *never* integrate a tutor (L2.1), and willingness to author Workspaces
  is high but conditional. A combined ~**55 %** will do it *only if it is fast (<15 min) or
  template-based* (L2.3), with easy Workspace creation a top-3 property (L3.1, 39.4 %). This
  validates the configuration route while pricing its precondition: low-effort authoring plus
  starter templates.
- *Complicates / extends.* Gabrovšek found student prompts mostly procedural/factual with little
  metacognitive engagement; our data corroborate the low metacognition (verification 36 %,
  over-use temptation 52 %) and extend it with two demands his framing does not foreground:
  factual correctness as the bilateral red line (students' top free-text no-go ≈21 %;
  teachers' #1 property **84.8 %**, L3.1) and curricular grounding (S3.4, M = 3.53). Novedu's
  requirements set is therefore broader than "which prompt style."
- *Shared caveat (external validity).* Both studies sit in tech-heavy STEM settings:
  Gabrovšek an undergraduate electrical-engineering lab, Novedu an informatics-dominated HTL
  (72.9 % of students in the IT branch; 55.9 % of teachers teach programming). The
  structured-over-reflective preference and the correctness obsession may be partly artefacts of
  technically-minded learners and should not be over-generalised to general-education
  contexts, a limitation the two works share.
- *Methodological note.* Gabrovšek analysed 208 real interaction logs; our comparable
  evaluation is constrained by our own finding that students reject individual chat visibility
  (S6.2, 57 % uncomfortable). Any log-based evaluation we mirror must run on aggregated and
  anonymised data, a constraint Gabrovšek's consenting-lab design did not face.

---

## 3. Synthesis — Where Novedu Sits in This Literature

1. **Shared premise, school-scale execution.** All four papers establish that alignment matters;
   our vision adopts that premise, and Novedu's distinct contribution is institutional
   embedding: SSO, teacher ownership, privacy/self-hosting, budgets, and a cooperative
   school-development model, none of which the four papers address. The literature is about *the
   tutor*; Novedu is about *the school's path to fielding tutors*.

2. **Configuration over training is a deliberate, defensible choice.** Two of the four papers
   (Sonkar, Song) align by training; two (Hu, Gabrovšek) align by prompt/structure. We chose
   the prompt/configuration route, the only realistic one for a secondary school. Hu and
   Gabrovšek are our precedent that structured prompts and configurable tutors work, while
   Sonkar and Song justify *why* the behaviour they encode (scaffolding, HPC) is the right
   target.

3. **The survey corroborates the literature locally, with figures and with limits.** The
   empirical drivers the papers assert all reappear in our results (n = 318): scaffolded
   guidance (Sonkar → S3.1 step-by-step 72.4 %, S4.3 M = 3.03), declarative/Socratic pedagogy
   (Hu → "let me choose" 52.0 %, per-Workspace configurability), personalization (Song → S3.4
   curriculum grounding M = 3.53, the top item), and the structured-versus-reflective tension
   (Gabrovšek → guided endorsed but only 36 % verify answers). Our 318 respondents thus take
   the argument beyond "the literature says." Crucially, the data also *bound* the literature:
   EduAlign's "creativity" pillar has near-zero local demand (S4.5, 12.8 %), and the
   personalization students want is *curricular*, not model-trained, pushing past Song's method
   toward configuration plus RAG.

4. **The citations also chart Novedu's roadmap.** Misconception tracking and learner-state modeling
   (Hu), quantitative scaffolding metrics (Sonkar), HPC reward dimensions as evaluation rubrics
   (Song), and interaction-log evaluation in a live course (Gabrovšek) are all things our
   scope-reduced MVP does *not* yet do but could adopt for its broader, ongoing evaluation phase.

---

## 4. Citation → Abstract Claim → Novedu Evidence (quick map)

| Citation | Claim it supports | Survey **confirms** (figures, n = 318) | Survey **complicates / extends** |
|---|---|---|---|
| Sonkar et al. 2024 | Unadapted LLMs give direct answers, not scaffolding | Step-by-step top function (S3.1 **72.4 %**); guided-when-stuck **71.5 %** vs full-solution 15.0 % (S3.3); "make me think" S4.3 **M = 3.03** | Behaviour contradicts stated preference: verify only **36.3 %** (S5.3), over-use temptation **51.9 %** (S5.1); S4.1↔S4.3 *r* = −0.20 → scaffolding may need enforcing |
| Hu et al. 2025 | Keep pedagogy central; Socratic ITS via structured prompt; misconception tracking | Configurable explanation "let me choose" (S3.2 **52.0 %**); aggregated misconception insight wanted by teachers (L6.2 **75.8 %**) | Individual learner-state modeling blocked by privacy: **57.2 %** uncomfortable with anonymised read-along (S6.2) → must stay aggregated |
| Song et al. 2025 (EduAlign) | Generic chatbots need Helpfulness / Personalization / Creativity | Personalization: curriculum grounding S3.4 **M = 3.53 (top item)**; Helpfulness: instant help S4.5 **65.6 %** | **Creativity has ~no demand** (S4.5 variety **12.8 %**); wanted personalization is *curricular*, not trainable into one model → RAG over local materials |
| Gabrovšek & Rihtaršič 2025 | Configurable tutors + prompt strategies work in STEM; students prefer structured | Structured preferred (S3.2 52 % + 27 %); tolerate occasional error (S5.2 **66.3 %**); willing to author *if cheap* (~**55 %**, L2.3); 0 % "never" use (L2.1) | Adds correctness (**84.8 %** teacher #1, L3.1) + curriculum as red lines; shared STEM external-validity caveat; log-eval must be anonymised (S6.2) |

---

## 5. Notes & Caveats

- Stropek's summaries are based on the papers' abstracts and HTML as fetched on 2026-06-28; for direct
  quotation he verifies exact wording and page numbers against the published PDFs.
- The "training versus configuration" framing is Stropek's own analytical lens, not the papers' own
  terminology. It is useful for positioning Novedu, but he presents it as his framing rather than
  theirs.
- Novedu commits to open-source, privacy-compliant, European and self-hostable components and
  teacher-configured tutors; none of the four papers share that sovereignty/institutional
  emphasis, which is where Novedu's novelty mainly lies.

---

## Sources

- Sonkar, Ni, Chaudhary & Baraniuk (2024), *Pedagogical Alignment of LLMs* — [ACL Anthology](https://aclanthology.org/2024.findings-emnlp.797/) · [arXiv:2402.05000](https://arxiv.org/abs/2402.05000)
- Hu, Xu, Tong & Graesser (2025), *Generative AI in Education: … Socratic Playground* — [arXiv:2501.06682](https://arxiv.org/abs/2501.06682)
- Song, Liu, Lu et al. (2025), *EduAlign* — [arXiv:2507.20335](https://arxiv.org/abs/2507.20335)
- Gabrovšek & Rihtaršič (2025), *Custom Generative AI Tutors in Action* — [Sustainability 17(21):9508 (MDPI)](https://www.mdpi.com/2071-1050/17/21/9508)
