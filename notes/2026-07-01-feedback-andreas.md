# Feedback Andreas – Novedu Work-in-Progress Paper

Handwritten notes on the printed draft (SRV-PRINT scan, 9 pages). The notes are
in German. Below is a reading of what each one is getting at, plus a draft
**Response** per item as a starting point. The responses are just opinions to
argue with, not decisions. Where the handwriting is hard to read, it's flagged as
**[uncertain]**.

One thing to keep in mind throughout: this is an 8-page LNCS work-in-progress
paper. A lot of these suggestions (more figures, more references, hypothesis
framing) all want the same scarce space, so the responses try to weigh value
against how many lines it costs.

## Content & framing

- **State the research question(s) explicitly.** A "RQ?" note sits next to the
  abstract, so the research questions should probably be more visible, maybe
  already in the abstract and not only as DQ1–DQ3 in the introduction.
  - **Response:** Mostly agree. The questions are already there as DQ1–DQ3, so
    this is about visibility, not substance. Cheapest fix is one sentence in the
    abstract that names the three questions. Could also rename "design questions"
    to "research questions" so reviewers see a familiar label, though "design
    questions" was a deliberate choice for a design-case WIP. Low effort, worth it.
    2026-07-02: Rename DQ -> RQ

- **Frame the central claims as hypotheses.** "Hypothesen" is written next to the
  paragraph with the central position and contributions (p. 2). The idea seems to
  be to state the position as testable hypotheses rather than as a stance.
  - **Response:** Would push back a bit here. The core claim (a school can align
    tutors institutionally instead of by training) is a design thesis, not really
    something you test empirically, and squeezing it into H1/H2 form risks
    over-claiming for a WIP that only has one-topic evidence. DQ3 is the exception:
    whether a small in-region model can hold the Socratic behaviour is genuinely
    testable, and turning just that one into an explicit hypothesis tied to Table 3
    would answer the note without distorting the rest. Suggest doing it narrowly.
    2026-07-02: No hypotheses because of WIP

- **Explain the school context for an international audience.** Near the intro
  heading: "HTL", "EQR 5", "VET School", plus a "Verweis" (reference) cue. HTL
  should be explained as a VET (vocational education and training) school at
  EQF/EQR level 5, so non-Austrian readers know what kind of school this is, with
  a cross-reference where it fits.
  - **Response:** Strongly agree, this is cheap and genuinely useful for an IMCL
    audience. One sentence does it: HTL is a five-year technical VET college with
    an exit qualification at EQF level 5, with a reference to the EQF framework. It
    clears up a real comprehension gap for almost no page cost.
    2026-07-02: "Higher Technical Vocational Education in Austria"

- **Explain the name "Novedu."** A margin note (p. 2) points at the project name.
  Right now the origin of "Novedu" isn't explained anywhere. **[uncertain]** The
  same note also looks like it says "Test", which might be a reminder that the
  name or the idea still needs to be nailed down.
  - **Response:** Fine as a quick footnote ("nov" + "edu"), but honestly low
    priority. A name gloss is nice to have, not something a reviewer will flag. Do
    it only if there's space left over. Better to check the "Test" reading with
    Andreas before reading too much into it.
    2026-07-02: Novedu must be described (background, not just name), Novedu is the name of the software.

- **Strengthen the research framing.** "Research" is written next to the
  contributions and design-questions block (p. 2), which reads as a nudge to make
  the scholarly contribution stand out more.
  - **Response:** Neutral to agree, and this probably overlaps with the "RQ?" note
    above. Both are really asking for the contribution to be easier to see up
    front. Treat them as one fix rather than two edits: a sharper contributions
    sentence in the intro likely covers both.
    2026-07-02: Covered with DQ -> RQ

## Figures

- **Add more figures, the paper is too text-heavy.** "Bilder" / "Abb."
  (Abbildung) shows up on more than one page (p. 2 and p. 4), with a hand-sketched
  box-and-arrow diagram in the p. 2 margin. The clear ask is for more visuals,
  especially an **architecture / system diagram**, and something in Section 4.
  - **Response:** Strongly agree, probably the highest-value change on the list. A
    single architecture diagram (teacher-authored YAML fragments, the assembly
    pipeline, the in-region model, the activities on top) would do more work than
    the same content in prose and directly fixes the "wall of text" feeling. The
    catch is page cost, so one well-made system figure rather than several, and
    ideally let it replace some Section 4 text instead of adding to it.
  - **Status:** The student team has already been contacted about the architecture
    diagram. This could be a great opportunity for them to contribute, since they
    built the system and know the internals best.

## Tables

- **Shorten the Table 3 caption.** "Tabellenbeschreibung kurz" next to Table 3
  (p. 7). The caption is too long and should be trimmed.
  - **Response:** Agree, easy win. Move the "read descriptively, one pass = 2.8
    points" caveat down into the body text and keep the caption to a single line.
    Bonus: it frees a little space to offset the architecture figure above.
    2026-07-02: Table caption must be single line, describe in text

## References

