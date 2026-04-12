# How people actually use AI to edit and improve their theses

**AI has become the unofficial second advisor for thousands of graduate students**, but the gap between naive use and effective use is enormous. Real-world accounts from Reddit communities (r/PhD, r/ClaudeAI, r/ChatGPT, r/GradSchool), academic blogs, and open-source projects reveal a remarkably consistent set of workflows, frustrations, and hard-won best practices. The dominant finding: **AI works best as a diagnostic critic of human-written text, not as a ghostwriter**, and the most effective users treat context management as a core skill rather than an afterthought. Students who ask AI to write their thesis get mediocre output; students who write first and then use AI to stress-test arguments, tighten prose, and simulate hostile reviewers consistently report transformative results.

The landscape splits into two tiers. Casual users paste text into ChatGPT and ask "make this better," getting back generic, over-smoothed prose. Sophisticated users build multi-stage pipelines with custom GPTs, Claude Projects, and specialized prompts that separate structural review from line editing from argument testing. This report maps the full terrain — from specific prompts that work, to the technical realities of context windows, to open-source systems that simulate entire peer review panels.

---

## The 50-page problem: how people actually handle long documents

Context window limitations remain the single most discussed frustration across every community researched. Even with Claude's 200K-token standard window (roughly 500 pages of text) and GPT-5's 128K-token window for Pro users, practical editing of thesis-length documents fails in ways that token counts alone don't predict.

The core technical issue is **context degradation** — what practitioners call "context rot." As one analysis of r/ChatGPT discussions noted: "The first ten messages in a ChatGPT thread are usually great. The AI remembers your brief, follows your tone instructions, and produces output that matches what you asked for at the start. By message thirty, something has shifted. The topic has drifted. The style is inconsistent." Even when earlier content technically fits within the context window, the model's attention mechanisms weight recent content more heavily, causing earlier instructions to progressively lose grip. Users describe a shift from confusion to competence when they reframe this: moving from "I lost my context" to "I'm managing my context."

Four chunking strategies dominate real-world practice. **Chapter-by-chapter processing** in separate conversation threads is the most common approach, with users providing a brief structural summary of the full thesis at the start of each thread. **Summarization anchoring** — creating condensed summaries at the end of each session and feeding them into the next — helps maintain continuity. **Claude Projects** allow uploading the thesis proposal, key source papers, and data once, with context persisting across sessions for weeks, a capability ChatGPT currently lacks. For the most granular work, **paragraph-level processing** of 300–1,000 words per interaction consistently produces the highest-quality edits according to multiple independent sources. A PhD in Computational Linguistics at ProofreaderPro found that quality drops significantly beyond 800–1,000 words per AI response, regardless of the model's total context capacity.

One critical limitation that no current tool solves well: **cross-chapter structural coherence**. Multiple practitioners confirmed that no AI tool they tested could reliably identify when chapter three undermines chapter two's argument. This macro-level structural editing still requires human judgment, though some workarounds exist — like pasting an abstract and conclusion together and asking "Do these align?" or running a dedicated consistency-check pass after editing all chapters individually.

---

## Line-by-line editing: the prompts and workflows that actually work

The most effective academic editors on AI have converged on a counterintuitive principle: **ask for error lists, not rewrites**. The academic writing blog Ref-n-Write crystallized what many discovered independently — asking AI to "list all spelling, tense, grammar, and punctuation errors" produces more useful output than asking it to rewrite text, because it keeps the author in control and avoids introducing AI writing patterns that detection tools can flag.

Professor Lennart Nacke, an HCI researcher who has tested prompts across fields from neuroscience to architecture, shared his core editing prompt: "Act as an academic editor. You prefer active over passive voice. Review the following text. Rewrite it to enhance clarity, conciseness, and formality, ensuring an objective and academic tone, but not to make it sound too stiff. It should be formal but easy to read and lively through variation of sentence and paragraph lengths." He reported this "consistently outperforms generic requests" because it specifies tone, voice preference, and readability constraints simultaneously.

For catching redundancy and weak argumentation specifically, a prompt template from Intellectual Lead's academic prompt library stands out for its precision: "Be my concision editor. Goal: cut 10–20% of words while preserving meaning and citations. Produce a tightened version. List a change log with before → after examples for 8–10 representative edits. Mark any low-value sentences to delete." The change-log requirement forces the AI to make its reasoning transparent, which users report dramatically increases the usefulness of feedback.

Ilya Shabanov, an academic coach at The Effortless Academic, discovered through writing an entire paper with heavy AI assistance that **AI cannot evaluate semantic coherence beyond the sentence level**. He recounts feeding AI notes where key terms were accidentally reversed — confusing "increase" with "decrease" — and watching it "produce fluid, grammatically correct prose and argue for that mistake with confidence." This finding has profound implications: AI excels at surface-level polish but cannot reliably catch deep logical errors without explicit instruction and structured context.

The solution Shabanov and others converge on is the **bullet-point scaffolding method**: begin every section with bullet points capturing the logical progression (A leads to B, then C, then conclusion), then let AI expand these into prose. Without this scaffolding, "AI generates relevant but logically disconnected prose." With it, output improves substantially because the logical structure is externally imposed rather than relying on the model's reasoning.

