---
name: thesis-reference-calibrator
description: "Use this skill at the very start of any thesis editing workflow, before any analysis, structural review, or editing takes place. Triggers when the user uploads reference papers (supervisor's articles, department exemplars) or faculty guidelines. Also triggers on: 'here are example papers', 'my supervisor wrote this', 'calibrate to my field', 'what's the standard in my department', 'use this as a reference'. Output is a structured rubric to pass to all other skills. Always respond in the same language the user writes in."
metadata:
  version: "1.0"
  pipeline_position: "1 — run first, before all other skills"
---

# Thesis Reference Calibrator

## Purpose

Run **once**, at the start of the entire thesis editing pipeline. Extracts a concrete, field-specific quality standard from reference materials provided by the user — supervisor's papers, highly-rated department work, or official faculty writing guidelines.

Output is a **structured rubric** (a named, persistent document) that all other skills use to calibrate their assessments. Without it, every diagnostic skill defaults to generic "good academic writing" — which may not match the user's specific discipline, institution, and examiner.

---

## What this skill is NOT

- Does not edit or analyse the user's own thesis text.
- Does not provide general writing advice.
- Does not trigger on the user's own draft — only on **reference materials**.

---

## Inputs

Ask the user to provide **at least one** of the following:

1. **Reference papers** — articles or papers by their supervisor, prominent faculty members, or top-graded students in the same program.
2. **Faculty or departmental guidelines** — official rubrics, style guides, or assessment criteria.
3. **Explicit constraints** — verbal descriptions of what their supervisor emphasises.

If no materials are provided:
> "To calibrate accurately, I need at least one reference paper or set of faculty guidelines. Without these, I can only apply a generic standard, which may not match your examiner's expectations."

---

## Extraction process

Work through the reference materials **systematically**, section by section. For each dimension below, extract **specific, concrete observations** — not general impressions. Where possible, quote short illustrative phrases (under 10 words) as evidence.

### Dimensions to extract

**1. Hypothesis and claim formulation**
- How are research questions or hypotheses stated? (declarative, interrogative, formal H1/H2, embedded in prose?)
- How specific and falsifiable are the claims?
- Are hypotheses stated before or after the literature review?
- Is uncertainty acknowledged and how?

**2. Claim-to-evidence ratio**
- How many sentences of argument precede each piece of evidence?
- Is evidence cited immediately after a claim, or after a paragraph of reasoning?
- Are claims ever made without citation? Under what conditions?

**3. Language formality and tone**
- Active vs. passive voice ratio.
- Use of first person vs. impersonal constructions.
- Vocabulary register.
- Presence of evaluative language — accepted or avoided?
- Hedging density.

**4. Paragraph structure**
- Does each paragraph follow topic-sentence → development → conclusion?
- Average paragraph length.
- Transitions: explicit or implicit?
- Single-sentence paragraphs: present or avoided?

**5. Citation and referencing conventions**
- Citation style (APA, Chicago, MLA, custom).
- In-text citation format.
- Citation density.
- Are secondary sources cited or avoided?

**6. Section-level conventions**
- How does the introduction open?
- Does the conclusion introduce new material or strictly summarise?
- Is a "limitations" section present and where?
- Are subheadings used within sections?

**7. Polish-language academic register** *(critical for WNE UW licencjat)*
- Does the reference paper use Polish or English? If Polish: what is the dominant sentence construction (impersonal "analiza wskazuje" vs first-person plural "prezentujemy / analizujemy")?
- How are hedged claims phrased in Polish? Look for the specific constructions used: "wyniki sugerują", "dowody są zgodne z", "analiza wskazuje na", or stronger formulations.
- Are there Polish-specific filler phrases present or systematically absent? ("należy podkreślić", "warto zaznaczyć", "oczywiście", "bezsprzecznie")
- How are English technical terms handled — transliterated, translated, or used in original form with italics?
- What is the footnote density? WNE convention: footnotes for supplementary content only, not citations or explanations of key terms.

**8. Figure, table, and data presentation** *(if applicable)*
- Results in tables, figures, or prose?
- Are figures and tables numbered, titled, and sourced? (WNE Załącznik B requires this.)
- How are statistical or quantitative results reported — with units, significance levels, confidence intervals?

---

## Output format

```
═══════════════════════════════════════════════
QUALITY RUBRIC — [Field/Discipline]
Calibrated from: [list of reference materials used]
Date: [today's date]
═══════════════════════════════════════════════

1. HYPOTHESIS & CLAIM FORMULATION
   Standard: [1–3 sentences describing the pattern observed]
   Red flags: [what would deviate from this standard]

2. CLAIM-TO-EVIDENCE RATIO
   Standard: [...]
   Red flags: [...]

3. LANGUAGE FORMALITY & TONE
   Standard: [...]
   Red flags: [...]

4. PARAGRAPH STRUCTURE
   Standard: [...]
   Red flags: [...]

5. CITATION CONVENTIONS
   Standard: [...]
   Red flags: [...]

6. SECTION-LEVEL CONVENTIONS
   Standard: [...]
   Red flags: [...]

7. POLISH ACADEMIC REGISTER
   Standard: [dominant construction — impersonal / first-person plural / mixed]
   Hedging vocabulary observed: [list specific Polish phrases from the reference paper]
   Filler phrases: [present or systematically absent — list examples]
   English terms: [transliterated / translated / italicised original]
   Red flags: [what would sound out of register for this author/field]

8. DATA & TABLE CONVENTIONS
   Standard: [how results are presented — tables, prose, figures]
   Statistical reporting: [level of detail expected — units, significance, etc.]
   Red flags: [what would look unprofessional]

9. FIELD-SPECIFIC NOTES
   [Any discipline-specific conventions — e.g., use of JEL codes, specific citation norms in Polish political science, required appendix conventions]

10. SUPERVISOR / EXAMINER PROFILE
   [If known: specific preferences, known pet peeves, recurring feedback patterns]

═══════════════════════════════════════════════
PASS THIS RUBRIC TO: thesis-structure-decision,
thesis-macro-auditor, thesis-analyzer,
thesis-reviewer, thesis-editor, thesis-section-writer
Note: Dimension 7 (Polish Academic Register) is especially critical for
prose-polisher-wne and section-drafter-wne — paste it explicitly when
dispatching those agents.
═══════════════════════════════════════════════
```

---

## After generating the rubric

Tell the user explicitly:
> "Please save this rubric — copy it somewhere you can retrieve it. At the start of every subsequent session with any other thesis skill, paste this rubric as the first thing in the conversation, before any text from your thesis."

Then ask:
> "Do any of these standards surprise you, or contradict what your supervisor has told you? If so, let's resolve the conflict before we proceed."

---

## Edge cases

- **Only one short reference paper**: Generate the rubric but flag which dimensions are inferred vs. directly evidenced.
- **Faculty guidelines only, no example papers**: Generate from guidelines but note that guidelines describe the minimum bar, not the ceiling.
- **Contradictions between reference materials**: Flag them explicitly and ask which source to treat as authoritative.
- **User's thesis in a different language than reference materials**: Extract structural and logical conventions (which transfer across languages) but flag that language-specific conventions will need manual adjustment.