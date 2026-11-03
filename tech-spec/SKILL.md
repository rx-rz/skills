---
name: tech-spec
description: Write a tech spec plus step-by-step implementation plan for an incoming task, as a single HTML page grounded in the real code, then publish it. Part 1 is a short spec scoped strictly to the ticket, told in plain prose with one marked-up code block and a diagram per step. Part 2 walks through the same task Feynman-style, smallest idea first, with a picture for every flow, race and before/after. Use when the user asks for a "tech spec", "spec", "design doc", "implementation plan", "RFC" or "plan for this ticket", or wants code steps to review before coding. Same look and voice as explainer-html: prose over tables, funny and plain, every claim tied to a line of code.
---

# Tech spec

A single-file HTML page that gets a task from "ticket" to "ready to code and review". It is the `explainer-html` skill pointed at one ticket: same fonts, same palette, same voice, same habit of drawing instead of describing. The difference is scope and length. An explainer teaches a whole system; a spec teaches exactly the change the ticket asks for, and stops.

Part 1 is the spec a reviewer reads in five minutes. Part 2 is the same plan explained properly, for the person doing the work. Start from `template.html` in this folder; it holds the stylesheet, fonts, figure classes and every component.

If `explainer-html` is available, read its sections 3 to 5 (how to teach, diagrams, voice) before writing. This file repeats what matters for specs and adds what's specific to them.

## 1. Research before writing

- Read the code the ticket touches, plus every caller of anything you plan to change. For broad tasks, fan out to subagents (one per area) and ask for file:line refs and short verbatim excerpts.
- Map the blast radius: every place that reads or writes the thing you're changing. Use it to decide scope. Whatever the ticket doesn't ask for goes in "Left out on purpose".
- Check each claim in the ticket against the code. The verdicts go into the prose ("the ticket says the job retries forever; it retries three times, `jobs/sync.ts:88`").
- Find existing helpers before proposing new ones, and name them where the plan uses them.
- Anything you can't confirm goes under "Questions to settle before coding", with the file:line that raised the doubt. Never present a guess as fact.
- Make a figure list while you plan: one picture per step at minimum (section 4).

## 2. Voice and teaching

Same rules as `explainer-html`. The ones that bite hardest in specs:

- Prose carries the page. Paragraphs, short and varied, talking to "you". A reviewer should be able to read Part 1 top to bottom like a short letter, not scan a grid.
- Know the thing, then the name. "the code that takes money out of a wallet (`debitUserWallet`, `wallet/debit.ts:41`)", never the name alone.
- Name every actor concretely. Never "the worker", "the consumer", "the job" on its own. The first time an actor appears in a section, say what it physically is: the process (pm2 app name, container), the file and function, and what sets it off (queue, cron schedule, route, webhook). When two actors clash, say they're separate programs that only share the database row.
- Point at the line for every claim about what code does. Re-check line numbers just before publishing.
- Start from ordinary life. Pick one or two anchor pictures in the overview (a hotel card hold for a lien, two cashiers paying the same cheque for a race) and reuse them; say where the analogy breaks.
- Ask the reviewer's question before they do ("why not just lock the row?") and answer it right there. This replaces the old alternatives table.
- Reduce the plan to one sentence or formula and say it early: "free = total minus frozen"; "set it to X, but only if it's still Y".
- Funny means dry and observational, and never costs accuracy. No em or en dashes, no bold in prose, no emoji, no "let's dive in", no rule-of-three habit, no "not X, but Y", no puffed-up words. Run the `humanizer` skill's audit pass at the end if it's available.

## 3. Tables: almost never

The old spec was a stack of tables. Don't do that.

- Testing becomes a `.checklist` of observable results, plus a sentence on which ones fail today.
- Open questions, follow-ups and rejected alternatives become short prose or a short list, each item with its file:line.
- Monitoring and rollback is a paragraph.
- A table is allowed only when the content really is rows and columns and prose would be worse: a verdict per ticket claim when there are five or more, or a race strip (time | actor A | actor B | row state) for a timing bug, and even then pair the race strip with a figure. Most specs should have zero or one table.

## 4. Diagrams

The reader should never have to picture a flow you could have drawn. Draw:

- the call chain for each step: trigger, then each function down to the one you edit, then who reads the result;
- before and after for each behaviour change, side by side, with real values;
- every race or ordering problem as two lanes over time, with the bad moment marked;
- the data shape when the change touches one (a document with the field you add, a queue message, a state machine with the new transition).

Aim for at least one figure per plan step, and one per Part 2 movement. Use the `.figure` / `.figcap` markup and colour only through the `.svg-*` classes so it works in both themes (the template has a call-chain example and a two-lane example). Render-check figures (screenshot or `rsvg-convert`) for clipped labels and overlap, in light and dark and at 375px wide. If drawing the real thing shows the prose overclaims, fix the prose.

## 5. Part 1 · The spec

Scope: only what the ticket asks for. Every plan step must serve a line in the ticket. Anything else you find goes in "Left out on purpose" or "Questions to settle", never in the plan. When unsure, leave it out and say so in chat. A good spec is short; four plan steps is normal.

