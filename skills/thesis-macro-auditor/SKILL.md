---
name: thesis-macro-auditor
description: "Use this skill after thesis-structure-decision has been completed and before any sentence-level analysis begins. Triggers on: 'czy moja praca jest dobrze zbudowana?', 'czy struktura ma sens?', 'co brakuje calosci?', 'zrob przeglad struktury', 'oceń układ pracy'. Works on table of contents plus first and last sentence of each section only — never on full text. Dispatches 6 parallel structural agents, synthesises their output into a prioritised macro issue list. Always respond in the same language the user writes in."
metadata:
  version: "1.0"
  pipeline_position: "3 — after thesis-structure-decision, before thesis-analyzer"
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

3. **The calibration rubric** from `thesis-reference-calibrator` (must be provided at session start).

Calibration note: The reference papers are published journal articles — they represent a ceiling above licencjat requirements. Audit assesses whether the thesis meets the licencjat standard, not whether it matches a journal article.

---

## Inputs required

Before beginning, confirm the user has provided:

1. **Quality rubric** from `thesis-reference-calibrator`
2. **Full table of contents** with all section and subsection headings
3. **First and last sentence** of each section and subsection
4. **Brief statement** of the thesis's central research question/objective and hypothesis
5. **Structural decision** from `thesis-structure-decision` (Option A, B, C, or D)

If items 2–4 are missing, ask for them. Do not proceed without them.

---

## Orchestration procedure

### Krok 0 — Załaduj pryncypia akademickie

**Przed wysłaniem agentów:** Użyj narzędzia Read, aby wczytać plik `principles/academic-writing.md` z katalogu pluginu. Wyciągnij i zachowaj treść następującej kategorii:
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
- The table of contents + all first/last sentences
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

If dispatching: compile chapter summaries from the first/last sentences already provided by the user (2–4 sentences per chapter). If the summaries are too sparse, ask the user for 2 sentences per chapter before dispatching.

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

NASTĘPNY KROK: thesis-analyzer
(analiza akapit po akapicie, sekcja po sekcji — zacznij od sekcji z BLOKUJĄCYMI problemami)
═══════════════════════════════════════════════
```

---

## Failure modes to avoid

**Do not comment on sentence quality.** If a first or last sentence is poorly written, note "prose issues — flag for thesis-analyzer" and move on.

**Do not produce a generic checklist.** Every finding must be specific to this thesis. "The introduction should contain a research question" is useless. "The introduction ends with a description of Poland's economic transformation but does not state a research question or preview the structure" is actionable.

**Do not audit what you haven't been given.** If a section has no first/last sentence, note the gap and ask.

**Do not recommend specific wording.** That is `thesis-editor`'s job.