- **Add more sources.** "mehr Quellen" at the end of the reference list (p. 9).
  The paper needs a broader set of citations.
  - **Response:** Agree, but selectively. Six references is thin for the claims
    being made. The ones actually worth adding: GDPR / EU AI Act primary sources
    for the compliance claims, at least one more recent LLM-in-education or
    classroom-deployment study, and a retrieval/RAG reference to back the
    "retrieval over LMS materials" next step. No padding for the sake of the count,
    just fill the spots where a claim currently stands uncited.

## Scope – split content across papers

- **Requirements survey may belong in a follow-up paper.** "evtl. nächstes Paper"
  next to the requirements-survey section (p. 3). The survey material could move
  into, or be expanded as, a separate paper instead of sitting in full here.
  - **Response:** Agree, and it's actually useful strategically. The n=318 survey
    is a contribution in its own right that the WIP has to compress hard. A
    dedicated paper could give the methodology and per-item results the room they
    deserve. For this paper, keep only the survey signals that drive the design
    (Table 1) and point to the fuller version as future work. Less page pressure
    now, and it sets up the next publication.
    2026-07-02: Up to now pretest with IT bias, next academic year broaden questionnaire to additional schools, goal: remove bias

## Ideas for the next paper

At the bottom of p. 9 there's a list of possibilities ("nächstes Paper –
Möglichkeiten") for a follow-up:

- **AI and IoT**
- **AI and AR**
- **AI in CAD and Engineering/Experimental Design**, with two tools named:
  **Creo** and **Onshape**

These are directions for a later paper, not changes to this one.
  - **Response:** Agree they're out of scope here, so park them. They look like
    Andreas's engineering-domain take on the same idea, applying the
    configuration-over-training thesis to CAD, AR and IoT teaching, which is a
    promising angle. Worth keeping in a separate "future directions" backlog. Maybe
    one line in Next Steps saying the architecture might transfer to
    engineering-design education, without going into detail.

---

*Note: anything marked **[uncertain]** will be checked with Andreas directly.*

---

# Response to "mehr Quellen": RAG, prompt fragments, and grep-based retrieval

(Added by Rainer, research for papers with AI support)

Andreas, on the "mehr Quellen" note, here's a concrete place to add citations that
also strengthens the paper. It lines up with what we already call the "most
important gap": students want the tutor to know their concrete lesson material,
and the MVP only supplies that through teacher-authored prompt fragments, not
retrieval over the LMS. There's a real architecture story behind that gap, and
telling it brings the references in naturally.

The story is the v1 -> v2 shift. Novedu v1 (the Open WebUI prototype) leaned
heavily on RAG to pull curriculum material into the tutor. Novedu v2 (the coded
MVP) deliberately dropped RAG and instead builds on modular, teacher-authored
prompt fragments, so curriculum context lives in the fragments rather than in a
retrieval pipeline. The future-work angle is then to evaluate whether a
prompt-based approach, maybe extended with a lightweight tool-based grep step over
curriculum material, gives meaningful, grounded responses, and whether that holds
up against a full RAG pipeline in our setting.

I think this is worth doing. It explains the v1 -> v2 shift as a deliberate design
decision rather than an accident, and it frames the retrieval gap more honestly
than "we should add RAG": the literature is genuinely split on whether RAG is the
right lever once context windows are large and cheaper retrieval (keyword/grep,
agentic search) is on the table, so putting Novedu inside that open question is
defensible for a WIP. Two things to watch: it needs to stay tight given the page
budget (a short paragraph in the design/history section plus a few sentences in
Next Steps, not a full RAG related-work section).

See `references-rag-citations.bib` for possible citations:

- **`lewis2020rag`** (Lewis et al., NeurIPS 2020) is the canonical RAG paper. We
  need it to say v1 was RAG-based and to define RAG at all.
- **`agrawal2024mindfulrag`** (Mindful-RAG, 2024) studies RAG failure modes and
  backs the v1 -> v2 decision: it helps explain why moving away from a naive RAG
  pipeline was reasonable.
- **`li2025lara`** (LaRA, 2025) benchmarks RAG against long-context LLMs and lands
  on "no silver bullet". It's the strongest single citation for the future-work
  framing, since it supports treating prompt/long-context vs retrieval as an open
  trade-off rather than a solved question.
- **`liu2024lostmiddle`** (Lost in the Middle, TACL 2024) shows long-context models
  use the middle of the context poorly, which is why we can't just stuff all
  curriculum material into a prompt and why the prompt-only approach has limits
  worth testing.
- **`sen2026grep`** ("Is Grep All You Need?", 2026) is the most on-point citation
  for the tool-based grep idea. It's a very recent preprint, so we should lean on
  it as a pointer to an emerging approach, not as settled evidence.
- **`chen2021spar`** (SPAR, 2021) covers dense retrieval imitating sparse/keyword
  retrieval, useful for the grep angle. Optional: keep it if the grep discussion
  needs depth, drop it first if space is tight.
- **`wang2026greprag`** (GrepRAG, 2026) does grep-like retrieval but for code
  completion, so the domain is a bit off. Optional: only if we want a second grep
  reference, and we'd flag the domain difference.

2026-07-02: Take 2-3 that fit nicely

## Generals

2026-07-02: Not "section", but "chapter" (for references)

2026-07-02: "A general-purpose chatbot is a poor default for two reasons." is not clear.