---
name: tech-spec
description: Write a tech spec plus step-by-step implementation plan for an incoming task, as a single HTML page grounded in the real code, then publish it. Part 1 is a short spec (goals, design, API changes, implementation plan with a "code changes, in order" timeline, testing, rollback, open questions, alternatives). Part 2 explains the task in plain words with before/after code. Use when the user asks for a "tech spec", "spec", "design doc", "implementation plan", "RFC" or "plan for this ticket", or wants code steps to review before coding. Written for a tired reader: plain English, one running example, anchor analogies.
---

# Tech spec

A single-file HTML page that gets a task from "ticket" to "ready to code and review". Part 1 is the spec a reviewer reads. Part 2 is the same plan in plain words for the person doing the work. Start from `template.html` in this folder. It uses the same look as the `explainer-html` skill, plus a timeline component for the implementation plan.

If the `explainer-html` skill is available, follow its research rules, teaching rules and plain-English voice (sections 1–3). This skill only adds what's specific to specs. If it isn't available, the essentials are repeated below.

## 1. Research before writing

- Read the code the ticket touches, plus every caller of anything you plan to change. For broad tasks, fan out to subagents (one per area) and ask for file:line refs and short verbatim excerpts.
- Map the blast radius: every place that reads or writes the thing you're changing. Put it in "scope at a glance" tables, so nothing is a surprise in review.
- Verify each claim in the ticket against the code: confirmed / partly / not confirmed.
- Find existing helpers before proposing new ones, and name them in Dependencies with file:line.
- Note problems found along the way under Follow-ups, separate from the task's scope.
- Anything you can't confirm goes under Open questions as "check this before coding", with the file:line that raised the doubt. Never present a guess as fact.

## 2. Voice

Assume the reader is smart but tired.

- Short sentences, one idea each, "you". Say what code does before naming it.
- Introduce one or two anchor pictures in the overview and reuse them throughout (for example: a hotel card hold for a lien; two cashiers paying the same cheque for a race).
- Use one running example with numbers (₦1m in the wallet, ₦500k frozen, so ₦500k free) everywhere: overview, steps, tests and quiz.
- Reduce the plan to one sentence or formula and repeat it ("free = total − frozen"; "set it to X, but only if it's still Y").
- Re-explain terms briefly in each major section, so readers can jump straight to a step.
- No em-dash asides, no "not X but Y", no stock phrases.

## 3. Part 1 · Tech spec

1. **Overview.** The problem in plain words, the ticket (lightly tidied) in an aside, Goals (observable outcomes), Non-goals, and "at a glance" tables of every affected path, with pills for today/after and the step that fixes it.
2. **Technical design.** Architecture as plain numbered steps (who calls what, in what order). Data model changes, or "no schema change" and why. **API design: spell out exactly what API consumers will notice**: new fields, changed units, new cases for existing webhooks. Flag anything breaking explicitly, and prefer adding a field over changing what an existing one means. Then security considerations.
3. **Implementation plan:**
   - **Dependencies:** existing helpers (file:line), product decisions needed, frontend work that follows.
   - **Timeline table:** phase, steps, estimate, deliverable.
   - **Code changes, in order.** This is the part reviewers use most:
     - Open with two to four everyday paragraphs: what the thing is (anchor picture), what goes wrong today, the one idea the plan repeats, and how the steps build on each other.
     - Then a `.timeline` of `.tstep` blocks, one per step. Each has a `.when` tag that says where you are ("start here", "the main fix", "same idea, next job", "after the product answer"), a plain title, and why this step comes now and what it sets up later. Write a lead-in sentence before every code block. End each step with a `.handoff` line saying what's now true and what the next step tackles.
     - After each step's first paragraph, add one `.ctxline`: the call chain in arrows from the real trigger to the edit ("daily cron → `autoSettlement` → your edit") and who uses the result. Keep it under ~35 words.
     - Code blocks show only the lines that change, with a `.codecap` file path (and line numbers when known). Mark changes inline: `// ← new`, `// ← was …`. Mark proposed code as a sketch when it isn't drop-in.
     - Order steps by dependency and risk: reliable foundations first, the main fix next, shared or risky code after the targeted fixes, decisions waiting on product last.
     - Call out ship-order hazards in the step itself, for example "don't ship this without step 4", when one change is unsafe without another.