---

## Simulating the skeptical advisor who reads everything twice

The "AI as supervisor" use case generates the most enthusiastic responses across communities. Professor Inger Mewburn, the Thesis Whisperer at Australian National University, developed four prompts that have become widely cited:

- **Argument stress-testing**: "Criticize this text from the point of view of someone who doesn't believe [insert your key argument]"
- **Peer review simulation**: "Can you peer-review this text for me, look for logical argumentation problems?"
- **Viva preparation**: "Now pretend you are an academic in [discipline] — ask me six difficult questions about this text"
- **Devil's advocate**: "You are a person who doesn't believe [your key proposition], now ask me five difficult questions"

Her assessment of the peer review prompt: "ChatGPT is surprisingly good at actually naming the logical errors, and I would rather hear it from a machine than get a desk reject." For the viva preparation prompt: "It's not perfect, but honestly, it's quite close" to what a real panel would ask. She emphasizes this is especially valuable for **isolated researchers** who lack colleagues willing to read and critique their work.

However, testing by the Academic Research Skills project on GitHub exposed two significant failure modes. **Frame-lock**: when asked to run a devil's advocate debate against its own thesis, the AI attacked arguments but never premises — it never asked "are we even discussing the right question?" **Sycophancy under pushback**: every time the user challenged the devil's advocate's attacks, the AI conceded too quickly, "retracting findings faster than it launched them." These limitations mean AI supervisor simulation works best as a first pass to identify obvious weaknesses, not as a replacement for genuine adversarial critique.

A more sophisticated approach comes from the **hostile reviewer prompt** used by the ProofreaderPro workflow: "Read this as a hostile peer reviewer. What are the three weakest points in this argument? Be specific." The specificity constraint — asking for exactly three points — forces the model to prioritize rather than generating a generic laundry list, and the "be specific" instruction combats the tendency toward vague suggestions like "improve clarity."

---

## Macro-level structural review and the tools built for it

Evaluating whether thesis sections actually fulfill their structural roles requires a different approach than line editing. Users report two distinct methods: using general-purpose LLMs with specialized prompts, or using purpose-built tools.

For LLM-based structural review, the most effective approach involves **feeding the table of contents plus section summaries** rather than full text. One prompt from Intellectual Lead asks AI to "take this thesis statement and build a one-page map: Main claim, 3–5 supporting claims, evidence needed for each, counterarguments and rebuttals, status (existing/to collect/risky)." This argument mapping reveals structural gaps that close reading often misses. A complementary prompt asks AI to review abstract and conclusion together for alignment — a quick consistency check that catches drift.

Among purpose-built tools, **Thesify** has gained traction for providing "reviewer-style comments" on thesis statement clarity, evidence support, chapter coherence, gaps in logic, and unclear transitions. Critically, it does not rewrite anything — it generates a downloadable feedback report functioning as a structured revision roadmap. **Gatsbi** analyzes logical connections across chapters and handles equations and citations, focusing on organization rather than generation. The distinction matters: users consistently prefer tools that diagnose over tools that prescribe, because diagnostic feedback preserves the author's ownership of the text.

The most technically sophisticated system discovered is an **open-source 10-stage pipeline** on GitHub (Academic Research Skills by Imbad0202) that simulates an entire editorial workflow: Research → Write → Integrity Check → Review (5-person simulated panel) → Socratic Coaching → Revise → Re-Review → Re-Revise → Final Integrity Check → Finalize. The simulated review panel includes an Editor-in-Chief, three dynamic reviewers, and a Devil's Advocate, each scoring sections on 0–100 quality rubrics. Between review and revision stages, a "Socratic revision coaching" module uses a State-Challenge-Reflect loop. This pipeline also includes **AI writing-tic detection**, flagging 25 high-frequency AI terms, suspicious punctuation patterns, "throat-clearing" openers, and structural repetition. It requires Claude Opus on the Max plan and represents the current ceiling of what's possible.

---

## The frustrations everyone encounters — and what they reveal

Five frustrations appear so consistently across sources that they constitute near-universal experiences.

**Hallucinated citations remain dangerous.** Every model fabricates references with plausible titles, journal names, and even DOIs. Nacke notes that "ChatGPT cites completely fabricated 2022 meta-analyses about possibly even your own exact research topic." NotebookLM's RAG architecture reduces hallucinations to roughly **10%** compared to **40%** for ChatGPT and Gemini in one study with a 300-document corpus, but no tool is fully reliable. The universal advice: treat any AI-generated citation as a lead to verify, never as a fact.

**Generic, mediocre output is the default.** Nacke's verdict is blunt: "Most AI-generated academic text feels like fast food: convenient, predictable, and utterly forgettable. You could call it a faster way to mediocrity. It averages out any intellectual stimulus that you possess." As Ethan Mollick explains, cited by Mewburn, "By default, the AI gives you answers from the center of the cloud, the most likely answers to the question for the average person." Breaking past this requires aggressive specificity in prompts, domain-specific Custom GPTs, and using AI to polish human-written drafts rather than generate from scratch.

