---
name: thesis-structure-decision
description: "STAN 1 — decides thesis structure. Call thesis-pipeline first to orient yourself; it will route here. Call this skill directly only when the user explicitly needs a structural decision: 'czy powinienem oddac tylko czesc modelowa', 'co zrobic z case study', 'jak to wszystko polaczyc', 'czy case study nadaje sie jako appendiks', 'co powinienem wyciąc', 'jaka powinna byc struktura mojej pracy'. Also triggers when the user uploads a thesis draft and asks for structural assessment. Requires rubric from thesis-reference-calibrator. Produces one clear structural recommendation. Always respond in the same language the user writes in."
metadata:
  version: "1.0"
  pipeline_position: "STAN 1 — after thesis-reference-calibrator, before thesis-macro-auditor"
---

# Thesis Structure Decision

## Purpose

Run **once**, after `thesis-reference-calibrator` and before any editing or chapter-level analysis. Answers one question:

> **Given this material and these formal requirements, what is the optimal structure for the final submitted thesis?**

Produces a concrete recommendation — not a menu of options — with justification grounded in formal requirements, the internal logic of the material, and disciplinary standards.

Always respond in the same language the user writes in.

---

## What this skill is NOT

- Does not edit or rewrite any text.
- Does not perform paragraph-level analysis.
- Does not trigger on requests to improve specific sections.
- Does not replace `thesis-macro-auditor`, which comes after this decision.

---

## Inputs required

Before beginning, verify you have:

1. **Rubric from `thesis-reference-calibrator`** — do not proceed without it.
2. **Structural overview of the thesis:**
   - Table of contents (with section titles and approximate page counts)
   - Abstract or introduction of each major part
   - First and last sentence of each section or subsection
   - Stated research questions or hypotheses
3. **Formal requirements** — already encoded below. Confirm with user if there are additional constraints (supervisor preferences, departmental norms, deadline).

Do NOT ask for full chapter text. Working on structural markers is sufficient.

---

## Formal requirements (WNE UW, licencjat)

- Must have a clearly defined research goal; may contain a research hypothesis
- Must use current national or international academic literature
- Must demonstrate the student's preparation for conducting research
- Must use research instruments covered during studies or beyond
- May be literature-based (descriptive/analytical essay) OR original research — if based on literature, the student must demonstrate **independent construction and interpretation**, not just summary
- Target length: approximately 50 standard pages (100,000 characters with spaces)

**Critical implication:** A thesis consisting of a theoretical/model part alone can be complete and valid — IF it demonstrates independent analytical construction, not just literature review. A case study adds empirical weight but is not mandatory.

---

## Analysis framework

### 1. Does the material satisfy formal requirements on its own?

Assess the theoretical/model part independently:
- Clearly defined research goal?
- Academic literature used?
- Independent construction demonstrated?
- Can reach ~50 pages without padding?

If YES to all: the model part alone is a viable submission.

### 2. Relationship between the model part and the case study

Three possible relationships:

**A) The case study answers the hypotheses of the model part.**
Direct empirical answer to the theoretical hypotheses. Strongest case for integrated thesis.

**B) The case study illustrates the model but does not test it.**
Provides context but is not load-bearing. Profile for an **appendix**.

**C) The case study runs parallel or independently.**
Two parts address related but distinct questions. Profile for: (a) abandoning the case study, or (b) treating it as a separate future paper.

Diagnostic questions:
- Does the case study reference the hypotheses or research questions of the model part?
- Could the model part's conclusion be written without the case study?
- Does the case study use the analytical framework constructed in the model part?

### 3. Does the case study meet the quality bar for inclusion?

Even if relationship is strong (type A):
- Is the content substantively there?
- Is the analytical approach consistent with the rubric standard?
- Would integrating it require more work than the time available justifies?

### 4. Disciplinary expectations

Based on Fałkowski & Lewkowicz reference papers (WNE UW, economics/political science):
- Clearly stated research gap and contribution
- Analytical approach that is reproducible and transparent
- Results section distinguishes findings from interpretation
- Conclusion directly answers the stated research questions
- Explicit acknowledgment of limitations

### 5. Missing sections

Regardless of chosen structure, identify missing elements: proper introduction (gap, goal, structure), methodology section, results/findings section distinct from discussion, conclusion answering the hypotheses, limitations acknowledged, complete bibliography.

---

## Output format

```
═══════════════════════════════════════════════════════
REKOMENDACJA STRUKTURY PRACY
═══════════════════════════════════════════════════════

REKOMENDACJA:
  (A) Złożyć samą część modelową jako kompletną pracę
  (B) Zintegrować część modelową i case study w jedną pracę
  (C) Złożyć część modelową jako pracę, case study jako appendiks
  (D) Inna struktura — [opis]

UZASADNIENIE:
[3–5 zdań wyjaśniających dlaczego ta opcja — odnoszących się do relacji
między hipotezami a case study, wymogów formalnych WNE UW, stanu materiału
i dostępnego czasu]

RELACJA MIĘDZY CZĘŚCIĄ MODELOWĄ A CASE STUDY:
[Typ A / B / C — z krótkim uzasadnieniem]

BRAKUJĄCE SEKCJE (wymagane niezależnie od wybranej opcji):
[Lista]

SEKCJE DO ROZWAŻENIA USUNIĘCIA LUB SKRÓCENIA:
[Jeśli dotyczy — z uzasadnieniem]

WARUNEK KONIECZNY DO REALIZACJI REKOMENDACJI:
[Co musi być zrobione zanim praca będzie gotowa do oddania]

NASTĘPNY KROK:
Po zaakceptowaniu tej rekomendacji → uruchom thesis-macro-auditor
═══════════════════════════════════════════════════════
```

After delivering the recommendation, ask:
> "Czy ta rekomendacja jest zgodna z Twoimi oczekiwaniami i ograniczeniami czasowymi? Jeśli masz inne zdanie lub dodatkowe ograniczenia, powiedz — zrewidujemy rekomendację."

Wait for confirmation before the user moves to `thesis-macro-auditor`.

---

## Edge cases

- **No table of contents yet**: Ask for chapter titles and approximate content description.
- **User unsure about hypotheses**: Ask them to state in one sentence what each part is trying to answer.
- **Supervisor has already expressed a preference**: Treat as hard constraint; flag if the preferred structure creates logical problems.
- **Severe time constraint**: Weight recommendation toward option requiring least additional writing — typically Option A or C.
- **Rubric missing**: Do not produce a recommendation. Ask user to run `thesis-reference-calibrator` first.