4. **Testing strategy.** One test per changed path. Say which tests fail on today's code (write those first). Add success metrics.
5. **Monitoring and rollback.** What to watch after release, and how to undo each step and what that breaks.
6. **Open questions.** Decisions needed, with who owns them, plus "check this before coding" doubts.
7. **Follow-ups (out of scope).** Follow-up, where, why it's separate.
8. **Alternatives considered.** Each option and why not, in plain words.

## 4. Part 2 · The task in plain words

9. **Two-minute refresher:** concept blocks (What / Everyday / Where / Why) for each idea the task needs.
10. **What the task wants, and why it matters:** a plain restatement, and who is hurt today.
11. **What you'll do, step by step:** per step, a heading with its files, an `.aside` with the plain why, a `.ctx` context box (below), then "Before · file:line" code, "After (sketch)" code and a `.fixes` line. This section is the detailed companion to Part 1's timeline, so keep the two in step.
    **The context box** answers the questions readers actually ask about an edit: where a value comes from, what an object holds, who calls this code and who uses its result. Readers need that to understand a change rather than copy it. Build it from the code, citing file:line for every fact:
    - **How you get here:** the call chain from the real trigger (route, webhook, cron job, queue consumer, admin action) to the function you edit, plus one everyday sentence on what triggers it.
    - **What you already have:** the variables in scope that the change uses, what each holds, where it was read or built, and any surprises (read before several awaits; one partition rather than the whole wallet; a copy the caller may have changed). Add a 3–8 line real-code excerpt only if a line is confusing on its own.
    - **What happens next:** who receives the return value or reads what you wrote, and what they do with it today. Flag callers that ignore the result.
    - **Watch out for** (optional): real gotchas only, such as another caller of the same function or a shared helper other flows use.

    Writing "What happens next" means checking callers, and that often turns up gaps in the plan itself (for example, a caller that ignores a new `stale` result and still logs a retry). Fix the step and say so; don't bury it in the box. Skip the call chain when the edit is self-contained. Don't paste whole surrounding functions.
12. **How you'll know you're done:** a checklist of observable results.

## 5. Wrap-up

13. **Data stores and tools worth knowing:** only the niche commands and options this task uses (for example, `findOneAndUpdate` with a status filter, `bulkWrite`'s `matchedCount`, `$project` computed fields, `SET NX EX`).
14. **Senior engineer tips:** the practice, where it applies, the trade-off.
15. **Practice:** 4–6 questions grounded in this task's code.
16. **Glossary.**

## 6. Answering follow-up questions about the spec

- When the user asks what something in the spec means, answer from the code (file:line). Use the plain version first: the analogy, then the two or three facts that matter.
- If answering turns up something the spec gets wrong or leaves out, say so, and offer to fold it into both Part 1's timeline and Part 2's step.
- When asked to make the page plainer, follow the rewrite pass in `explainer-html` (back up first; rewrite prose only; keep facts, code, ids; list doubts instead of silently fixing them).

## 7. Save and publish

- Title: a 2–4 word name ("Wallet Lien Spec"). Put the one-sentence summary in `<meta name="description">`.
- The template has no `<!doctype>`/`<html>`/`<head>`/`<body>` wrapper, because artifact hosts add it. For a standalone file opened locally, add the wrapper.
- Save the file in the project's gitignored `docs/` if there is one. Otherwise ask, or use a scratch directory. If your tool can publish pages, publish there too, and re-publish to the same URL on every update.
- If the page was published before, compare the live version with your local file before overwriting it, so edits made elsewhere aren't lost.
- Record the path (and URL) wherever the project keeps notes, so later specs can link to it.
- In the reply: the link or path, then a few short lines on what matters most, especially breaking API changes and open questions that block coding.
