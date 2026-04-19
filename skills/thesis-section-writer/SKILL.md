---
name: thesis-section-writer
description: "Run this skill when you need to write a new section from scratch — a missing section, case study, or additional chapter not yet present in the thesis. Available at any pipeline stage. Triggers on: 'napisz sekcje o', 'potrzebuje napisac case study', 'brakuje mi rozdzialu o', 'napisz wstep', 'napisz wnioski', 'write the political case study section', 'draft the methodology section'. Requires rubric from thesis-reference-calibrator. Dispatches literature-gap-finder and section-drafter-wne in sequence, optionally adds prose-polisher-wne. Always respond in the same language the user writes in."
metadata:
  version: "1.0"
  pipeline_position: "AD-HOC — available at any pipeline stage"
---

# Thesis Section Writer

## Purpose

Drafts new sections or substantial additions to a WNE UW licencjat thesis in Word document format. Specifically designed for the scenario where the thesis already exists as a rough draft with one or more missing or underdeveloped sections.

**Architecture:** Two-stage sequential pipeline:
1. `literature-gap-finder` — researches the intellectual landscape, identifies key sources and arguments
2. `section-drafter-wne` — drafts the section using the gap brief, calibrated to the thesis's voice and WNE conventions

Optionally followed by: `prose-polisher-wne` for a final polish pass.

---

## When to use

This skill is designed for:
- **Missing sections** — a section required by WNE conventions that does not yet exist (e.g., a methodology section, a limitations section, a proper conclusion)
- **Underdeveloped sections** — a section that exists as bullet points or notes and needs to be developed into academic prose
- **New chapters** — adding an empirical case study, comparative analysis, or additional theoretical discussion to an existing draft
- **Bridging sections** — the connective tissue between a theoretical and empirical part (required for Option B / C from thesis-structure-decision)

This skill is **not** for editing existing text — use `thesis-editor` for that.

---

## Inputs required

Before beginning, confirm the user has provided:

1. **Quality rubric** — auto-loaded from `rubric.md` in the project folder (see Krok 0); if not found, ask the user to paste it
2. **Research question / hypothesis** (one sentence)
3. **Section specification:**
   - Section title (proposed)
   - Section function: what this section must achieve for the thesis
   - Approximate target length (in pages or words)
   - Where it sits in the thesis structure (after what section, before what section)
4. **Adjacent text** (recommended): the last paragraphs of the preceding section and the first paragraphs of the following section — so the draft can bridge correctly
5. **Existing bibliography** (if any): list of sources already cited — to avoid duplicating and to build on existing references
6. **Specific content requirements** (if any): arguments to make, data to incorporate, specific sources the user wants cited, analytical framework to apply

---

## Orchestration procedure

### Krok 0 — Załaduj rubryk kalibracyjny i pryncypia akademickie

**Przed wysłaniem agentów wykonaj dwa odczyty:**

**1. Rubryk kalibracyjny (opcjonalny plik projektu):**
Sprawdź czy plik `rubric.md` istnieje w folderze projektu użytkownika (zamontowanym folderze). Jeśli tak, wczytaj go narzędziem Read i zachowaj jego treść. Jeśli nie — sprawdź `outputs/rubric.md`. Jeśli nadal nie ma:
- Zapytaj użytkownika: "Nie znalazłem pliku `rubric.md`. Czy możesz wkleić rubrykę kalibracyjną? Jest potrzebna do skalibrowania draft sekcji do standardu Twojego wydziału."
- Jeśli użytkownik potwierdzi brak rubryki — kontynuuj bez niej, ale zaznacz w drafcie: "UWAGA: Sekcja napisana bez rubryki wydziałowej."

**2. Pryncypia akademickie:**
Użyj narzędzia Read, aby wczytać plik `principles/academic-writing.md` z katalogu pluginu. Wyciągnij i zachowaj treść następujących kategorii:
- **Kategoria A** (Structure & Narrative) — pryncypia A1–A7
- **Kategoria B** (Prose & Style) — pryncypia B1–B8

