# Academic Thesis Reviewer Agent

A specialized multi-agent workflow for thesis improvement in Claude, designed around one principle: diagnose first, edit second, review before submission.

This repository is not a runnable app. It is an orchestration and prompt system for an AI agent working on your thesis files.

## Why This Exists

Most thesis AI usage is still a copy-paste loop:
- paste a paragraph,
- get a rewrite,
- manually copy changes back,
- lose context between sessions.

This project was created to replace that with a stateful, professional workflow:
- persistent progress tracking across sessions,
- custom quality standards based on your faculty and supervisor expectations,
- direct interactive work on LaTeX files,
- clear stage-by-stage pipeline from structure to final review.

## What Makes It Powerful

### 1. Persistent state between sessions

The workflow uses a session log file to preserve what was already analyzed, fixed, and decided.

Core files:
- session-log.md: running history of decisions, findings, and session handoffs.
- rubric.md: your calibrated quality standard for all downstream checks.

This avoids re-explaining context every time and keeps analysis consistent over long projects.

### 2. Custom standards instead of generic AI advice

With thesis-reference-calibrator, the system derives your own rubric from:
- faculty requirements,
- reference papers,
- supervisor style expectations.

All later skills use this rubric, so feedback is calibrated to your context, not generic internet-level writing tips.

### 3. Interactive LaTeX editing (no copy-paste treadmill)

When Claude can access your thesis project folder, it can read and edit your .tex workflow directly (through Read/Edit tooling in the host environment), so you can move from:
- manual paragraph shuttling,
to:
- iterative, file-based edits with changelog logic and continuity.

### 4. Structured pipeline, not random prompting

The system separates responsibilities:
- macro structure audit,
- micro argument diagnosis,
- targeted editing,
- final reviewer simulation.

That dramatically reduces superficial rewrites and helps you improve argument quality, not just sentence polish.

## Required Claude Setup (Important)

To get full benefits, create a dedicated Claude Project for your thesis.

### Recommended setup

1. Create a new Claude Project for this thesis.
2. Attach or mount your thesis folder (including .tex files) to that project.
3. Keep this repository instructions available in the same working context.
4. Ensure Claude can create and update files in the project workspace.
5. Keep these two files in the thesis workspace:
	 - rubric.md
	 - session-log.md

Without a project-level file workspace, you can still use the system by pasting text, but you lose the main advantage: interactive file operations and persistent state.

## Quick Start in Claude

### Best mode: Claude with project files attached

1. Open Claude Project with your thesis files.
2. Start with:
	 - Use thesis-pipeline and tell me where I am in the workflow.
3. If no rubric exists yet, run:
	 - thesis-reference-calibrator (STATE 0)
	 - thesis-structure-decision (STATE 1)
4. Continue through:
	 - STATE 2 -> STATE 3 -> STATE 4 -> STATE 5
5. Update rubric.md and session-log.md after each session.

### Fallback mode: no file access

1. Paste text chunks manually (about 800-1000 words for micro-analysis).
2. Include research question/hypothesis and current pipeline state.
3. Continue with the same skills, but expect more manual copying.

## User Guide (Step by Step)

### 1. Calibrate your quality standard once

Run thesis-reference-calibrator and provide:
- strong reference papers,
- faculty guidelines,
- supervisor preferences (if known).

Output: rubric.md.

### 2. Decide final thesis structure

Run thesis-structure-decision.

Output: one recommended structure option (A/B/C/D) and missing elements list.

### 3. Audit macro architecture

Run thesis-macro-auditor on:
- table of contents,
- first and last sentences of each section.

Output: prioritized whole-thesis structural issues.

### 4. Run micro analysis in iterations

Run thesis-analyzer on 800-1000 word fragments.

Output: findings with stable labels (R/C/H/E) + handoff block for next session.

### 5. Apply targeted edits

Run thesis-editor with specific diagnosed findings.

Output: revised fragment + change log.

### 6. Run final pre-submission review

Run thesis-reviewer in one or more modes:
- Standard
- Hostile
- Viva
- Bibliography
- Comprehensive

Output: submission-risk report and defense-prep signals.

### 7. Draft missing sections whenever needed