**AI develops detectable writing tics.** Shabanov discovered that AI "leans to certain ticks, regardless how often I try to prompt not to do something. One example is the overuse of the em-dash." Beyond punctuation, certain sentence structures, transitions, and vocabulary choices recur across AI outputs. Karolinska Institutet's guidelines warn that AI may silently change domain-specific terminology — substituting "vaccine efficacy" for "vaccine effectiveness," terms with importantly different meanings in medical contexts.

**The emotional experience changes in uncomfortable ways.** Shabanov offers a remarkably candid reflection: "Writing with AI shifted my role from being a direct author to something more like a supervisor or curator. Rather than composing sentences, I was directing AI, verifying its output... While the final product was often strong, the process didn't always feel like 'deep work.' In fact, sometimes I just felt emotionally unsatisfied, because all I did was write prompts." This existential dimension rarely appears in guides but matters for sustained thesis work.

**Privacy concerns create real constraints.** Mewburn won't put "original research material like survey material and interview transcripts in Claude, or any other AI" due to privacy concerns. The PhD Academy warns: "Under no circumstances should you trust online AI platforms with interview data! If you do, you may be in breach of ethics or data privacy rules." This effectively excludes AI from helping with qualitative data analysis in many research contexts.

---

## Best practices distilled from collective trial and error

Across hundreds of user experiences, six principles have been validated repeatedly enough to constitute genuine best practices.

**Write first, edit with AI.** This is the single most consistent recommendation. PhD student Yonis, quoted in Nature: "If you let AI write and then you look, you'll struggle to break away from the example. It's better to write it yourself and then use AI to clean up." Multiple independent sources confirm that starting with your own draft — even a rough one — and using AI to tighten, question, and refine produces dramatically better results than starting from AI-generated text.

**Build custom configurations rather than using vanilla models.** Shabanov is direct: "Without customization, none of [the tools] do what I need." He built specialized Custom GPTs — "WritingWanda" for polishing paragraphs and "FocalExtraction" for extracting quotations from papers. Claude Projects allow uploading 2–3 papers representing your writing style for style calibration, plus your thesis proposal and key sources. The most advanced practitioners, like economist Pedro Sant'Anna of Emory University, build entire **skills-based architectures** with 22 reusable commands (e.g., `/proofread`, `/review-paper`, `/validate-bib`) and multi-agent review systems with automated quality scoring.

**Follow a strict editing sequence: structure → argument → language → grammar → citations → voice.** Multiple sources independently converged on this ordering. Use tools like Thesify or structural prompts first for macro-level review. Then test arguments with hostile reviewer prompts. Then edit language with Claude or Writefull. Then check grammar with Grammarly or similar. Verify all citations manually. Finally, do a "voice pass" to restore your personality to AI-smoothed text.

**Copy-paste text rather than uploading PDFs.** Shabanov found that "uploading a PDF and adding a prompt yielded worse results than copying the text from the publisher's website... AI seemed to struggle with PDF formatting." This simple workflow change improved output quality across multiple tasks.

**Use multiple AI tools for different tasks rather than one tool for everything.** The most productive users combine Claude for deep editing and argument review (leveraging its larger context window and stronger reasoning), ChatGPT for brainstorming and organizational tasks, NotebookLM for source-grounded literature analysis with minimal hallucination, and Perplexity for research discovery with cited sources. A colleague of Nacke's describes asking AI to "write three different viewpoints on her research questions" — using AI for perspective multiplication rather than content generation.

**Treat context management as a first-class skill.** Experienced users plan their context budget the way programmers plan memory allocation. They use separate threads for separate chapters, include structural summaries at the start of each session, restate key constraints when they notice drift, and create "handoff prompts" when context erodes — starting a new thread with the current draft and the three most critical constraints carried forward.

---

## Conclusion: designing systems for AI-assisted thesis completion

The collective experience of thousands of thesis writers points toward a clear system architecture for AI-assisted thesis editing. **The most effective approach is a multi-stage pipeline, not a single tool.** This pipeline separates diagnostic functions (structural review, argument testing, consistency checking) from editing functions (prose tightening, grammar correction, style calibration), with human judgment as the integration layer between stages.

Three insights stand out as genuinely novel. First, the **paragraph-level processing principle** — that 300–1,000 words per interaction produces better results than whole-document processing regardless of context window size — suggests that the bottleneck is not technical capacity but attention quality. Second, the emergence of **adversarial prompt patterns** (hostile reviewer, devil's advocate, viva simulation) as the highest-value use case reveals that AI's greatest contribution to academic writing may be as a thinking partner rather than a writing partner. Third, the **emotional dimension** — the shift from author to curator that some find unsatisfying — points to a design challenge beyond functionality: tools that preserve the writer's sense of intellectual ownership will likely see greater adoption and more sustained use.

The open-source pipelines from Sant'Anna and the Academic Research Skills project represent the current frontier, with multi-agent review panels, quality scoring gates, and AI writing-tic detection. But the most widely applicable insight is simpler: students who use AI to ask harder questions about their own writing — rather than to avoid writing altogether — produce better theses and develop stronger skills in the process.