# Novedu School Survey — Results

**Data files:** the `2026-06-28-results/` export of the Azure SQL database `ai-tutor-questionaire`
(`responses.json` as the primary source, `questions.json` as the codebook, and
`responses_{students,teachers}.csv`). Export date: 2026-06-28. The question texts and verbatim
answers are German; key quotes are translated inline.

This document presents the empirical results of the school-wide survey. It is the detailed
results counterpart to the companion document describing the instrument
(`Novedu-Survey-Report.md`), which set out the questionnaire design and the earlier 2026-05-27
analysis. Here Rainer Stropek analyses the full, later export (`n = 318`).

> **Scale convention (read before any mean).** All Likert items use a **4-point scale with no
> neutral midpoint** (theoretical mean = 2.5). There is no "5". Scale *direction differs per
> item*, so every reported mean is paired with the meaning of `1` and `4`. Means are reported
> with the sample standard deviation (`SD`); "top-2" = share answering 3 or 4.

---

## 1. Executive Summary

The survey collected **318 valid responses, 284 students and 34 teachers**, at HTBLA
Leonding, an Austrian federal technical upper-secondary school (HTL) specialising in
informatics and engineering. Participation was high and item-level completion excellent
(students answered on average 36.5 of 39 questions; teachers 34.4 of 37), but the sample is
**strongly informatics-biased** (72.9 % of students in the IT branch; 56 % of teachers teach
programming), which is the single most important caveat for external validity.

Five findings dominate and are mutually consistent:

1. **Curricular grounding is the strongest single signal.** The highest-rated student Likert
   item by a clear margin is *"how important is it that the tutor knows exactly your lesson
   content"* (S3.4, **M = 3.53**, 90 % top-2). It recurs as the leading free-text wish.
2. **Factual correctness is the shared red line.** Hallucinations and wrong answers are
   students' most frequent free-text "no-go" (≈21 %) and teachers' top requirement
   (84.8 % rank factual correctness among their three most important tutor properties).
3. **Pedagogy-vs-convenience tension is real and internally visible.** Students endorse
   "make me think for myself" (S4.3, M = 3.03) yet also report a substantial temptation to
   let the tutor do the work outright (S5.1, M = 2.60; 52 % top-2), and only 36 % check AI
   answers "often/always" (S5.3). The data themselves contain the contradiction the platform's
   *configurable pedagogy modes* are meant to resolve.
4. **Cost/limits is a standalone driver.** 74.8 % of students have no paid AI account, and
   "paywall/limits" is the most frequent free-text theme for *what a school tutor must do
   better* (≈15 %). A school-provided, unlimited tool has value on its own.
5. **Teachers are willing but feel under-prepared.** 100 % would use the tutor at least
   occasionally and 94 % see their students already using AI regularly, yet only ~24 % use AI
   with students regularly, just 6 % feel "well informed" on the legal framework, and 84.8 %
   want training (L7.2, M = 3.27).

On privacy, expectations are compatible with a privacy-by-design product: students dislike
teachers reading along (57 % find it uncomfortable even when anonymised), while teachers
overwhelmingly want **aggregated** statistics (75.8 %) rather than individual chats (6 %).

These results validate the platform's core bets: teacher-configurable, curriculum-grounded
tutors with configurable Socratic behaviour, factual fidelity, and aggregated (not
surveillance-style) oversight. They also reinforce our MVP scoping decision.

---

## 2. School Background: HTBLA Leonding

All respondents come from a single institution, **HTBLA Leonding** (Höhere Technische
Bundeslehranstalt Leonding), in Upper Austria. Interpreting the results requires knowing what
kind of school this is.

An **HTL** (*Höhere Technische Lehranstalt*) is an engineering-focused Austrian
upper-secondary college. The standard *Höhere Abteilung* track runs **five years, starting at
grade 9 (≈ age 14)** and concluding with the *Reife- und Diplomprüfung*, a final examination
that includes the *Matura* and entitles graduates to attend university, combined with a
substantial diploma project. HTLs are a major pillar of Austrian vocational education (≈ 75
HTLs nationally as of 2016).

HTBLA Leonding describes itself as **"The Digitizers"** and a certified **MINT school**
(mathematics, informatics, natural sciences, technology). Its departments are **computer
science, IT media technology, electronics & technical computer science, and biomedical &
health technology**, supplemented by general-education subjects and a night school. It
positions itself as a training centre tightly coupled to the regional technology economy, with
project-based cooperation with industry, the same ethos under which we build Novedu itself as
a cooperative student software-engineering project.

**Why this matters for the data.** The respondent pool reflects this profile directly: the
school's grade structure spans the 9th–13th *Schulstufe*, and the student sample is dominated
by the informatics branch. The findings should be read as those of a **technically literate,
AI-fluent, programming-heavy population**, not a general secondary school. This both
strengthens the requirements signal for a CS-school deployment and limits 1:1 generalisation to
general-education contexts, which remains the headline caveat throughout this analysis.

