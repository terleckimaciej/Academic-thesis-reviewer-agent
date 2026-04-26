---
name: thesis-macro-auditor
description: "Run this skill for a bird's-eye assessment of the thesis architecture — whether sections fulfill their roles, whether the logical order holds, what is missing or redundant, where transitions fail. Call at any point when you want to zoom out from sentence-level work and look at the whole structure. Triggers on: 'czy moja praca jest dobrze zbudowana?', 'czy struktura ma sens?', 'co brakuje calosci?', 'zrob przeglad struktury', 'oceń układ pracy', 'chcę zobaczyć całość z lotu ptaka', 'sprawdź ogólną strukturę'. Works on table of contents plus first and last sentence of each section only — never on full text. Dispatches 6 parallel structural agents. Can be called after deep micro-analysis to recheck the macro view. Always respond in the same language the user writes in."
metadata:
  version: "1.0"
  pipeline_position: "STAN 2 — after thesis-structure-decision, before thesis-analyzer; re-callable at any stage to recheck macro structure"
---

# Thesis Macro Auditor

## Purpose

Evaluates the **architecture** of the thesis — the relationship between sections, the logical flow of the argument across the whole work, and the presence or absence of structural elements required by WNE UW norms. Does not evaluate individual sentences, paragraphs, or stylistic choices — those belong to `thesis-analyzer`.

Output: a ranked list of macro-level issues to be addressed before sentence-level work begins. Fixing structure before fixing prose is always more efficient.

**Architecture:** Dispatches 6 specialised section audit agents in parallel, collects their structured findings, synthesises into a single prioritised report. Optionally dispatches `cross-chapter-auditor` as a 7th agent when the thesis has two or more distinct parts whose internal consistency needs verifying (see Step 1b).

---

## Strict scope boundary

**This skill analyses structure only.** If during synthesis Claude notices a stylistic problem, note "prose issues — flag for thesis-analyzer" at most once per section, without elaborating.

**This skill does not rewrite anything.** It produces diagnoses, not fixes.

**Works on minimal inputs only:** table of contents + first and last sentence of each section/subsection.

---

## Institutional context (WNE UW — Licencjat)

Audited against:

1. **WNE UW formal requirements:** clear research objective, use of academic literature, demonstration of research readiness, independent construction and interpretation, ~50 pages.

2. **Reference paper conventions (Fałkowski & Lewkowicz, WNE UW):**
   - Introduction: gap statement → contribution → literature positioning → structure preview
   - Method section justifies all analytical choices
   - Results section presents findings without interpretation
   - Discussion/Conclusion: synthesises findings, acknowledges limitations, opens future research
   - Every section transition explicitly signalled

3. **The calibration rubric** from `thesis-reference-calibrator` — auto-loaded from `rubric.md` in the project folder (see Krok 0).

Calibration note: The reference papers are published journal articles — they represent a ceiling above licencjat requirements. Audit assesses whether the thesis meets the licencjat standard, not whether it matches a journal article.

---

## Inputs required

Before beginning, confirm the user has provided:

1. **Quality rubric** — auto-loaded from `rubric.md` in the project folder (see Krok 0); if not found, ask the user to paste it
2. **Full table of contents** with all section and subsection headings (if the thesis is a `.tex` file in the mounted folder, Claude can extract this by reading the file with the Read tool and scanning for `\section{}` / `\subsection{}` commands)
3. **First and last sentence** of each section and subsection (Claude can read these directly from the `.tex` file if mounted)
4. **Brief statement** of the thesis's central research question/objective and hypothesis
5. **Structural decision** from `thesis-structure-decision` (Option A, B, C, or D)

If items 2–4 are missing, ask for them. Do not proceed without them. Item 1 is resolved automatically — only ask the user for it if the file cannot be found.

---

## Orchestration procedure

### Krok 0 — Załaduj rubryk kalibracyjny, session log, logical spine i pryncypia akademickie

**Przed wysłaniem agentów wykonaj cztery odczyty:**

**0. Session log (historia pracy):**
Sprawdź czy plik `session-log.md` istnieje w folderze projektu użytkownika. Jeśli tak — wczytaj go narzędziem Read. Skorzystaj z niego by zrozumieć aktualny stan pracy (opcja strukturalna z STAN 1, wcześniejsze wyniki audytów). Jeśli nie ma pliku — kontynuuj bez niego (pierwsza sesja).

