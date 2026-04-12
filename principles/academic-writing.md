# Academic Writing Principles

Distilled from thesis supervision and author self-review feedback.
Canonical reference — all agents and workflows should consult this file.
Calibrated for: WNE UW licencjat, Word format, economics and political science.
Organized into 4 categories that map to agent specializations.

---

## A. Structure & Narrative

*Used by: thesis-macro-auditor (all 6 structural agents), thesis-section-writer (section-drafter-wne), thesis-analyzer (hypothesis-alignment-checker, redundancy-checker), thesis-reviewer (all review agents)*

### A1. Recursive Consistency Checks
For each chapter, section, and subsection — verify that terminology, section summary/intro, actual flow, and argument all match. If a section says "we discuss X, Y, Z," the subsections must cover exactly X, Y, Z in that order.

**Common violations**: Section intro promises three topics but delivers two; subsection order doesn't match the intro's enumeration; terminology drifts between sections (e.g., using "polityka fiskalna" in one section and "wydatki publiczne" in another to refer to the same variable).

### A2. Logical Chaining with Transitions
Every paragraph, section, and chapter should be "chained" logically. The end of one section should motivate the start of the next. Never leave two adjacent paragraphs without a transition. Section-level transitions are necessary but insufficient — paragraph-to-paragraph connections are the more common failure mode.

**Common violations**: Abrupt topic shifts between paragraphs; sections that start with no connection to what came before; chapter endings that don't set up the next chapter; adjacent paragraphs on related subtopics connected only by thematic proximity rather than explicit bridging.

### A3. Close Every Paragraph
The last sentence of a paragraph should conclude, synthesize, or motivate the next paragraph — not trail off. A strong closer draws an implication, states a principle, or poses a question the next paragraph answers.

**Common violations**: Paragraphs ending with "co zostanie omówione w następnej sekcji" or a bare citation; final sentences that introduce new information without resolution; paragraphs that simply stop after the last piece of evidence.

### A4. Claim-First Exposition
State the conceptual contribution or objective before diving into details. The reader should know *what* and *why* before *how*. The first 1–2 sentences of each section or subsection should declare the goal before presenting data or procedures.

**Common violations**: Sections that open with data tables or methodology details before stating the purpose; subsections that dive into "how" without first explaining "what" or "why"; method descriptions that defer the motivation to the end.

### A5. Goal-Problem-Solution Rhythm
Every section — from the full thesis down to individual subsections — should follow a Goal-Problem-Solution (GPS) rhythm. **Goal**: what are we trying to establish? **Problem**: what makes this analytically challenging or what gap are we addressing? **Solution**: how does this section address it? Sections that jump to solutions without establishing the goal and problem lose the reader's motivation.

**Common violations**: Empirical sections that present findings without first stating what research question each finding addresses; introduction paragraphs that list contributions without establishing the gap; subsections that describe "how" without "why this is needed."

### A6. The Central Argument — One Key Claim Per Thesis
Every thesis should orbit a single central argument — the claim the reader takes away. All sections, analyses, and case studies should serve this central message. If you cannot state the thesis's contribution in one or two sentences, the focus is unclear.

**Common violations**: Theses that present multiple loosely connected analyses without a unifying claim; abstracts that list topics covered rather than stating what was found; conclusion sections that enumerate results rather than synthesizing them into a coherent answer to the research question.

---

## B. Prose & Style

*Used by: thesis-analyzer (efficiency-auditor, claim-strength-checker), prose-polisher-wne, thesis-editor, thesis-reviewer (all review agents), thesis-section-writer (section-drafter-wne)*

### B1. Avoid Exhaustive-Sounding Enumerations
When listing examples, use "such as" or "takich jak" rather than "for" / "dla" to avoid implying the list is complete.

**Common violations**: "dla X, Y, Z" when the list is illustrative; enumerations that imply completeness when the list is partial.

### B2. Avoid Negation-Contrast Structures
Rephrase "not X, but Y" / "nie dlatego... lecz dlatego..." positively. These are a strong AI-writing marker. E.g., "nie dlatego, że zjawisko jest rzadsze, ale dlatego, że nikt go nie zdefiniował" → "dlatego, że nikt nie skonceptualizował tego jako kategorii, niezależnie od częstości jego występowania."

**Common violations**: Sentences structured around negation before stating the actual point; "not only... but also" used to combine two claims that should be separate sentences; "nie X, lecz Y" constructions throughout.

### B3. Match Discussion Tone to Thesis Voice
Discussion and findings sections should flow as analytical prose (claim → evidence → mechanism → example → implication), not flat report-style enumeration. The author's voice is analytical, hedged appropriately to the evidence, active where possible.

**Common violations**: Bullet-point style findings in the discussion; flat enumeration without analytical depth; sections that read like lists rather than arguments; switching unexpectedly between formal and informal registers.

### B4. One Idea Per Sentence
Split sentences that pack multiple distinct claims. Each sentence should advance exactly one point. If a sentence needs "while", "unlike", "podczas gdy", or a semicolon to stitch together separate claims, it should be two sentences.

**Common violations**: Sentences with "podczas gdy wcześniejsze badania wskazywały na X, niniejsza praca wykazuje Y i osiąga Z"; compound sentences where each half could stand alone as a distinct contribution.

