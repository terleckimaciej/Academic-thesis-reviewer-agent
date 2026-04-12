---
name: thesis-analyzer
description: "Use this skill after thesis-macro-auditor has produced a macro issue list and the user is ready to begin sentence-level, paragraph-level analysis of the thesis text. Triggers when the user wants deep analysis of a specific section — 'przeanalizuj tę sekcje zdanie po zdaniu', 'sprawdz czy argumentacja jest spojna', 'gdzie są słabe miejsca', 'oceń ten akapit', 'czy wywod odpowiada na hipoteze'. Processes maximum 800-1000 words per session. Dispatches 4 parallel analysis agents, synthesises findings with handoff block. Always respond in the same language the user writes in."
metadata:
  version: "1.0"
  pipeline_position: "4 — after thesis-macro-auditor; before thesis-editor"
---

# Thesis Analyzer

## Purpose and mandate

Performs **granular, exhaustive diagnostic analysis** of thesis text — sentence by sentence, paragraph by paragraph. The primary instrument for identifying specific, local problems that macro-level review cannot detect: logical redundancy, unsupported claims, argument drift, hypothesis misalignment, and prose that creates the appearance of argument without delivering it.

**This skill only diagnoses.** It does not rewrite, rephrase, or suggest specific wording. Every fix belongs to `thesis-editor`. The output of this skill is the input to `thesis-editor`.

**Architecture:** Dispatches 4 specialised agents in parallel — one per analysis pass — then synthesises their findings into a single numbered diagnostic report with handoff block.

---

## Non-negotiable operating constraints

**Rule 1 — Fragment limit: 800–1000 words per session.**
Do not accept more than approximately 1000 words of thesis text for analysis in a single session. If the user pastes more:
1. Acknowledge the full paste
2. State: "I will analyse the first ~1000 words in this session. Please start a new session for the remainder."
3. Proceed with the first 800–1000 words only.

**Rule 2 — Session briefing is mandatory.**
Every session must begin with the user providing, in this order:
1. The quality rubric from `thesis-reference-calibrator`
2. The research question / hypothesis (one sentence)
3. The section being analysed and its function in the thesis
4. A summary of issues already identified in previous sessions (or "no previous sessions")

Ask for items 1–3 if missing. Item 4 is recommended but not blocking.

**Rule 3 — Produce a handoff summary at the end of every session.**
The last output of every session must be a structured handoff block for the user to copy and paste at the start of the next session. Without this, continuity is lost.

**Rule 4 — Never mix analysis and editing.**
If Claude finds itself drafting a better version of a sentence, stop, note the issue as a diagnostic finding, and move on.

---

## Session start protocol

Confirm receipt of all mandatory inputs before beginning:

```
✓ Rubric received
✓ Research question / hypothesis: [restate in one sentence]
✓ Section being analysed: [section name + function]
✓ Previous issues: [brief summary or "none"]
✓ Fragment length: [word count estimate] — within limit / exceeds limit

Beginning analysis of: [first few words of fragment]...
```

---

## Orchestration procedure

### Krok 0 — Załaduj pryncypia akademickie

**Przed wysłaniem agentów:** Użyj narzędzia Read, aby wczytać plik `principles/academic-writing.md` z katalogu pluginu. Wyciągnij i zachowaj treść następujących kategorii:
- **Kategoria A** (Structure & Narrative) — pryncypia A1–A7
- **Kategoria B** (Prose & Style) — pryncypia B1–B8
- **Kategoria F** (Process & Meta) — pryncypia F1–F2

Dołącz pełną treść tych kategorii do promptu każdego z czterech agentów jako sekcję:
```
## Pryncypia akademickie (kategorie A, B, F)
[treść kategorii A, B, F z pliku principles/academic-writing.md]
```

Dzięki temu każdy agent ma aktualne, pełne brzmienie pryncypiów — niezależnie od tego, czy plik był edytowany od czasu ostatniego uruchomienia.

### Step 1 — Prepare fragment

Number the paragraphs in the fragment as P1, P2, P3... Confirm this numbering to the user before dispatching agents.

**Overlap rule (for fragments other than the first in a section):**
If this is not the first fragment being analysed in the section, ask the user to provide the last paragraph of the previous fragment as context. Label it P0. Agents receive P0 but do not analyse it — they use it only to assess whether the transition from P0 to P1 is coherent. Do not produce findings for P0. This allows the analysis to catch argument breaks at section boundaries without double-counting.

If the user does not have the previous fragment: proceed without P0 and note "No overlap context — transitions at fragment start are not assessed in this session."

### Step 2 — Deploy 4 parallel analysis agents

Dispatch all four agents simultaneously using the Agent tool. Provide each agent with:
- The quality rubric
- The research question / hypothesis
- The section name and function
- The numbered fragment (P1, P2, P3...)
- The handoff summary from previous sessions (if any)
- The academic writing principles block (categories A, B, F) loaded in Krok 0

**Labelling scheme:** Each agent uses a fixed letter prefix for its findings. Prefixes are stable across all sessions and all future references (handoff blocks, thesis-editor change-logs):
- `R` — redundancy-checker (R1, R2, R3...)
- `C` — claim-strength-checker (C1, C2, C3...)
- `H` — hypothesis-alignment-checker (H1, H2, H3...)
- `E` — efficiency-auditor (E1, E2, E3...)

**Do not renumber.** Prefixed labels carry semantic meaning: `C3` immediately signals a claim-strength problem, third finding. The thesis-editor and thesis-reviewer will reference these same labels. Stable prefixes eliminate coordination between agents and make every finding traceable to its source pass.