**1. Logical Spine (głęboki kontekst makro po analizie mikro):**
Sprawdź czy plik `logical-spine.md` istnieje w folderze projektu użytkownika, albo w `outputs/logical-spine.md`. Plik ten zawiera szczegółowe streszczenia każdego z akapitów z poprzednich sesji analizy mikro.
- Jeśli tak: Wczytaj go narzędziem Read i zachowaj. Uruchamia to **Deep Macro Mode**, a tym samym używasz pełnych streszczeń z `logical-spine.md` *zamiast* tylko wczytanego spisu treści i pierwszych/ostatnich zdań sekcji przy zleceniach dla agentów makro-audytu.
- Jeśli nie: Kontynuuj na bazie standardowych wejść (spis treści + pierwsze i ostatnie zdania), co oznacza początkowy makro audyt przed rozpoczęciem analizy mikro.

**2. Rubryk kalibracyjny (opcjonalny plik projektu):**
Sprawdź czy plik `rubric.md` istnieje w folderze projektu użytkownika (zamontowanym folderze). Jeśli tak, wczytaj go narzędziem Read. Jeśli nie — sprawdź `outputs/rubric.md`. Jeśli nadal nie ma:
- Zapytaj użytkownika: "Nie znalazłem pliku `rubric.md`. Czy możesz wkleić rubrykę kalibracyjną z `thesis-reference-calibrator`? Jeśli jej nie masz, mogę przeprowadzić audyt względem ogólnego standardu WNE."
- Jeśli użytkownik wklei rubrykę — kontynuuj z nią.
- Jeśli użytkownik potwierdzi brak rubryki — kontynuuj bez niej, ale zaznacz w raporcie: "UWAGA: Audyt bez rubryki wydziałowej — ocena względem ogólnego standardu WNE."

**3. Pryncypia akademickie:**
Użyj narzędzia Read, aby wczytać plik `principles/academic-writing.md` z katalogu pluginu. Wyciągnij i zachowaj treść następującej kategorii:
- **Kategoria A** (Structure & Narrative) — pryncypia A1–A7

Dołącz pełną treść tej kategorii do promptu każdego z sześciu agentów strukturalnych jako sekcję:
```
## Pryncypia akademickie (kategoria A — Structure & Narrative)
[treść kategorii A z pliku principles/academic-writing.md]
```

Te pryncypia definiują wymagania dotyczące spójności logicznej, chaining sekcji, rytmu GPS i struktury narracyjnej — dokładnie te kryteria, według których audytorzy makro oceniają strukturę pracy.

### Step 1 — Deploy 6 parallel audit agents

Dispatch all six agents simultaneously using the Agent tool. Provide each agent with:
- The quality rubric
- The research question / hypothesis
- The structural decision (option chosen)
- The thesis content outline. If in **Deep Macro Mode** (i.e. `logical-spine.md` was loaded), send the full content of `logical-spine.md` rather than just the first/last sentences and TOC. If not in deep mode, send the standard TOC + all first/last sentences.
- The agent's specific scope
- The academic writing principles block (category A) loaded in Krok 0

```
Agents to deploy in parallel:
- intro-auditor        → audits Introduction section
- lit-review-auditor   → audits Literature Review / Theoretical Framework
- method-auditor       → audits Methodology / Data section
- results-auditor      → audits Results / Analysis section
- conclusion-auditor   → audits Discussion / Conclusion section
- flow-auditor         → audits inter-section transitions and logical spine
```

### Step 1b — Cross-chapter audit (conditional)

After the 6 section agents return, assess: does this thesis have two or more distinct parts whose internal consistency is at risk? Dispatch `cross-chapter-auditor` if ANY of these is true:
- The structural decision is Option B or C (theoretical part + empirical part)
- The thesis has 3 or more major chapters with independent claims
- `flow-auditor` returned findings related to two-part bridge or introduction-conclusion mismatch
- The user explicitly asks for cross-chapter consistency check
- **Deep Macro Mode** is active (which warrants a full semantic cross-check)

If dispatching: send either the `logical-spine.md` context (if in Deep mode) or compile chapter summaries from front/back sentences (if standard mode). If the non-deep summaries are too sparse, ask the user for 2 sentences per chapter before dispatching.

If NOT dispatching: note "Cross-chapter audit skipped — single-structure thesis or insufficient cross-chapter risk." Do not dispatch unnecessarily — it adds model cost.

### Step 2 — Collect and deduplicate (from all agents dispatched)

Wait for all six agents to return. Collect all findings. Deduplicate: if two agents flag the same issue from different angles, merge into one finding that notes both dimensions.

### Step 3 — Classify and prioritise

Classify each finding using the unified severity scale:
- **BLOKUJĄCY** — structural error that breaks the logical spine or violates a non-negotiable WNE requirement; must be resolved before micro-level work begins. Includes: broken argument chain, missing cel badawczy, missing methodology section, hypothesis-conclusion mismatch.
- **ISTOTNY** — weakens the thesis's coherence; address before micro-level work ideally, but does not block it. Includes: missing section transitions, weak literature-to-hypothesis bridge, missing limitations section, BRAKUJĄCY ELEMENT (required by convention but not present).
- **DRUGORZĘDNY** — structural choice that could be improved; address when convenient. Includes: ZBĘDNY elements (redundant or unjustified sections), minor transition gaps.

