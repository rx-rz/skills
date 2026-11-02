---
name: explainer-html
description: Write a long-form, Feynman-style explainer article as a single HTML page that teaches a codebase, a concept, an incoming task, a bug investigation or a fix plan, packed with diagrams, then publish it as an artifact. Use when the user asks for an "explainer", "explainer html", "primer", "explain this in an html page", a study/digest page, a long read about how something works, says a page is too dense or "technobabble", or wants to understand the surrounding concepts before starting a task. Free-flowing prose, funny and plain, smallest idea first, grounded in the real code, with a diagram for nearly every idea so the reader never has to picture it alone. Opens with a contents list, ends each section with a cheatsheet, and closes with a practice quiz and a glossary.
---

# Explainer HTML

A long, free-flowing article that teaches the way Richard Feynman taught: start from something the reader already understands from ordinary life, build one small idea at a time, and only widen the scope once the smaller thing is solid. It reads like a magazine long read written by a friend who is enjoying explaining this, not like documentation. It is grounded in the real code, and it is full of pictures: the reader should never have to imagine a shape, a sequence or a before/after that you could have drawn for them.

Start from `template.html` in this folder; it holds the stylesheet, the fonts, every component (contents list, figure, code block, aside, table, cheatsheet, tip, quiz, glossary, end note) and the quiz script.

Two pages show the target voice and diagram density: `docs/iconography.html` and `docs/nib-generator.html` in the Formtune repo ("Iconography, from scratch" and "How Nib draws"). When in doubt, read a stretch of the Nib one and match it. They predate the rest of this spec (17px headings, contents list, cheatsheets, quiz, glossary), so follow the template for those.

## 1. What the page is (and is not)

- One article. Paragraphs carry it. Numbered `h2` movements (about 8 to 14), occasional `h3`.
- It opens with a contents list (grouped into parts that go from small to wide) and every numbered movement ends with a cheatsheet. The wrap-up has senior engineer tips (only if they fit), a practice quiz and a glossary. Details in section 6.
- Tables go in when the content really is rows and columns: a side-by-side comparison, statuses with pills, a verdict per claim, a race strip of two actors over time. Never as a substitute for explaining in prose. Other lists stay rare and short.
- Length: a whole system or a broad concept gets at least 15,000 words of prose (code excluded). A narrow topic (one bug, one function) can be shorter, but it is still long-form and never skimps on explanation to hit a smaller number. When short, add substance (more worked examples, more history, more "why not just...", more detail), never padding.
- Senior engineer tips go in only when they fit the bill: a real practice tied to a real file in the code being explained, with when it applies, when it does not, and the trade-off. Weave them into the prose where they come up, or, if several earn a place, one short closing section. If nothing genuinely fits, leave them out. Never invent tips to fill a slot.

## 2. Research before writing

- Read the actual code. For anything broad, fan out to subagents in parallel (one per topic). Collect file:line refs, real numbers and short verbatim excerpts you can quote.
- If the user gave an investigation, findings or claims, check each one against the code. The verdicts go into the prose ("this part is true, this part is not live yet, here is the line").
- Re-check anything surprising yourself (run it, read the line) before it goes on the page. If you find a confirmed bug, tell the user and offer to fix it before documenting; describe only unbuilt features as gaps.
- List the ideas the page needs and sort them by size: what must the reader understand first for the next thing to make sense? That order is the skeleton.
- Make a figure list at the same time as the skeleton (section 4). Every idea on the list gets at least one picture unless it truly cannot be drawn.

## 3. How to teach

Feynman's test: if you can't explain it simply, you don't understand it yet. Go back to the code until you can.

