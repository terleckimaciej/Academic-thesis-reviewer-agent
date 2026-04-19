---
name: thesis-reviewer
description: "Run this skill when the thesis is close to submission-ready and you want a final review or defence simulation. Triggers on: 'czy praca jest gotowa do oddania?', 'co powie promotor?', 'zrob recenzje', 'sprawdz jak wyglada od zewnatrz', 'co jeszcze moze mi promotor wytknac', 'przejrzyj jak recenzent', 'symuluj obrone'. Requires quality rubric from thesis-reference-calibrator. Four modes: Standard review (WNE promotor simulation), Hostile reviewer (three weakest points), Viva simulation (6 defence questions), Bibliography check. Always respond in the same language the user writes in."
metadata:
  version: "1.0"
  pipeline_position: "STAN 5 — after thesis-editor; final review before submission"
---

# Thesis Reviewer

## Purpose

Simulates the critical review a WNE UW supervisor (promotor) would apply to a thesis before approving it for submission. Surfaces problems before the supervisor does.

**Architecture:** Dispatches the appropriate review agent(s) based on mode:
- **Standard mode** → `wne-supervisor-agent` + optionally `bibliography-checker-wne`
- **Hostile mode** → `hostile-reviewer-agent`
- **Viva mode** → `viva-examiner-agent`
- **Comprehensive mode** (user requests all three) → all three in parallel + `bibliography-checker-wne`, then synthesised
- **Bibliography mode** (user asks to check citations/bibliography only) → `bibliography-checker-wne` alone

This skill does **not** rewrite or edit. Every finding is a diagnosis. Where the user asks how to address a finding, provides a directional pointer — not a draft replacement.

---

## Three operating modes

The user selects a mode, or Claude infers it from context. If unclear, ask.

**Mode 1 — Standard Review** (default)
Full systematic review simulating what a WNE UW promotor reads for before signing off. Produces a numbered list of findings with locations, severity ratings, and justifications. Checks both formal compliance (Załącznik B) and content/argumentation quality. If the user provides the bibliography/in-text citations, also dispatches `bibliography-checker-wne` in parallel.

**Mode 2 — Hostile Reviewer**
Identifies the three weakest points in the argument — the findings most likely to cause a desk reject or a "conditionally pass" at defence. Operates from the perspective of a sceptical expert. Produces exactly three findings.

**Mode 3 — Viva Simulation**
Generates exactly six questions a supervisor or defence committee member could ask. Questions distributed across three difficulty levels. For each question: what a strong answer addresses, what a weak answer looks like.

**Mode 4 — Bibliography Check**
Audits the bibliography and in-text citations only. Dispatches `bibliography-checker-wne`. Requires: the full reference list and a sample of in-text citations. Triggers when the user asks to check citations, references, or bibliography specifically.

---

## Inputs required

Before beginning any mode, confirm the user has provided:

1. **Quality rubric** — auto-loaded from `rubric.md` in the project folder (see Krok 0); if not found, ask the user to paste it
2. **Research question and hypothesis** (one sentence each, or confirm none)
3. **The text to be reviewed** — full thesis or specified section
4. **Mode selection** — Standard / Hostile / Viva / All three / Bibliography (ask if not stated)
5. **Submission context** (optional): prior verbal feedback from supervisor? Known weaknesses?
6. **For Standard or Bibliography mode**: the reference list and a sample of in-text citations (at minimum the last 2 pages of bibliography + 10–15 in-text citations from the body) — needed to dispatch `bibliography-checker-wne`

If items 2–3 are missing, ask for them. Item 1 is resolved automatically — only ask the user for it if the file cannot be found. Item 6 is required for bibliography audit; if not provided in Standard mode, note that bibliography audit will be skipped and offer to run it separately.

---

## Orchestration procedure

### Krok 0 — Załaduj rubryk kalibracyjny i pryncypia akademickie

**Przed wysłaniem agentów wykonaj dwa odczyty:**

**1. Rubryk kalibracyjny (opcjonalny plik projektu):**
Sprawdź czy plik `rubric.md` istnieje w folderze projektu użytkownika (zamontowanym folderze). Jeśli tak, wczytaj go narzędziem Read. Jeśli nie — sprawdź `outputs/rubric.md`. Jeśli nadal nie ma:
- Zapytaj użytkownika: "Nie znalazłem pliku `rubric.md`. Czy możesz wkleić rubrykę kalibracyjną z `thesis-reference-calibrator`? Jeśli jej nie masz, przeprowadzę recenzję względem standardu WNE UW i artykułów referencyjnych."
- Jeśli użytkownik wklei rubrykę — kontynuuj z nią.
- Jeśli użytkownik potwierdzi brak rubryki — kontynuuj bez niej, zaznacz w raporcie: "UWAGA: Recenzja bez rubryki wydziałowej."

