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

- **Explain the school context for an international audience.** Near the intro
  heading: "HTL", "EQR 5", "VET School", plus a "Verweis" (reference) cue. HTL
  should be explained as a VET (vocational education and training) school at
  EQF/EQR level 5, so non-Austrian readers know what kind of school this is, with
  a cross-reference where it fits.
  - **Response:** Strongly agree, this is cheap and genuinely useful for an IMCL
    audience. One sentence does it: HTL is a five-year technical VET college with
    an exit qualification at EQF level 5, with a reference to the EQF framework. It
    clears up a real comprehension gap for almost no page cost.

- **Explain the name "Novedu."** A margin note (p. 2) points at the project name.
  Right now the origin of "Novedu" isn't explained anywhere. **[uncertain]** The
  same note also looks like it says "Test", which might be a reminder that the
  name or the idea still needs to be nailed down.
  - **Response:** Fine as a quick footnote ("nov" + "edu"), but honestly low
    priority. A name gloss is nice to have, not something a reviewer will flag. Do
    it only if there's space left over. Better to check the "Test" reading with
    Andreas before reading too much into it.

- **Strengthen the research framing.** "Research" is written next to the
  contributions and design-questions block (p. 2), which reads as a nudge to make
  the scholarly contribution stand out more.
  - **Response:** Neutral to agree, and this probably overlaps with the "RQ?" note
    above. Both are really asking for the contribution to be easier to see up
    front. Treat them as one fix rather than two edits: a sharper contributions
    sentence in the intro likely covers both.

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

## Tables

- **Shorten the Table 3 caption.** "Tabellenbeschreibung kurz" next to Table 3
  (p. 7). The caption is too long and should be trimmed.
  - **Response:** Agree, easy win. Move the "read descriptively, one pass = 2.8
    points" caveat down into the body text and keep the caption to a single line.
    Bonus: it frees a little space to offset the architecture figure above.

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