*Sources:* [HTL Leonding — About](https://www.htl-leonding.at/en/about/);
[Höhere Technische Lehranstalt — Wikipedia](https://en.wikipedia.org/wiki/Hohere_Technische_Lehranstalt).

---

## 3. Participation and Data Quality

### 3.1 Sample composition

| Group | Respondents | Registered accounts | No-answer accounts |
|---|---|---|---|
| Students | **284** | — | — |
| Teachers | **34** | — | — |
| **Total** | **318** | 336 | 18 (signed in, never submitted) |

The export retained pseudonymised IDs (`S###`, `T###`) and, for students, the `class_name`
(283 of 284 students carry a class; teachers have none by design). 19 distinct classes appear,
spanning year-levels 1–5 (class-name prefix counts: year 1 = 79, year 2 = 96, year 3 = 59,
year 4 = 46, year 5 = 3).

This export (284 students) is **larger than the 265 reported in the 2026-05-27 analysis**. The
19 additional student questionnaires are all dated **2026-05-27**, i.e. a final batch collected
on the day the earlier analysis was produced. Teacher `n` is unchanged at 34.

### 3.2 Completion and item-level response

Answers were saved incrementally per section, so completion degrades gradually rather than
all-or-nothing.

| Metric | Students (39 items) | Teachers (37 items) |
|---|---|---|
| Mean items answered | 36.5 | 34.4 |
| Median items answered | 37 | 35 |
| Fully complete questionnaires | 46 (16.2 %) | 3 (8.8 %) |
| ≥ 50 % of items answered | 274 (96.5 %) | 33 (97.1 %) |
| Range (min–max) | 4–39 | 9–37 |

Per-item response rates show a mild, monotonic drop-off from the first cluster (100 %) to the
last substantive free-text items (~94 % for students, ~97 % for teachers), consistent with
incremental saving and modest fatigue. **Optional items** show the expected sharp fall:
student S3.6 (37 %), S7.3 (24 %); teacher L3.4 (79 %), L3.7 (18 %), L7.5 (21 %). Absence of a
question key denotes *not answered*, not an empty answer, so Stropek reports per-question `n`
throughout rather than assuming a constant denominator.

### 3.3 Field timeline

Responses cluster in **supervised in-class sessions**. ~93 % of student questionnaires fall in
2026-04-16 → 2026-05-12, with clear peaks on 2026-05-05 (91), 2026-05-12 (47), 2026-05-06 (35)
and 2026-05-08 (24), plus a small opening batch on 04-16 (14) and the late 05-27 batch (19).
Teacher responses run 2026-04-21 → 2026-05-11, peaking 05-04/05-05 (11 each). The supervised,
clustered administration likely explains both the high completion and a possible mild
social-desirability / in-class context effect (see §7).

---

## 4. Questionnaire Structure

Two role-specific variants share a stable ID scheme `[S|L]<cluster>.<n>` and the same 8
thematic clusters. Students answered **39** items, teachers **37**.

| Cluster | Theme | Student items | Teacher items |
|---|---|---|---|
| 0 | Demographics & digital context | S0.1–0.4 | L0.1–0.4 |
| 1 | Current AI usage (status quo) | S1.1–1.6 | L1.1–1.5 |
| 2 | Willingness & deployment scenarios | S2.1–2.5 | L2.1–2.5 |
| 3 | Functional requirements & features | S3.1–3.7 | L3.1–3.7 |
| 4 | Learning effectiveness / pedagogy | S4.1–4.5 | L4.1–4.4 |
| 5 | Risks, misuse & fairness | S5.1–5.5 | L5.1–5.3 |
| 6 | Data protection, transparency, trust | S6.1–6.4 | L6.1–6.4 |
| 7 | Framework conditions & open questions | S7.1–7.3 | L7.1–7.5 |

**Item types.** Five answer types are used: `single-choice`, `multiple-choice` (several capped
at "pick top 3" via `maxSelections`), `multiple-choice-freetext` (options plus an "other"
field), 4-point `likert`, and `freetext`. Students: 13 Likert, 9 single-choice, 5
multiple-choice, 4 multiple-choice-freetext, 6 free-text, plus 2 demographic single-choice. The
instrument was designed by a cross-disciplinary teacher panel; the design rationale and history
are in the companion instrument document.

### 4.1 Method of this analysis

This is a descriptive-statistics analysis that Stropek computed for every item directly from
`responses.json`: Likert mean/SD/median and the full 1–4 distribution; frequency counts and
percentages (of respondents to that item) for choice items; selection counts and mean
selections for multiple-choice; and verbatim collection plus keyword-based thematic coding for
free-text. For multiple-choice, percentages sum to >100 % because respondents pick several
options. He ran **targeted inferential tests** (Pearson correlations between Likert items, with
t-statistics; subgroup mean comparisons by branch/grade) only where a substantive hypothesis or
apparent contradiction warranted it; given the 4-point ordinal scale, he interprets correlations
as indicative monotonic association, not as interval-scale effect sizes. **Stropek reports no
significance testing for the teacher group beyond description**: at n ≈ 33, each respondent is
~3 percentage points and tests are underpowered.

---

## 5. Student Results (n = 284)

### 5.1 Cluster 0 — Who answered

| Item | Question (abbrev.) | Type | n | Result |
|---|---|---|---|---|
| S0.1 | Grade level | SC | 284 | 9th 26.8 %, 10th 34.2 %, 11th 21.5 %, 12th 16.2 %, 13th 1.4 % |
| S0.2 | Branch | SC | 284 | **Informatik 72.9 %**, other 20.1 %, sciences 6.7 %, business 0.4 % |
| S0.3 | Digital skill (1 not good–4 very) | Likert | 284 | **M = 3.25**, SD 0.72, top-2 87.7 % |
| S0.4 | Prior AI use | SC | 284 | regularly 84.5 %, tried a few times 9.5 %, never 6.0 % |

**Findings.** The sample skews toward lower grades (61 % in grades 9–10) and overwhelmingly
toward informatics. Self-rated digital competence is high and prior AI exposure near-universal
(94 % have used an AI tool, 84.5 % regularly): this is an early-adopter population, so
acceptance figures throughout should be read as an upper bound relative to a general school.

### 5.2 Cluster 1 — Current AI usage

| Item | Question | Type | n | Headline |
|---|---|---|---|---|
| S1.1 | Tools used | MC+ft | 282 | ChatGPT 83.7 %, Gemini 59.9 %, Claude Code 50.0 %, GitHub Copilot 38.3 %, "none" 2.5 % |
| S1.2 | Paid account? | SC | 282 | **No 74.8 %**, >10 €/mo 14.5 %, ≤10 € 6.0 %, unsure 4.6 % |
| S1.3 | Frequency (school) | SC | 282 | several/wk 45.4 %, daily 34.4 %, never 3.9 % |
| S1.4 | Main purposes (top-3) | MC | 282 | explanations 83.0 %, coding/debugging 64.2 %, exam prep 48.2 % |
| S1.5 | Main device | SC | 282 | laptop 60.3 %, smartphone 27.0 %, desktop 11.3 % |
| S1.6 | AI share of schoolwork (1 none–4 most) | Likert | 282 | M = 2.41, SD 0.76, top-2 43.6 % |

**Findings.** Usage is heavy and tool-diverse, with a clearly developer-flavoured stack
(Claude Code 50 %, GitHub Copilot 38 %, unusual for secondary students and a direct artefact
of the IT branch). Despite daily/weekly use by ~80 %, three-quarters pay nothing, so they live
inside free-tier limits, the structural basis for the cost/limits frustration that surfaces in
free text (§5.8). The self-assessed AI *share* of schoolwork is moderate (M = 2.41, majority
below midpoint), suggesting students see AI as an assist rather than a replacement, a useful
counterweight to "students just cheat" narratives.

### 5.3 Cluster 2 — Willingness and scenarios

| Item | Question | Type | n | Headline |
|---|---|---|---|---|
| S2.1 | Expected usage if school offers it | SC | 279 | several/wk 37.3 %, daily 22.9 %, **not at all 10.0 %** |
| S2.2 | Situations (top-3) | MC | 279 | exam/test prep 81.0 %, stuck on homework 64.9 %, programming 52.3 % |
| S2.3 | Home vs class | SC | 279 | both 40.1 %, mainly home 27.6 %, undecided 21.9 %, mainly class 10.4 % |
| S2.4 | Subjects most wanted (top-3) | MC+ft | 279 | programming 72.4 %, applied maths 64.5 %, physics 42.3 % |
| S2.5 | What must it do better than ChatGPT? | freetext | 279 | see thematic coding §5.8 |

**Findings.** Projected adoption is strong (≈60 % at least several times a week), but a
non-trivial 10 % say they would not use a school tutor at all, a realistic ceiling worth
reporting honestly. Demand concentrates on the technical/quantitative core subjects
(programming, applied maths, physics), again reflecting the branch mix and pointing to where a
first deployment will earn its keep.

### 5.4 Cluster 3 — Functional requirements

| Item | Question | Type | n | Headline |
|---|---|---|---|---|
| S3.1 | Most important functions (top-3) | MC | 275 | step-by-step explanation 72.4 %, level-matched exercises 53.1 %, code/text feedback 38.2 % |
| S3.2 | Preferred explanation style | SC | 275 | **"depends — let me choose" 52.0 %**, step-by-step 27.3 % |
| S3.3 | Help when stuck | SC | 274 | ask where I'm stuck 42.3 %, hint 29.2 %, full solution 15.0 %, next step 13.5 % |
| S3.4 | Tutor must know my lesson content (1 unimp.–4 very) | Likert | 274 | **M = 3.53, SD 0.77, top-2 90.1 %** (highest student item) |
| S3.5 | Simplified / other language | SC | 274 | both 31.8 %, simplified 25.9 %, German only 22.6 %, other lang 19.7 % |
| S3.6 | Missing feature (optional) | freetext | 105 | curriculum/Moodle, no paywall, images/diagrams |
| S3.7 | Build my own tutor would be helpful (1–4) | Likert | 274 | M = 2.78, SD 0.91, top-2 64.6 % |

**Findings.** **S3.4 is the headline of the entire student survey:** near-unanimous demand
(90 % top-2, M = 3.53) that the tutor know *their* concrete lesson content. This is the
empirical anchor for retrieval over teaching materials (RAG over Moodle/curriculum), and free
text repeats it almost verbatim ("Wissen über den aktuellen Lehrplan" — knowledge of the
current curriculum; "Zugriff auf die Moodle Plattform" — access to the Moodle platform). On
*how* to help, students prefer guided diagnosis (ask-where-stuck 42 %, hint 29 %) over the full
solution (15 %), but the single most popular explanation-style answer is **"it depends — I
want to choose" (52 %)**, which is itself an argument for *configurable*, not fixed, pedagogy.
Appetite for authoring one's own tutor is moderate-positive (S3.7, 65 % top-2).

### 5.5 Cluster 4 — Learning effectiveness

| Item | Question (1→4 direction) | n | M | SD | top-2 |
|---|---|---|---|---|---|
| S4.1 | Learning aid → shortcut | 273 | 2.17 | 0.77 | 29.3 % |
| S4.2 | Understand better via AI than book/class | 273 | 2.91 | 0.83 | 71.1 % |
| S4.3 | Tutor should make me think, not hand answers | 273 | 3.03 | 0.79 | 79.9 % |
| S4.4 | AI improves my long-term learning | 273 | 3.04 | 0.79 | 78.8 % |
| S4.5 | Where AI helps most (top-3, MC) | 273 | — | — | instant help 65.6 %, exam prep 56.8 %, better explanations 54.2 % |

**Findings.** Students lean toward using AI as a genuine learning aid rather than a shortcut
(S4.1 M = 2.17, 71 % below midpoint), express strong belief in long-term benefit (S4.4, 79 %
top-2), and explicitly endorse a "make-me-think" tutor (S4.3, 80 % top-2). Taken at face value
this is a pedagogically encouraging self-image, but it sits in tension with the misuse data in
Cluster 5 (§5.9), and the endorsement of "think for myself" is statistically *independent* of
whether they treat AI as a shortcut (S4.1 ↔ S4.3 r = −0.20), i.e. wanting to be challenged does
not predict actually using AI deeply.

### 5.6 Cluster 5 — Risks, misuse, fairness

| Item | Question (1→4 direction) | n | M | SD | top-2 |
|---|---|---|---|---|---|
| S5.1 | Temptation to let AI do it all | 270 | 2.60 | 0.91 | 51.9 % |
| S5.2 | Use even if sometimes wrong, if usually good | 270 | 2.79 | 0.83 | 66.3 % |
| S5.3 | Verify AI answers (SC) | 270 | — | — | often/always 36.3 %; sometimes 38.1 %; rarely/never 25.5 % |
| S5.4 | Unfair if classmates use AI more | 270 | 1.71 | 0.96 | 21.5 % |
| S5.5 | Worried about over-dependence | 270 | 2.52 | 1.03 | 53.0 % |

**Findings.** This is the most revealing cluster. A small majority feels a real pull to
outsource work entirely (S5.1, 52 % top-2), two-thirds would tolerate an occasionally-wrong
tutor (S5.2), yet only **36 % verify answers often or always** (S5.3), the correctness /
over-reliance gap in numbers. About half worry about dependence (S5.5). Fairness, by contrast,
is a non-issue: students broadly reject the idea that differential AI use is unfair (S5.4,
M = 1.71, the lowest student mean), consistent with a culture where AI use is normalised.
Notably, worry about dependence is *negatively* associated with belief in long-term benefit
(S5.5 ↔ S4.4, r = −0.29), and temptation to over-use tracks treating AI as a shortcut
(S5.1 ↔ S4.1, r = 0.31), coherent, expected structure that lends the data credibility.

### 5.7 Cluster 6 — Privacy, transparency, trust

| Item | Question | Type | n | Headline |
|---|---|---|---|---|
| S6.1 | Important to enter no personal data (1–4) | Likert | 269 | M = 2.80, SD 1.10, top-2 60.2 % (bimodal) |
| S6.2 | Teacher reading anonymised class chats | SC | 269 | **uncomfortable 57.2 %**, only-if-informed 19.0 %, don't care 16.7 %, good 7.1 % |
| S6.3 | Acceptable purposes for teacher read-along | MC | 269 | spot misunderstandings 43.9 %, **"none, keep private" 41.6 %**, adapt teaching 38.3 % |
| S6.4 | Trust more with sources shown | SC | 269 | yes, sources matter 54.3 %; nice-but-not-decisive 35.3 %; no 7.4 % |

**Findings.** Privacy preferences are clear and somewhat polarised: avoiding personal-data
entry is important to a majority but the distribution is bimodal (SD 1.10, many "very
important", a sizeable "unimportant" tail). Students are distinctly uncomfortable with teachers
reading their chats **even when anonymised** (57 %), and a plurality would permit *no*
read-along purpose at all (41.6 %). Source attribution materially increases trust for a
majority (S6.4, 54 %). The product reading is unambiguous: **transparency (sources) yes;
individual chat visibility no**, favour aggregated insights.

### 5.8–5.9 Cluster 7 and free-text themes

The three free-text items (S7.1 no-go, S7.2 biggest value, S7.3 anything else) plus S2.5/S3.6
were thematically coded (keyword-based; noise responses such as "Keine Ahnung"/"Nein" were
frequent and are discussed in §7).

**S2.5 — "What must a school tutor do better than ChatGPT?" (n = 279).** Leading themes:
cost/limits/paywall (~15 %), curriculum/materials/Moodle grounding (~13 %), and factual
correctness (~12 %). Representative: *"Zugriff auf die Moodle Plattform würde einen enormen
Unterschied machen"* (access to Moodle would make an enormous difference); *"Wissen … die
sachen die im unterricht kommen"* (knowing the things that come up in class).

**S7.1 — absolute no-go (n = 267).** Correctness/wrong answers dominate (~21 %), followed by
data collection/privacy (~18 %) and teacher surveillance (~11 %); cost/paywall also recurs
(*"Eine Paywall."*). **S7.2 — biggest personal value (n = 267):** curriculum-fit/lesson
relevance (~11 %), instant availability (~6 %), and personalisation/exam prep. The same two
ideas, *be correct* and *know my lessons*, define both the floor (no-gos) and the ceiling
(value).

---

## 6. Teacher Results (n = 34)

> **Small-sample caveat.** With n = 34 (most items n = 33), each respondent ≈ 3 percentage
> points. Percentages are tendencies, not precise estimates; no significance testing is
> reported. Teachers also skew to programming (55.9 % teach `Programmierunterricht`), mirroring
> the student branch bias.

### 6.1 Cluster 0–1 — Profile and current usage

| Item | Question | n | Headline |
|---|---|---|---|
| L0.1 | Subjects taught | 34 | programming 55.9 % (+ many "other"); broad GE tail |
| L0.2 | Years teaching | 34 | 5–15 y 52.9 %, >15 y 35.3 %, <5 y 11.8 % |
| L0.3 | Confidence with digital tools (1–4) | 34 | **M = 3.38**, SD 0.65, top-2 91.2 % |
| L0.4 | Already used AI *with students* | 34 | regularly 23.5 %, tried 35.3 %, plan to 35.3 %, no/never 5.9 % |
| L1.1 | Tools used professionally | 34 | ChatGPT 82.4 %, MS Copilot 50.0 %, Gemini 26.5 % |
| L1.2 | Frequency (professional) | 34 | several/wk 47.1 %, daily 17.6 % |
| L1.3 | Main purposes (top-3) | 34 | lesson prep 76.5 %, creating tasks/tests 61.8 %, own PD 50.0 % |
| L1.4 | Paid account | 34 | none 47.1 %, privately paid 41.2 %, school-provided 11.8 % |
| L1.5 | Students' AI usage | 34 | **"most use AI regularly" 94.1 %** |

**Findings.** The teacher panel is experienced (88 % ≥ 5 years), digitally confident
(L0.3 M = 3.38), and already AI-using for their own work, but **classroom** use with students
is still emergent: under a quarter use it regularly and ~71 % have only tried it or merely plan
to. They are near-unanimous (94 %) that students already use AI regularly, so they are not
debating *whether* AI is present, only how to channel it. Only 12 % have a school-provided
account: institutional provisioning is essentially absent today, which the platform directly
addresses.

### 6.2 Cluster 2–3 — Willingness and requirements

| Item | Question | n | Headline |
|---|---|---|---|
| L2.1 | Would integrate into teaching | 33 | regularly 39.4 %, occasionally 39.4 %, rarely 21.2 % — **0 % never** |
| L2.2 | Scenarios (top-3) | 33 | remedial for weaker students 57.6 %, in-class practice 57.6 %, test prep 45.5 % |
| L2.3 | Willing to build Workspaces | 33 | yes sensible 39.4 %; only with templates 27.3 %; only if <15 min 27.3 %; too much effort 6.1 % |
| L2.4 | Availability | 33 | permanent 51.5 %, only at defined times 33.3 % |
| L2.5 | Why use it (MC+ft) | 33 | foster independent learning 78.8 %, individual support 57.6 %, modernise 48.5 % |
| L3.1 | Most important properties (top-3) | 33 | **factual correctness 84.8 %**, curriculum/material fit 51.5 %, easy Workspace creation 39.4 % |
| L3.2 | Curriculum alignment important (1–4) | 33 | M = 2.91, SD 0.88, top-2 69.7 % |
| L3.3 | Help when stuck | 33 | stepwise, solution only after attempts 36.4 %; **per-Workspace configurable 33.3 %**; hints only 24.2 % |
| L3.4 | Debugging support in class (opt., 1–4) | 27 | M = 2.74, top-2 55.6 % |
| L3.5 | Integrations | 33 | **LMS/Moodle/Teams 66.7 %**, dev tools/GitHub 36.4 % |
| L3.6 | Simplified/other language | 33 | nice extra 39.4 %, German only 36.4 %, important for some 18.2 % |

**Findings.** Willingness is essentially universal in principle (**0 % would never** integrate
a tutor), but conditional in practice: a combined ~55 % will build Workspaces only **if it is
fast or template-based**, a direct mandate for low-effort authoring and starter templates.
Teachers' #1 requirement is **factual correctness (84.8 %)**, the same red line as students,
followed by curriculum/material alignment (matching student S3.4). On Socratic behaviour,
teachers split between built-in stepwise help and **per-Workspace configurability (33 %)**,
echoing students' "let me choose": configurability is the consensus resolution on both sides.
Two-thirds want LMS/Moodle integration, corroborating the curricular-grounding signal from a
systems angle.

### 6.3 Cluster 4–7 — Pedagogy, risk, privacy, framework

| Item | Question (1→4 unless noted) | n | M / headline |
|---|---|---|---|
| L4.1 | Tutor supports differentiation | 33 | M = 3.09, top-2 81.8 % |
| L4.2 | Tutor strengthens problem-solving (vs copying) | 33 | M = 2.82, top-2 54.5 % (split) |
| L4.3 | Biggest pedagogical value (top-3, MC) | 33 | support weaker 69.7 %, personalised learning 63.6 %, stretch strong 51.5 % |
| L4.4 | Should deliberately ask back / prompt thinking | 33 | yes (fosters thinking) 60.6 %; yes if configured 24.2 %; no 3.0 % |
| L5.1 | Risk of wrong answers → misconceptions | 33 | M = 2.76, top-2 63.6 % |
| L5.2 | Need uniform rules if AI allowed | 33 | school-wide 45.5 % vs per-teacher 45.5 % (split) |
| L5.3 | Acceptable AI in exams | 33 | allowed if task designed for it 51.5 %; none 36.4 % |
| L6.1 | Traceability of student use important | 33 | M = 3.12, top-2 81.8 % |
| L6.2 | Data teachers should see | 33 | **aggregated stats 75.8 %**; anonymised chats 36.4 %; individually-attributed chats 6.1 % |
| L6.3 | Mandatory privacy requirements | 33 | GDPR/EU processing 69.7 %, no third-party sharing 63.6 %, no permanent chat storage 48.5 % |
| L6.4 | Who decides privacy settings | 33 | **school leadership centrally 69.7 %** |
| L7.1 | I feel prepared to guide AI use | 33 | M = 2.73, top-2 60.6 % |
| L7.2 | Need teacher training on pedagogical AI | 33 | **M = 3.27, top-2 84.8 %** |
| L7.3 | Know the legal framework (AI Act, GDPR) | 33 | basic 36.4 %, heard-of 39.4 %, none 18.2 %, **well-informed 6.1 %** |
| L7.4 | Absolute no-go (freetext) | 33 | wrong answers; handing out solutions; fake "friendship"/sycophancy |

**Findings.** Teachers strongly endorse the tutor for **differentiation and supporting weaker
students** (L4.1 M = 3.09; L4.3 remedial 69.7 %), a markedly equity-oriented framing distinct
from students' exam-prep framing. They are more skeptical on whether it genuinely builds
problem-solving (L4.2 split), take hallucination risk seriously (L5.1, 64 % top-2), and on
oversight want **aggregated statistics, not individual chats** (L6.2), which is *compatible*
with students' privacy preferences rather than in conflict. Governance is seen as a central,
institutional responsibility (privacy decided by school leadership 69.7 %; GDPR/EU processing
mandatory 69.7 %). The clearest gap is **preparedness**: feeling prepared is only middling
(L7.1 M = 2.73), legal-framework knowledge is weak (only 6 % well-informed), and the demand for
training is the second-highest teacher mean (L7.2 M = 3.27). Free-text no-gos add a nuance
absent from the closed items: a sharp rejection of AI that builds a pseudo-personal
relationship or fails to refuse abusive prompts.

---

## 7. Cross-Question Findings

**Convergences (strong, design-relevant).**

- **Correctness is the bilateral red line.** Students' top no-go (≈21 % of free text) equals
  teachers' top requirement (84.8 %). Factual fidelity, honest "I don't know", and verifiable
  sources (students S6.4 54 %, teachers value sources in free text) are *acceptance* criteria,
  not nice-to-haves.
- **Curricular grounding from both sides.** Students' single highest item (S3.4, M = 3.53)
  meets teachers' #2 property (curriculum/material fit 51.5 %) and 67 % wanting LMS/Moodle
  integration. This is the most actionable, mutually reinforced requirement in the dataset.
- **Configurability resolves the pedagogy question on both sides.** Students' top explanation
  preference is "let me choose" (52 %); teachers' help-mode answers and L4.4 both feature
  "configurable per Workspace". Neither group wants a single hard-coded Socratic mode; they
  want it *settable*, which is exactly the platform's teacher-configurable thesis.
- **Privacy expectations are compatible.** Students reject individual read-along (57 %
  uncomfortable, 42 % "no purpose"); teachers want aggregated stats (76 %), not individual
  chats (6 %). Aggregated, anonymous class insight satisfies both: there is no real conflict
  here, only a need to implement it correctly.

**Contradictions / tensions (worth reporting honestly).**

- **"Make me think" vs. actual behaviour.** Students endorse a challenging tutor (S4.3, 80 %
  top-2) and mostly use AI as a learning aid (S4.1), yet 52 % feel real temptation to outsource
  work (S5.1) and only 36 % verify answers (S5.3). The aspiration and the behaviour diverge;
  the platform's configurable pedagogy plus teacher oversight is the intended bridge.
- **Tolerance of error vs. error as #1 no-go.** Two-thirds would keep using an
  occasionally-wrong tutor (S5.2) while wrong answers are simultaneously the most cited no-go
  (S7.1). Read together: students will tolerate *rare* errors in a generally reliable tool but
  treat *systematic* unreliability as disqualifying, a tolerance band, not a paradox.
- **Teacher willingness vs. preparedness.** 100 % willing to use, 0 % "never", but only ~24 %
  currently use AI with students and only 6 % know the legal framework. Adoption is gated by
  training and institutional support, not by attitude.

**Notable correlations (students, Pearson r on 4-point items; n ≈ 270–273).**

- S5.5 (dependence worry) ↔ S4.4 (long-term benefit): **r = −0.29**, skeptics worry more.
- S5.1 (temptation to over-use) ↔ S4.1 (uses AI as shortcut): **r = +0.31**, coherent.
- S1.6 (current AI share) ↔ S4.1 (shortcut orientation): **r = +0.32**.
- S3.4 (wants curriculum grounding) ↔ S4.4 (believes in benefit): **r = +0.30**, the most
  engaged students are also the ones demanding curricular fit.
- S4.1 (shortcut) ↔ S4.3 (wants to be challenged): **r = −0.20**, wanting challenge does not
  translate into deep use. These associations are modest but in expected directions, supporting
  internal validity.

**Subgroup check.** Demand for curriculum grounding (S3.4) is uniformly high across branches
(Informatik M = 3.54, n = 200; "other" 3.69, n = 54; sciences 3.16, n = 19), i.e. **not** an
artefact of the IT majority: it is a school-wide expectation. (The single "business" student
makes that cell uninterpretable.)

**Teacher associations** (descriptive only, n = 33): L4.1 ↔ L4.2 (differentiation ↔
problem-solving belief) r = 0.54; L0.3 ↔ L7.1 (digital confidence ↔ feeling prepared) r = 0.48,
more confident teachers feel more prepared, reinforcing that targeted training could move the
preparedness gap.

---

## 8. Critique of the Questionnaire

The following is Rainer Stropek's own honest appraisal of the instrument. It is constructive: the survey is
well-designed for its purpose, but several limitations bound the conclusions.

1. **Sample bias dominates external validity.** 72.9 % informatics students and 56 %
   programming teachers from a single HTL make this a study of a tech-fluent population. It is
   excellent for designing a CS-school deployment but cannot be generalised to general-education
   secondary schools. The tool list itself (Claude Code, GitHub Copilot prominent) signals this.
   This remains the headline caveat.

2. **Single-site, supervised, in-class administration.** Clustered class sessions raise the
   chance of context/peer effects and mild social-desirability bias (e.g. the pedagogically
   "correct" endorsement of "make me think", S4.3, may be inflated relative to behaviour, and
   the S5.x data hint at exactly that gap). Anonymity/pseudonymity mitigates but does not
   remove this.

3. **4-point forced-choice scale.** Removing the neutral midpoint (a deliberate design choice)
   forces a lean and sharpens signals, but it prevents respondents from expressing genuine
   neutrality and rules out true-neutral interpretation; all means must be read against 2.5, and
   bimodal items (e.g. S6.1, SD 1.10) can share a mean with very different distributions. SDs
   and full distributions (reported here) are therefore essential, not optional.

4. **Free-text noise and uneven richness.** A large share of free-text answers are
   non-substantive ("Keine Ahnung", "Nein", "Ka", emoji), especially among younger students,
   inflating denominators and limiting quantitative thematic claims. The keyword coding is
   indicative only; a formal qualitative coding (two raters, inter-rater reliability) would be
   needed for publishable thematic percentages. Optional free-text items (S3.6 37 %, S7.3 24 %,
   L3.7 18 %) are too sparse for strong claims.

5. **Self-report only.** Every behavioural claim (verification frequency, AI share of work,
   temptation) is self-reported with no behavioural ground truth; the internal contradictions
   in Cluster 5 suggest self-report should be treated cautiously, not as measured behaviour.

6. **Construct overlap / leading framing.** Several items presuppose the product vision
   (Workspaces, "build your own tutor", per-Workspace configurability), which can lead
   respondents toward the designers' framing. Some constructs overlap (e.g. S2.2 vs S4.5
   scenario/benefit; S1.4 vs S2.2), risking redundancy; the redundancy check documented during
   instrument design addressed but did not fully eliminate this.

7. **Small and structurally different teacher sample.** n = 34 yields ±~3-point granularity;
   several teacher items split 50/50 (L5.2) where n is simply too small to resolve. Teacher
   results are best treated as qualitative tendencies. The two variants are also not directly
   comparable item-by-item (different wording/anchors), so student–teacher "agreement" is
   thematic, not statistical.

8. **No weighting / no non-response model.** The 18 registered-but-empty accounts and the
   incremental drop-off are not modelled; later-cluster results rest on a slightly smaller,
   possibly more motivated, subsample. Reporting per-item n (as done here) is the right
   mitigation, but a sensitivity check on completers-vs-all would strengthen the analysis.

None of these undermine the survey's core purpose, eliciting requirements and acceptance
signals, but each is a genuine bound on the conclusions.

---

## 9. Conclusion

Within a tech-fluent single-school population, the survey delivers a clear, internally
coherent, and bilaterally reinforced requirements signal. Students and teachers independently
converge on **curriculum-grounded, factually reliable, configurable tutoring with transparent
sources and aggregated (non-surveillance) oversight**, and teachers add an equity emphasis
(supporting weaker students) plus an explicit need for **training and institutional support**.
The most important tensions, aspiration vs. behaviour around self-directed learning, and
willingness vs. preparedness among teachers, are precisely the problems a teacher-configurable
platform with oversight is positioned to address. The findings therefore validate Novedu's core
design bets and our MVP scoping decision, while the sample bias and self-report limitations set
honest bounds on how far the conclusions generalise beyond a computer-science HTL.

---

## Appendix A — All Likert items ranked

**Students** (1→4 direction in parentheses):

| Rank | Item | M | SD | n | 1 → 4 |
|---|---|---|---|---|---|
| 1 | S3.4 | 3.53 | 0.77 | 274 | unimportant → very important (tutor knows my lessons) |
| 2 | S0.3 | 3.25 | 0.72 | 284 | not good → very good (digital skill) |
| 3 | S4.4 | 3.04 | 0.79 | 273 | not at all → very much (improves long-term learning) |
| 4 | S4.3 | 3.03 | 0.79 | 273 | disagree → agree (should make me think) |
| 5 | S4.2 | 2.91 | 0.83 | 273 | disagree → agree (understand better via AI) |
| 6 | S6.1 | 2.80 | 1.10 | 269 | unimportant → very important (no personal data) |
| 7 | S5.2 | 2.79 | 0.83 | 270 | never → definitely (use even if sometimes wrong) |
| 8 | S3.7 | 2.78 | 0.91 | 274 | not helpful → very helpful (build own tutor) |
| 9 | S5.1 | 2.60 | 0.91 | 270 | very small → very large (temptation to over-use) |
| 10 | S5.5 | 2.52 | 1.03 | 270 | disagree → agree (worried about dependence) |
| 11 | S1.6 | 2.41 | 0.76 | 282 | no role → does most (current AI share) |
| 12 | S4.1 | 2.17 | 0.77 | 273 | learning aid → shortcut |
| 13 | S5.4 | 1.71 | 0.96 | 270 | disagree → agree (unfair if others use more) |

**Teachers** (n ≈ 33):

| Rank | Item | M | SD | 1 → 4 |
|---|---|---|---|---|
| 1 | L0.3 | 3.38 | 0.65 | very unsure → very confident (digital tools) |
| 2 | L7.2 | 3.27 | 0.72 | disagree → agree (need training) |
| 3 | L6.1 | 3.12 | 0.78 | unimportant → very important (traceability) |
| 4 | L4.1 | 3.09 | 0.68 | disagree → agree (supports differentiation) |
| 5 | L3.2 | 2.91 | 0.88 | unimportant → very important (curriculum alignment) |
| 6 | L4.2 | 2.82 | 0.98 | disagree → agree (strengthens problem-solving) |
| 7 | L5.1 | 2.76 | 0.90 | very low → very high (misconception risk) |
| 8 | L3.4 | 2.74 | 0.94 | not useful → very useful (in-class debugging; n=27) |
| 9 | L7.1 | 2.73 | 0.84 | disagree → agree (feel prepared) |

*All figures computed directly from `responses.json` (2026-06-28 export). Likert means use the
sample SD; choice percentages are of respondents answering that item (per-item n shown).
Multiple-choice percentages exceed 100 % by design.*