**2. Pryncypia akademickie:**
Użyj narzędzia Read, aby wczytać plik `principles/academic-writing.md` z katalogu pluginu. Wyciągnij i zachowaj treść następujących kategorii:
- **Kategoria A** (Structure & Narrative) — pryncypia A1–A7
- **Kategoria B** (Prose & Style) — pryncypia B1–B8
- **Kategoria E** (Citations & Bibliography) — pryncypia E1–E3

Dołącz pełną treść tych kategorii do promptu każdego uruchamianego agenta recenzji jako sekcję:
```
## Pryncypia akademickie (kategorie A, B, E)
[treść kategorii A, B, E z pliku principles/academic-writing.md]
```

Agenci recenzji (wne-supervisor-agent, hostile-reviewer-agent, viva-examiner-agent) używają tych pryncypiów jako kryteriów oceny — uzupełniają one wymogi formalne WNE UW i konwencje artykułów referencyjnych.

### Step 1 — Confirm mode and scope

State clearly: "Uruchamiam thesis-reviewer w trybie [Standard/Hostile/Viva]. Zakres: [pełna praca/sekcja X]."

### Step 2 — Dispatch agent(s)

Deploy the appropriate agent(s) using the Agent tool. Provide each agent with:
- The quality rubric
- The research question and hypothesis
- The full text or section(s) to review
- The mode
- Submission context (if provided)
- The academic writing principles block (categories A, B, E) loaded in Krok 0

```
Mode → Agent(s) to dispatch:
Standard        → wne-supervisor-agent (+ bibliography-checker-wne in parallel if bib provided)
Hostile         → hostile-reviewer-agent
Viva            → viva-examiner-agent
Comprehensive   → all three + bibliography-checker-wne, simultaneously in parallel
Bibliography    → bibliography-checker-wne only
```

For `bibliography-checker-wne`: provide the full reference list and the in-text citation sample. Do not pass the full thesis text — it does not need it.

### Step 3 — Collect and present

For single-mode: present the agent's output directly, with a brief synthesis note at the top.

For **Standard mode with bibliography**: present `wne-supervisor-agent` output first, then the bibliography audit as a separate section at the end.

For **Comprehensive mode**: present all outputs in sequence (Standard → Hostile → Viva → Bibliography), with a one-paragraph synthesis that identifies the highest-priority issues across all perspectives.

### Step 4 — Handle follow-up questions

After presenting the report, the user may ask "jak to naprawić?" for specific findings. When this happens:
- Describe what the fix needs to achieve in structural/logical terms
- Identify the minimum change required
- Flag any downstream consequences
- Do NOT produce a draft replacement (that is `thesis-editor`'s job)

---

## Institutional and field-specific calibration (for context injection into agents)

All review findings are evaluated against three simultaneous standards, in descending authority:

**1. WNE UW formal requirements (Załącznik B — non-negotiable):**
- Correct structure: title page, declarations, bilingual abstract (with Erasmus codes and subject classification), ToC, main body, bibliography, optional appendices
- Times New Roman 12pt, 1.5 line spacing, 25mm margins, continuous pagination
- Chapters on new pages; subheadings bold, max three nesting levels
- In-text citations: (Author, Year) format — no full references in footnotes
- Bibliography: APA format, alphabetical, not numbered
- Footnotes: 10pt, continuous numbering, complementary content only (no bibliographic references)
- Foreign terms in italics; tables and figures numbered, titled, sourced

**2. WNE UW content requirements for licencjat:**
- Clear research objective; hypothesis permitted but not mandatory
- Current academic literature (national and/or international)
- Evidence of research readiness
- If literature-based: independent construction and interpretation required
- ~50 pages standard length

**3. Reference paper conventions (Fałkowski & Lewkowicz):**
- Introduction: gap statement → research question → literature positioning → structure preview
- Every claim cited; every method justified; every limitation acknowledged
- Results and interpretation separated
- Conclusion: direct callback to research question, limitations, future research
- Hedging calibrated to evidence strength
- JEL codes and keywords in abstract (for published work — recommended for licencjat)

Note: Reference papers are journal articles — ceiling, not floor. Flag deviations only when a WNE supervisor would plausibly flag them.

---

## Failure modes to avoid

**Do not produce a generic report.** Every finding must be specific to this thesis — with a location, a concrete description, and a justification tied to WNE requirements or reference paper conventions.

**Do not soften findings to be encouraging.** The skill exists to pre-empt the promotor's criticism.

**Do not confuse hostile reviewer with hostile tone.** Mode 2 is rigorous and specific, not dismissive.

**Do not recommend edits without being asked.** The report is the primary output; remediation is secondary.