### B5. Calibrated Confidence Language
Use assertive language for empirical facts and hedged language for causal explanations or interpretations. Do not hedge established facts or assert unproven mechanisms.

Assertive: "wyniki wskazują", "analiza wykazuje", "dane potwierdzają".
Hedged: "wyniki sugerują", "analiza jest zgodna z tezą, że", "nie można definitywnie ustalić przyczyny, jednak...".

**Common violations**: "powoduje" / "wynika z" / "prowadzi do" used without causal evidence; "może" / "być może" modifying reported numerical results; mixing confidence levels within the same sentence; "oczywiście", "bezsprzecznie", "jasno widać" used without evidence.

### B6. Ruthless Conciseness
Every word must earn its place. Cut filler phrases, compress redundant clauses, prefer direct constructions. A paragraph that says in 150 words what could be said in 80 is not thorough — it is wasteful.

**Common violations in Polish academic prose**: "należy podkreślić, że" (cut entirely); "w kontekście" used as filler; "odgrywa ważną rolę w" → "wpływa na"; "ze względu na fakt, że" → "ponieważ"; "w celu zbadania" → "aby zbadać"; restating in the conclusion what was already said in the abstract and introduction with only cosmetic changes.

### B7. AI-Writing Tell Detection
Actively scan for and eliminate patterns that mark text as AI-generated. Common tells:
1. "nie X, lecz Y" negation-contrast structures (see B2)
2. Words: "zgłębiać", "krajobraz" (as metaphor), "wieloaspektowy", "paradygmat", "tapestry"
3. Sentences starting with "Ponadto", "Co więcej", "Dodatkowo" in sequence
4. Formulaic transitions: "Bazując na powyższym", "Idąc o krok dalej"
5. Hollow intensifiers: "kluczowy", "istotny", "niezbędny" used interchangeably throughout
6. Mirroring the user's phrasing back verbatim
7. Overly balanced "z jednej strony / z drugiej strony" structures used repeatedly

Replace with direct, specific language grounded in the thesis's actual evidence.

**Common violations**: Three consecutive paragraphs starting with "Ponadto" / "Co więcej" / "Dodatkowo"; using "zgłębić" instead of "zbadać" or "przeanalizować"; "wieloaspektowy" as a vague descriptor; opening a paragraph with a gerund phrase that restates the previous paragraph.

---

## E. Citations & Bibliography

*Used by: bibliography-checker-wne, wne-supervisor-agent, thesis-reviewer (all review agents)*

### E1. Cite All Named Sources at First Use Per Section
At first use in each section, even if cited earlier in the thesis. Readers may jump to individual sections; a bare mention without citation forces them to search.

**Common violations**: Named methods, reports, or theories mentioned without citation in later chapters; dataset or index names (e.g., "World Bank Development Indicators", "Eurostat") used without citation in sections that readers may access directly.

### E2. Citation Format Consistency
In a WNE UW licencjat: in-text citations use (Autor, rok) format. No full references in footnotes — footnotes are for supplementary content only, not bibliographic references.

**Common violations**: Mixing (Autor rok) without comma and (Autor, rok) with comma; using footnote-based bibliographic references instead of in-text citations; bibliography not in APA format; bibliography not alphabetical; numbered bibliography entries (WNE requires alphabetical, not numbered).

### E3. Bibliography Completeness and Hygiene
Every bibliography entry must include all required fields: author(s), title, year, journal/publisher, and volume/pages or DOI where applicable. Entries should be consistent in style and up-to-date.

**Common violations**: Missing page numbers or DOIs for journal articles; "w druku" or "forthcoming" placeholders never updated; inconsistent author name format across entries (e.g., "Kowalski, J." in one entry and "Jan Kowalski" in another); inconsistent journal name formatting; duplicate entries under different keys; citing working paper versions when published versions are available.

---

## F. Process & Meta

*Used by: prose-polisher-wne (final pass), thesis-editor, thesis-reviewer (viva-examiner-agent for limitation questions)*

### F1. Strategic Limitation Placement
For a WNE UW licencjat: discuss limitations at the point where design decisions are made, so the reader (supervisor) sees the author's awareness of assumptions and tradeoffs. A dedicated limitations section in the conclusion is standard — but limitations relevant to methodology should also appear when the methodology is introduced.

**Common violations**: Limitations omitted entirely from the thesis; limitations listed only in the conclusion with no connection to the methodological choices that created them; defensive or apologetic tone when discussing known constraints; limitations framed as failures rather than as scope boundaries.

### F2. Negation-Contrast Audit
Before finalising any chapter, search for "nie...lecz" / "nie...ale" patterns and rephrase positively. Negation-contrast structures are a strong AI-writing marker (see B2 and B7). A final-pass search for patterns like "jest nie.*lecz", "nie aby.*ale aby", "nie jak.*ale jak" catches residual instances.

**Common violations**: "Celem pracy jest nie X, lecz Y" → rephrase: "Celem pracy jest Y: ..."; "nie tylko... ale także" used to combine two claims that should be separate sentences; negation-contrast appearing in the abstract or conclusion where writing quality is most scrutinised.