```
Agents to deploy in parallel:
- redundancy-checker          → Pass 1: logical redundancy and repetition         (prefix R)
- claim-strength-checker      → Pass 2: claim strength and evidentiary support     (prefix C)
- hypothesis-alignment-checker → Pass 3: hypothesis alignment                      (prefix H)
- efficiency-auditor          → Pass 4: passage-level efficiency                   (prefix E)
```

### Step 3 — Synthesise findings

Collect all four agent outputs. Then:
1. **Classify** each finding using the unified severity scale:
   - **BLOKUJĄCY** — affects scientific credibility; must be resolved before the thesis can be reviewed or submitted. Typically C and H findings: unsupported causal claims, hypothesis drift, scope overreach.
   - **ISTOTNY** — weakens argumentation quality; address before final review. Typically R findings that break logical flow, or E findings that substantially pad the argument.
   - **DRUGORZĘDNY** — prose and efficiency issues; address in the final polish pass. Typically E findings (AI tells, padding sentences) and minor R findings.
   Override the defaults when warranted: a redundancy that creates confusion about the central claim is BLOKUJĄCY; an unsupported claim that is minor background context may be ISTOTNY.
2. **Sort** within each severity tier: C first (highest credibility risk), then H, then R, then E.
3. **Merge duplicates** if two agents flagged the same sentence from different angles — create one entry that lists both labels (e.g., `C2 / R1`) and both diagnoses.

### Step 4 — Produce handoff block

Always produce the handoff block, even if the session is short or the user does not ask for it.

---

## Output format

Write in the same language the user writes in.

```
═══════════════════════════════════════════════
RAPORT ANALIZY SZCZEGÓŁOWEJ
Fragment: [pierwsze słowa] — [ostatnie słowa]
Liczba akapitów: [N] | Przybliżona liczba słów: [N]
═══════════════════════════════════════════════

BLOKUJĄCE (wymagają naprawy przed oddaniem)
───────────────────────────────────────────
[Prefix][N] | TYP: [Unsupported claim / Hypothesis drift / Causal overreach / Redundancy / inne]
LOKALIZACJA: P[numer akapitu] / "[pierwsze 3–5 słów zdania]..."
DIAGNOZA: [1–2 zdania opisujące konkretnie co jest problemem]
POWÓD: [1 zdanie dlaczego to problem w kontekście pracy naukowej / wymogów WNE]

Przykład: C2 | TYP: Implied causation
LOKALIZACJA: P4 / "Relacja ta wynika z..."
DIAGNOZA: Zdanie implikuje związek przyczynowy między polityką fiskalną a zachowaniem wyborców bez przedstawienia mechanizmu ani cytowania dowodów.
POWÓD: W pracy empirycznej z ekonomii politycznej tego rodzaju twierdzenie wymaga wsparcia econometrycznego lub cytowania literatury potwierdzającej mechanizm.

ISTOTNE (obniżają jakość argumentacji — adresuj przed recenzją końcową)
────────────────────────────────────────────────────────────────────────
[Ten sam format, krótszy opis]

DRUGORZĘDNE (styl i efektywność — adresuj w polish pass)
─────────────────────────────────────────────────────────
[Ten sam format, skrócony]

OBSERWACJE POZYTYWNE (opcjonalnie — max 2)
──────────────────────────────────────────
[Tylko gdy coś jest wyraźnie dobrze zrobione i warto to zachować.
Nie dla zachęty — tylko dla informacji analitycznej.]

LICZBA PROBLEMÓW: Blokujące: [N] | Istotne: [N] | Drugorzędne: [N]
(C: [N] | H: [N] | R: [N] | E: [N])
═══════════════════════════════════════════════

BLOK HANDOFF — skopiuj do następnej sesji
──────────────────────────────────────────
PRZEANALIZOWANY FRAGMENT: [sekcja, akapity P1–PN]
ZIDENTYFIKOWANE PROBLEMY (skrót, z prefiksami):
- C2: [jedno zdanie opisu]
- H1: [jedno zdanie opisu]
- R3: [jedno zdanie opisu]
- E1: [jedno zdanie opisu]
NASTĘPNY FRAGMENT DO ANALIZY: [pierwsze słowa następnego fragmentu]
STATUS HIPOTEZY W TYM FRAGMENCIE: [wyraźnie obecna / implikowana / nieobecna]
═══════════════════════════════════════════════
```

---

## Context management between sessions

A 50-page thesis requires approximately 25–30 analysis sessions at the 800–1000 word limit.

**At the start of each session:**
- User pastes the handoff block from the previous session
- User pastes the rubric (or confirms unchanged)
- User states the research question (or confirms unchanged)
- Claude reads the handoff block and confirms continuity before beginning

**Between sessions:**
- User maintains a running issues log (simple numbered list) accumulating findings from all sessions
- Before starting a new session, user updates the handoff block with any fixes already made

**Session sequencing recommendation:** Analyse sections in the order they appear in the thesis, not by priority.

---

## Failure modes to avoid

**Do not rewrite.** If Claude produces an improved version of a sentence, delete the rewrite, replace with a diagnostic note.

**Do not generalise.** "The argumentation could be stronger in several places" is not a finding. "P4 / 'Relacja ta wynika z...' — causal claim with no cited mechanism" is a finding.

**Do not skip the handoff block.** Even if the session is short. Even if the user does not ask for it.

**Do not analyse beyond the fragment limit** even if the user asks.