Run thesis-section-writer for missing or underdeveloped sections.

## Pipeline Map

- STATE 0: thesis-reference-calibrator
- STATE 1: thesis-structure-decision
- STATE 2: thesis-macro-auditor
- STATE 3: thesis-analyzer
- STATE 4: thesis-editor
- STATE 5: thesis-reviewer
- AD-HOC: thesis-section-writer
- META: thesis-pipeline (navigation)

## Technical Architecture

### Orchestration skills

- thesis-pipeline: state navigation.
- thesis-reference-calibrator: quality rubric calibration.
- thesis-structure-decision: global structure decision.
- thesis-macro-auditor: bird's-eye architecture checks.
- thesis-analyzer: micro-level diagnostics (4 parallel passes).
- thesis-editor: the only skill that edits text.
- thesis-reviewer: pre-submission and defense simulation.
- thesis-section-writer: section drafting pipeline.

### Specialized agents

Audit and analysis:
- redundancy-checker
- claim-strength-checker
- hypothesis-alignment-checker
- efficiency-auditor
- intro-auditor
- lit-review-auditor
- method-auditor
- results-auditor
- conclusion-auditor
- flow-auditor
- cross-chapter-auditor

Review and defense:
- wne-supervisor-agent
- hostile-reviewer-agent
- viva-examiner-agent
- bibliography-checker-wne

Drafting and polishing:
- literature-gap-finder
- section-drafter-wne
- prose-polisher-wne

### Models and tools

Agent frontmatter declares:
- model: sonnet or opus,
- tools: empty or WebSearch/WebFetch (literature-gap-finder).

The orchestration design assumes access to:
- file read/edit operations,
- agent dispatch,
- optional web retrieval for literature scouting.

### Operating constraints

- Micro-analysis and editing use 800-1000 word chunks.
- Diagnosis before editing (no blind rewrite pass).
- Session continuity should be recorded in session-log.md.
- Quality calibration should be stored in rubric.md.

## Repository Structure

- agents/: task-specific agent definitions.
- skills/: pipeline orchestration skills.
- principles/academic-writing.md: canonical academic writing principles.
- How people actually use AI to edit and improve their theses.md: ecosystem context and rationale.

## Working Artifacts You Should Keep

In your thesis project folder:
- rubric.md
	- calibrated standard,
	- required input for most later skills.
- session-log.md
	- continuity and state memory across sessions,
	- practical handoff surface for long thesis timelines.

## Minimal Input Bundle for Most Sessions

Prepare:
- research question and (if applicable) hypothesis,
- section name and section function,
- target text fragment or direct .tex file access,
- current pipeline state,
- rubric.md when available.

## Example User Prompts

- Start with thesis-pipeline and tell me my current state.
- Run thesis-macro-auditor on my current structure and show missing pieces.
- Run thesis-analyzer on this 900-word fragment and give me a handoff block.
- Run thesis-editor and fix only C2, H1, and E3.
- Run thesis-reviewer in Hostile mode and show the three weakest points.
- Run thesis-section-writer and draft a methodology section after this chapter.

## Best Practices

- Treat AI as a critical reviewer, not a ghostwriter.
- Work iteratively in controlled fragments.
- Keep session-log.md updated to preserve continuity.
- Verify every citation before submission.
- Re-run macro checks after multiple local edits.

## Known Risks

- Citation hallucinations are possible in all models; verify manually.
- Very long conversations can degrade context quality; use session summaries and resets.
- AI can over-smooth voice; keep an explicit voice-preservation pass.

## FAQ

### Can I use this with Word instead of LaTeX?

Yes, but the strongest workflow is file-based .tex editing. With Word, you will rely more on manual text transfer.

### Do I need every state?

Not always, but STATE 0 and STATE 1 significantly improve downstream quality.

### Can thesis-editor run without prior diagnosis?

By design, no. The workflow enforces diagnosis before edits.

## Contributing

When extending the system:
- keep skill and agent names aligned with frontmatter,
- treat principles/academic-writing.md as a single source of truth,
- preserve stable issue labels (R/C/H/E, M, S, P),
- update this README when pipeline behavior changes.

## License

See LICENSE.