Dołącz pełną treść tych kategorii do promptu agenta `section-drafter-wne` jako sekcję:
```
## Pryncypia akademickie (kategorie A, B)
[treść kategorii A i B z pliku principles/academic-writing.md]
```

Przekaż tę samą sekcję agentowi `prose-polisher-wne`, jeśli jest uruchamiany w kroku 4.

Nie przekazuj pryncypiów agentowi `literature-gap-finder` — ten agent pracuje z literaturą, a nie z prozą.

### Step 1 — Intake and scope confirmation

Confirm the section specification with the user before dispatching agents:
```
Sekcja do napisania: [tytuł]
Funkcja w pracy: [opis]
Długość docelowa: [N stron / N słów]
Umiejscowienie: po [X], przed [Y]
Dostępne materiały: [lista]
```

Ask if any clarification is needed.

### Step 2 — Literature research (literature-gap-finder)

Deploy `literature-gap-finder` with:
- The research question / hypothesis
- The section specification (title, function, arguments to make)
- The discipline: economics / political science / political economy, WNE UW level
- The existing bibliography (to avoid duplicates)

Wait for the gap brief to return before proceeding. Present the source brief to the user and ask:
> "Oto raport literatury dla tej sekcji. Czy są konkretne źródła lub argumenty, które koniecznie chcesz uwzględnić, a których tu nie ma? Czy mam kontynuować do draftu?"

**This confirmation step is mandatory.** The user must approve or adjust the gap brief before the section is drafted.

### Step 3 — Section drafting (section-drafter-wne)

Deploy `section-drafter-wne` with:
- The quality rubric
- The research question / hypothesis
- The section specification
- The approved gap brief (including source list and argument scaffolding)
- Adjacent text (if provided)
- Any specific content requirements from the user
- Target length
- The academic writing principles block (categories A, B) loaded in Krok 0

The drafter produces plain-text prose ready to paste into Word, with [ŹRÓDŁO POTRZEBNE] markers where citations are needed but specific sources are uncertain.

### Step 4 — Optional polish pass

If the user requests it (or if the draft shows AI writing tells), deploy `prose-polisher-wne` on the draft with:
- The draft text
- A note that this is a new draft (not a diagnosed problem list — the polisher should apply its standard checks)
- The rubric (loaded in Krok 0 from `rubric.md`, or omit if not available)

### Step 5 — Deliver and annotate

Present the final draft with:
- The complete section text ready to paste into Word
- A list of all [ŹRÓDŁO POTRZEBNE] markers with descriptions of what source is needed
- A list of assumptions made about content
- Suggested placement instructions (before/after what existing section)

---

## WNE conventions for new sections (enforced via section-drafter-wne)

**Language:** Polish unless instructed otherwise (matches the thesis's language).

**Citations:** (Autor, rok) in-text format; no footnote-based bibliographic references; [ŹRÓDŁO POTRZEBNE: opis] for unverified sources.

**Case study sections (political / comparative):**
- Must explicitly anchor to the theoretical framework established in the preceding sections
- Must reference the research hypotheses and state how the case study addresses them
- Must include case selection rationale (why this case, why now, what it illustrates)
- Must acknowledge case study limitations (generalisability, data access, selection bias)

**Methodology sections:**
- Must justify not only what method was used but why (including why alternatives were rejected)
- Must describe data sources with provenance, period, and limitations
- Must precede the results section

**Introduction:**
- Must follow the GPS rhythm: gap → research question → literature positioning → structure preview
- Must end with a sentence previewing the structure of the rest of the thesis

**Conclusion / Wnioski:**
- Must open with a direct callback to the research question
- Must explicitly acknowledge limitations (non-negotiable)
- Must mention future research directions
- Must not introduce new empirical claims

---

## Failure modes to avoid

**Do not draft without the literature gap brief.** A section drafted without research context will miss key sources and produce generic arguments.

**Do not skip the confirmation step after the gap brief.** The user may have specific sources or arguments they want; discovering this after the full draft is wasteful.

**Do not over-promise on citations.** Every uncertain source gets a [ŹRÓDŁO POTRZEBNE] marker. The student verifies citations — the skill does not invent them.

**Do not write more than the target length.** Match the section length to the thesis's existing section lengths.