- Smallest thing first, then widen. Open with the tiniest piece that stands on its own. Each later section uses only what came before and says how the new thing is built out of the old. Never introduce a big picture the reader has no pieces for yet.
- Start every idea from something the reader already knows from ordinary life (a highlighter, a road sign, a rubber band, an undo button, squinting at a phone at 2am). Then show the real mechanism, and say where the analogy breaks. An analogy is a ladder to climb, not a place to stay.
- Know the thing, then the name. Describe what something does in plain words first and give the jargon afterwards: "a list of changes waiting to be sent to the server. The code calls it the sync queue."
- Ask the question the reader is about to ask ("why not just make the big icon smaller?") and answer it. Show the naive approach, show it failing with real numbers, then show the fix.
- Pick the best example for each point. Don't drag one running example through everything; switch when another case shows the point more clearly, and say so in a line. Running jokes and recurring examples are fine when they are earned.
- Show it happening: walk through behaviour step by step with concrete values. One worked case beats a paragraph of description.
- Say what we don't know. Unfinished, unclear or unconfirmed things get said plainly, with the file:line that raised the doubt. Admit where something is a matter of taste.
- Fixes: a "Before · file:line" code block, an "After" block (say "sketch" in the codecap if it's not drop-in), and a plain sentence on what it fixes.
- Quote real code with a `<p class="codecap">path:line</p>` above the `<pre><code class="language-…">`. Trim with `...`, never invent code presented as existing, escape `< > &`. Explain every quoted line in the prose around it. Keep code sparse for concept pieces and moderate for code walkthroughs.

## 4. Diagrams: pump the page full of them

The reader should never be left to visualize. If a sentence describes a shape, a path, a sequence, a size, a before/after, a comparison, a failure, a data flow or a process, draw it. Aim for roughly one figure every 250 to 400 words of prose; a 15,000-word article should carry 40 to 60 figures. More is better than fewer, as long as each one shows something the text is saying right there.

- What to draw: every analogy (the highlighter and the mark it leaves), every mechanism step by step (stages side by side), every naive-vs-real comparison, every number that matters (a dial, a number line, a bar), every failure (the clipped, clogged, invisible, racing case) next to its fix, every process (pipelines, who calls whom, run order, timing between two actors), every data shape (a tree, a path traced point by point, a grid of pixels), every bug story (before/after with the real colours).
- Draw from real data whenever possible. Write a script (Python or Node) that imports or reruns the project's own code and emits the SVG, so geometry, sizes and numbers are the real ones, not sketches. Keep the script in the scratchpad, have it record which paragraph each figure goes after, and let an assembler insert them. Hand-drawn SVG is fine for simple concept diagrams.
- Markup: `<div class="figure"><svg viewBox="…" role="img" aria-label="…">…</svg></div>` followed by `<p class="figcap">…</p>`. Colour only through the `.svg-*` classes in the template so it works in both themes. Literal colours only when the figure is about literal colours (then use the real ones from the code).
- Captions are one or two plain sentences saying what to look at. Keep labels short and clear of the strokes.
- Check every figure by rendering it, not by reading the SVG: render to PNG contact sheets (wrap each SVG with the template CSS and run `rsvg-convert`, or screenshot the page) and look for clipped labels, overlapping text, strokes overflowing the panel and wrong scale. Fix them, then check light and dark and a 375px-wide viewport.
- Honesty: if drawing the real geometry shows the text overclaims, fix the text and the caption. The picture wins.

## 5. Voice: funny, plain, human

Read and follow the `humanizer` skill in full before writing; its rules are part of this one. Hard constraints:

- No em dashes or en dashes anywhere. Scan for `—` and `–` before finishing.
- Straight quotes, sentence-case headings, no bold or `<strong>` in prose, no emoji.
- No "let's dive in" signposting, no rule-of-three habit, no "not X, but Y" formula, no staccato fragment runs for drama, no puffed-up words (crucial, pivotal, testament, landscape, delve, showcase, underscore, vibrant, intricate).
- Funny means dry and observational: self-deprecating asides, small absurd comparisons, mild exasperation at things everyone has suffered. No memes, no "lol", no exclamation spam. Humour never costs accuracy.
- Plain words over formal ones ("sends", not "dispatches"). Talk to the reader. Name every actor concretely the first time (what it is, which file and function, what sets it off).
- Vary paragraph length a lot. Don't end every paragraph on a punchline.
- After drafting, do one audit pass asking "what makes this obviously AI-written?" and fix what you find.

When the user says they don't follow, answer in chat in two or three plain sentences with a different example, and offer to fold it into the page.

## 6. Page structure

All markup is in `template.html`.

1. Kicker (project · what this is about), `h1` name, dek (two or three sentences: what this is, who it's for, what they'll be able to explain afterwards), byline.
2. Contents list (`nav.toc`), grouped into parts with `.tocpart`. The parts go from small to wide, for example "The smallest pieces" → "How the pieces fit" → "The whole system" → "What's next". Every `h2` gets an `id` and a `.secnum`.
3. The numbered movements. Each one starts from what the reader knows, shows the idea with the best example, the real code and its figures, asks the obvious "why not just…?" and answers it, and closes with how this piece feeds the next one.
4. Cheatsheet: every numbered movement ends with one `.cheat` block holding what the reader should keep:
   - Gist: 2–4 bullets in the plainest words, as if telling a friend what you just learned.
   - Snippets: the smallest real, copy-ready code that applies the idea, each with a `codecap`. A movement with nothing to apply gets a one-line rule instead.
   - Use it when / Skip it when: one or two bullets each, where the movement implies a choice.
   - Nothing that isn't already in the movement. Wrap-up sections get no cheatsheet.
5. For tasks and investigations, a part after the foundations: what exists today, where it falls short (walked through with values and figures), a status table with pills, a "checked against the code" verdict table where relevant, and "what the task asks for" with a `.checklist`, before → after fixes, and decisions flagged in an aside. Timing problems get a race strip (time | actor A | actor B | state, bad rows marked) as well as a figure.
6. Wrap-up:
   - "<Tools/libraries> worth knowing", only when the code uses something less common: what it does, where it's used, why it beats the obvious alternative.
   - Senior engineer tips, only when they fit (section 1), each in a `.tip` with the file it applies to.
   - Practice: 4–8 items grounded in the page's real code. Mix multiple choice (`data-answer` + `.why`) with "explain it back" or spot-the-problem items (`<details class="ans">`). Plain JS, no storage.
   - Glossary, grouped. One plain sentence per entry.
7. `<p class="end">` final note with links to companion pages.

Other components: `<blockquote>`, `<div class="aside"><p>…</p></div>` for a genuine tangent, `<p class="sep">· · ·</p>` for a scene break, `.tablewrap` tables (always closed and bordered).

## 7. How to produce a long page

1. Write a brief in the scratchpad: reader, voice rules (this file + humanizer), verified facts with file:line, the skeleton, the figure list.
2. For long articles, split the writing across subagents (one per run of sections, each writing `partNN.html`) with the brief, then concatenate to `body.html`. Read the joins yourself and smooth them.
3. Generate figures with the script, insert them with an assembler, render-check them (section 4).
4. Checks before publishing: prose word count (strip `<pre>` and tags), zero `—`/`–`, balanced tags, every contents link resolves to an id, every movement ends in a cheatsheet, every quiz answer is right, every quoted path:line still true, the humanizer audit pass, figures checked in both themes and at phone width.

## 8. Visual style (already in template.html)

- Fonts: Newsreader for every heading (h1, h2, h3) and the dek, Instrument Sans for body text, Geist Mono for code, paths, identifiers and figure labels. Load them from Google Fonts as the template does.
- Headings are never larger than 17px. h1 and h2 are 17px, h3 is 15px; they stand out through the serif, weight and spacing, not size. Don't add bigger display type anywhere, including on mobile.
- Body text is Instrument Sans 15px, line-height about 1.75, one column of about 66ch.
- Dark-first neutral palette (black ground, #16181c and #202327 surfaces, #2f3336 hairlines, #e7e9ea text, #8b9096 muted) with a light variant under `prefers-color-scheme: light` and `data-theme`. Saturated accents (`--c-purple`, `--c-teal`, `--c-orange`, `--c-pink`, `--c-blue`) are for figures, used sparingly; text stays neutral. Follow the project's own colour roles when it has them.
- highlight.js 11.9.0 from cdnjs, coloured through the CSS variables.
- Extra page-specific CSS (new `.svg-*` classes for figures) goes in a second `<style>` block that uses only the tokens.

## 9. Save and publish

- Title: a 2–4 word name, never "X: explainer". The one-sentence summary goes in `<meta name="description">`.
- The template has no `<!doctype>`/`<html>`/`<head>`/`<body>` wrapper because artifact hosts add it. For a standalone local file, wrap it with doctype, charset and viewport meta.
- Save in the project's `docs/` if it exists; otherwise ask, or use the scratchpad.
- Publish with the Artifact tool when available (load its design skill first if it asks); otherwise give the file path.
- Record the path and URL wherever the project keeps notes (memory, AGENTS.md, a docs index).
- In the reply: the link, then a few short lines on what matters most, especially anything that corrects the user's assumptions or anything you couldn't verify (themes, phone width). Don't repeat the page.