Prioritise BLOKUJĄCY findings by impact: broken logical spine first, missing required sections second, hypothesis mismatch third.

### Step 4 — Assess readiness

Based on the finding count and severity, assess whether the thesis is ready to move to sentence-level analysis.

---

## Output format

Write in the same language the user writes in.

Use the prefix **M** for all synthesised findings: M1, M2, M3... These labels persist if the user passes macro findings to thesis-editor for structural fixes. Separate numbering series per category (M1–M8 for główne, M9–M14 for drugorzędne, etc.) is acceptable but a single continuous series is simpler and preferred.

```
═══════════════════════════════════════════════
RAPORT MAKRO-AUDYTU STRUKTURY
═══════════════════════════════════════════════

BLOKUJĄCE (wymagają działania przed analizą mikro)
──────────────────────────────────────────────────
M[N]. [1 zdanie diagnozy] + [1 zdanie dlaczego to problem] + [1 zdanie co zrobić]
Max 8 punktów. Każdy musi być specyficzny dla TEJ pracy.

Przykład:
M1. Wstęp kończy się opisem transformacji polskiej gospodarki bez sformułowania pytania badawczego ani zapowiedzi struktury pracy — narusza wymóg WNE cel badawczy i konwencję artykułów referencyjnych. Dodać akapit zamykający wstęp: pytanie badawcze + zdanie "Pozostała część pracy jest zorganizowana w następujący sposób..."

ISTOTNE (warto zaadresować — nie blokują analizy mikro, ale obniżają spójność)
───────────────────────────────────────────────────────────────────────────────
M[N]. [Krótszy opis] Max 6 punktów. Obejmuje brakujące elementy wymagane konwencją.

DRUGORZĘDNE (elementy zbędne lub do rozważenia)
───────────────────────────────────────────────
M[N]. [Sekcje bez funkcji lub redundantne] Max 3 punkty.

OCENA GOTOWOŚCI DO ANALIZY MIKRO
─────────────────────────────────
[Jedna z trzech opcji:]
- GOTOWA: brak BLOKUJĄCYCH problemów strukturalnych.
- WARUNKOWO GOTOWA: M[N], M[N] (BLOKUJĄCE) muszą być rozwiązane przed analizą mikro.
- NIE GOTOWA: praca wymaga przebudowy strukturalnej przed analizą mikro.

SPÓJNOŚĆ MIĘDZY ROZDZIAŁAMI
────────────────────────────
[Jeśli cross-chapter-auditor był uruchomiony: przedstaw jego findings z prefiksem X tu, po raporcie sekcji.]
[Jeśli nie był uruchomiony: "Cross-chapter audit: pominięty — praca jednostrukturalna."]

NASTĘPNY KROK:
[Jeśli to pierwsza sesja makro-audytu:]
→ thesis-analyzer (analiza mikro, zacznij od sekcji z BLOKUJĄCYMI problemami)
[Jeśli wracasz do makro-audytu po wcześniejszych sesjach analizy/edycji:]
→ thesis-editor (zaadresuj BLOKUJĄCE problemy M-prefix z tej sesji), lub
→ thesis-analyzer (kontynuuj analizę kolejnych sekcji jeśli nie ma BLOKUJĄCYCH)
[Jeśli praca nie ma BLOKUJĄCYCH i wszystkie sekcje przeanalizowane:]
→ thesis-reviewer (recenzja końcowa)
═══════════════════════════════════════════════
```

### Zapisz wpis STAN 2 do session-log.md

Po dostarczeniu raportu dopisz wpis do `session-log.md` (Edit tool — dopisz na końcu). Jeśli plik nie istnieje — stwórz go (Write tool).

Szablon wpisu:
```
## STAN 2 — thesis-macro-auditor ✅
Data: [aktualna data]
Sekcje z problemami: [lista sekcji z M-findings BLOKUJĄCE]
Kluczowe problemy makro:
- M1: [opis 1 zdanie]
- M2: [opis 1 zdanie]
[...]
Następny krok: thesis-analyzer (fragment: [pierwsze słowa pierwszego fragmentu])

---
```

---

## Failure modes to avoid

**Do not comment on sentence quality.** If a first or last sentence is poorly written, note "prose issues — flag for thesis-analyzer" and move on.

**Do not produce a generic checklist.** Every finding must be specific to this thesis. "The introduction should contain a research question" is useless. "The introduction ends with a description of Poland's economic transformation but does not state a research question or preview the structure" is actionable.

**Do not audit what you haven't been given.** If a section has no first/last sentence, note the gap and ask.

**Do not recommend specific wording.** That is `thesis-editor`'s job.