1. The short version. Two or three paragraphs: what the change does, the gap in today's code with the file:line that causes it, and the one-sentence formula. Then a figure of the whole change (before vs after). Goals follow as a short bulleted list straight from the ticket.
2. What changes in the data. One or two sentences: the change, or "nothing" plus the field that already exists (file:line). Say if API consumers will notice; flag anything breaking in an `.aside`.
3. The plan. Numbered `h3` steps, one per function you edit (split a step that edits two). Each step:
   - a short paragraph: the function, what triggers it (process plus cron/queue/route, file:line), what changes, and why in plain words;
   - a call-chain figure if the path to this function isn't obvious;
   - one code block of the real surrounding code with a `.codecap` of file:lines. Enough context to see where the edit sits, unrelated lines trimmed to `// …`, a `// new` comment on its own line directly above each changed or added line, or one `// new` above a whole run of new lines (a new file gets a single `// new` at the top). Never a trailing `// new` at the end of a line. No separate before/after blocks;
   - an optional `.aside` for a real gotcha, and a `.fixes` line saying what it fixes.
4. How we'll know it works. A `.checklist` of observable results, a sentence on which fail today, and a one-line bug bash recipe.
5. Watching it and backing out. One paragraph: what to watch after release, how to roll back.
6. Questions to settle before coding. A short list, each with where (file:line) and who decides.
7. Left out on purpose. A short list of follow-ups outside the ticket, each with file:line, plus any "why not just…?" alternatives that didn't fit naturally into the steps.

## 6. Part 2 · The task, explained

Explainer style, scaled to the task: usually 3,000 to 8,000 words of prose, smallest idea first, a figure every few hundred words. Numbered `h2` movements continue Part 1's numbering.

1. The pieces you need. One movement per idea the task leans on, starting from ordinary life and ending at the real code. Only what this ticket needs; this is not the whole-system explainer.
2. What's wrong today, and who it hurts. Walk through the failing case step by step with real values and a figure.
3. The steps, one movement per Part 1 step (same numbers). Don't repeat Part 1's code block; build around it. Each movement answers, in prose, with file:line for every fact:
   - how you get here: the chain from the real trigger to the function, with its figure;
   - what you already have: the variables in scope, what each holds, where it was read, and any surprises (read before several awaits; one partition rather than the whole wallet);
   - what happens next: who receives the result or reads what you wrote, and what they do with it today. Checking callers often turns up holes in the plan (a caller that ignores a new `stale` result). Fix the step in Part 1 and say so here;
   - watch out for: real gotchas only.
   End each movement with a short `.cheat` (gist bullets, and a snippet only if there's something copy-ready).
4. How you'll know you're done, in plain words, matching Part 1's checklist.

## 7. Wrap-up

- Tools worth knowing, only for less common things this task uses (`findOneAndUpdate` with a status filter, `bulkWrite`'s `matchedCount`, `SET NX EX`): what it does, where it's used, why it beats the obvious option. Prose, not a table.
- Senior engineer tips, only when a real one fits a real file. Leave the section out otherwise.
- Practice: 4 to 6 questions grounded in this task's code, mixing multiple choice and "explain it back".
- Glossary, one plain sentence per entry.

## 8. Answering follow-up questions about the spec

- When the user asks what something means, answer from the code (file:line) in two or three plain sentences, starting from an everyday picture, and offer to fold it into the page.
- If answering turns up something the spec gets wrong, say so and fix both Part 1's step and Part 2's movement. Outside the ticket, it goes in "Left out on purpose".
- When asked to make the page plainer, follow the rewrite approach in `explainer-html`: back up first, rewrite prose only, keep facts, code and ids, and list doubts instead of silently fixing them.

## 9. Visual style (already in template.html)

Identical to `explainer-html`: Newsreader for headings and the dek, Instrument Sans 15px body at about 1.75 line height, Geist Mono for code, paths and figure labels; headings never above 17px; one ~66ch column; dark-first neutral palette with a light variant; saturated accents (`--c-purple`, `--c-teal`, `--c-orange`, `--c-pink`, `--c-blue`) for figures only; highlight.js 11.9.0 from cdnjs. `// new` markers on changed lines turn teal automatically. Page-specific figure CSS goes in a second `<style>` block using only the tokens.

## 10. Save and publish

- Title: a 2–4 word name ("Wallet Lien Spec"). The one-sentence summary goes in `<meta name="description">`.
- The template has no `<!doctype>`/`<html>`/`<head>`/`<body>` wrapper, because artifact hosts add it. For a standalone local file, add the wrapper.
- Save in the project's gitignored `docs/` if there is one; otherwise ask, or use the scratchpad. Publish with the Artifact tool when available, and republish to the same URL on every update. If it was published before, compare the live version with your local file first so edits made elsewhere aren't lost.
- Record the path and URL wherever the project keeps notes.
- In the reply: the link, then a few short lines on what matters most, especially breaking API changes and questions that block coding.
