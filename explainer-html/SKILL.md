---
name: explainer-html
description: Write a first-principles explainer HTML page that teaches a codebase concept, an incoming task, a bug investigation or a fix plan, then publish it as an artifact. Use when the user asks for an "explainer", "explainer html", "explain this in an html page", a study/digest page, or wants to understand the surrounding concepts before starting a task. Plain-language definitions before jargon, analogies written as comments inside real code, concept blocks, before → after → what-it-fixes, senior tips, practice quiz, glossary.
---

# Explainer HTML

A single-file HTML page for a reader who may not know any of the surrounding jargon yet. It teaches from first principles and is grounded in the real code. Start from `template.html` in this folder; it holds the stylesheet, every component and the quiz script.

## 1. Research before writing

- Read the actual code. For anything broad, fan out to subagents in parallel (one per topic) if your tool supports them; otherwise search topic by topic. Collect file:line refs and short verbatim excerpts (5–25 lines) you can quote.
- If the user gave an investigation, findings or claims, verify each one against the code. Give a verdict per claim: confirmed / partly / not confirmed / not live.
- Personally re-check any surprising claim (run the library's own function, read the line) before it goes on the page.
- Note extra issues found along the way, but keep them visibly separate from the task's scope.

## 2. Teaching rules (most important)

- **Define before use.** Introduce every term in plain language the first time it appears, before any sentence relies on it. Don't rely on the glossary to do this. Before publishing, scan for jargon used before it's defined and fix each one.
- **Analogies live inside the code.** Put the everyday comparison as a comment on the exact line it explains, e.g. `// like a bouncer who checks your ID at the door`. Don't open with a standalone analogy paragraph and map it to code afterwards.
- **Walk through behaviour step by step**, with concrete numbers: what is read, what is decided, what is written, in what order, and why. Don't just state rules.
- **Fixes are always three parts:** a "Before · file:line" code block, an "After" code block (label it "sketch" if it's not drop-in), and a "What it fixes" line in plain words. Add caveats where the obvious fix isn't enough on its own.
- **Concurrency and timing** go in race strips: a time | actor A | actor B | DB state table, with the bad rows marked.
- Quote real code with `file:line` captions. Trim with `...`, and never invent code that is presented as existing.
- Plain, direct sentences. No em-dash asides, no "not X but Y", no stock phrases.

## 3. Page structure

1. Eyebrow (project · scope · branch/ticket), H1 name, lede saying who it's for.
2. TOC, grouped into parts with `.tocpart`.
3. **Part 1 · Foundations**: the domain model and mechanisms the task touches, with concept blocks (What / Everyday / Where / When-or-Why).
4. **One part per task or topic**:
   - what exists today, with annotated code;
   - where it falls short (walkthroughs with numbers);
   - an at-a-glance status table with pills;
   - for investigations, a "checked against the code" verdict table;
   - **"What the task is asking for"**: a plain-words restatement, a checklist of deliverables, before → after fixes, and decisions to make, flagged in an aside.
5. **Wrap-up**:
   - **"<Data stores/tools> worth knowing"**: only niche commands and options the covered code actually uses (e.g. findOneAndUpdate with a state filter, `$first` without `$sort`, `SET NX EX`, cron step syntax, ack/prefetch). For each: what it does, the path where it's used, and why it beats the obvious alternative. Skip basics.
   - **Senior engineer tips**: the practice, when it applies and when not, the file it applies to, and the trade-off. Opinions with reasons.
   - **Practice**: 4–8 items grounded in the page's real code. Mix multiple choice (`data-answer` + `.why`) with spot-the-problem (`<details class="ans">`). Plain JS, no storage.
   - **Glossary**, grouped.

Use concept blocks generously, one per named concept. Add an inline SVG figure only where a mechanism or timeline needs one. Colour it through the `.svg-*` classes so it works in both themes.

## 4. Visual style (already in template.html)

- Page-1 palette with light and dark tokens (bg #fbfbfa / #15191d, accent #2b5d83 / #86b7dd), with a concept purple and a warn red.
- Geist 15px body, 17px/500 headings, Geist Mono for code, paths and identifiers. **No bold anywhere.**
- One 75ch column; highlight.js 11.9.0 from cdnjs, coloured through CSS variables; no line numbers.
- Only change tokens or add components when the subject truly needs it. Keep the page consistent with earlier ones.

## 5. Save and publish

- Title: a 2–4 word name, never "X: explainer". Put the one-sentence summary in `<meta name="description">`.
- The template has no `<!doctype>`/`<html>`/`<head>`/`<body>` wrapper, because artifact hosts add it. For a standalone file opened locally, wrap it: `<!doctype html><html lang="en"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width, initial-scale=1">` … `</head><body>` … `</body></html>`. The `<title>`, links and `<style>` go in head; `<main>` and the script go in body.
- Save the file in the project: use `docs/` if it exists and is gitignored. Otherwise ask, or use a scratch directory.
- If your tool can publish pages (e.g. Claude's Artifact tool; load its design skill first if it asks), publish there too. Otherwise give the user the file path to open in a browser.
- Record the path (and URL if published) wherever the project or tool keeps notes (memory, AGENTS.md, a docs index), so later pages can link to it or match its style.
- In the reply: the link or path, then a few short lines on the most important findings (especially anything that corrects the user's assumptions). Don't repeat the page.
