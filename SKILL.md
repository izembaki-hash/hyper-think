---
name: hyper-think
description: Structured high-performance reasoning protocol that raises the quality, precision and originality of hard tasks. Use whenever the user invokes /hyper-think, or asks for breakthrough or out-of-the-box solutions, inventions, novel designs, first-principles analysis, zero-defect or production-grade work, maximum accuracy, or says things like "think harder", "find a better way", "give me the best possible answer". Also use for non-trivial engineering, architecture, algorithm, math, strategy and decision problems where a generic answer would be mediocre. Arabic triggers - "فكر خارج الصندوق", "حل مبتكر", "اختراع", "أقصى أداء", "بدقة عالية", "أفضل حل ممكن". Do not use for trivial or purely mechanical requests (renaming, reformatting, simple lookups, small talk).
---

# Hyper-Think

## What this skill does, and what it cannot do

This skill raises the output of whatever model runs it up to that model's own ceiling. It cannot add capability the model does not have. What it removes are the usual causes of weak output:

- Premature convergence: taking the first, most common idea.
- Unverified claims: stating guesses with the same confidence as facts.
- Skipped edge cases: solving the happy path only.
- Truncated work: placeholders, "rest of the code", half-finished answers.
- Fake novelty: presenting a known technique as an invention.

Effort is matched to difficulty. Deep machinery on a trivial task wastes time and makes the answer worse, so the first step is always triage.

## Step 1 - Triage (always, one line)

Pick a level and state it in one line, for example: `Level: STANDARD - needs a design choice between known options.`

| Level | When | What to do |
|---|---|---|
| QUICK | Clear task, one obvious correct approach (bug fix, small function, direct question) | Do it directly, then run one real check. Skip steps 2, 3, 4 and 5. |
| STANDARD | Design or analysis with several plausible approaches; correctness matters | Full steps 2 to 6. Compare at least 3 candidates. |
| DEEP | Open problem, invention, hard trade-off, or a case where STANDARD answers are all mediocre | Steps 2 to 7 and load `references/triz.md`. |

Re-triage when the task turns out harder or easier than expected. Escalate to DEEP only when the standard candidates all fail a success criterion or the user explicitly asks for invention. A conventional answer is often the right one, and saying so is a valid result.

## Step 2 - Frame the problem (STANDARD and DEEP only; skip completely for QUICK)

For QUICK tasks: skip directly to the deliverable and run one check.
For STANDARD and DEEP tasks:
1. Restate the goal in one sentence.
2. Write 2 to 4 testable success criteria. A criterion is testable if someone could check it without asking you what you meant.
3. List hard constraints (language, platform, performance, budget, deadline, compatibility).
4. Handle ambiguity: if a missing fact would change the answer materially and the user is reachable, ask exactly one question. Otherwise list your assumptions in one or two lines and continue.

Reason: most wrong answers solve a slightly different problem than the one asked. Success criteria also give Step 6 something concrete to verify against.

## Step 3 - Choose the working mode

- Model with a private reasoning phase: run the steps internally and keep the visible answer tight.
- Model without private reasoning: write a compact worksheet (5 to 12 lines) before the answer. Without writing, such a model skips the steps and jumps to an answer. Template and rules are in `references/weak-model-mode.md`. When in doubt about which you are, use the worksheet.

## Step 4 - Diverge, then converge (STANDARD and DEEP)

1. Name the default answer: what a typical competent person would propose first. Write one line on where it may fall short. Do not discard it yet, because it often wins.
2. Generate 3 to 6 candidates that differ in approach, not in wording. At least one should invert an assumption of the default (do the opposite, remove a component, move the work to a different time or place).
3. Score the candidates against the success criteria with weights (1 to 5). Pick the winner by the numbers, and say what would change the decision.
4. Pre-mortem: assume the chosen solution failed a month from now. Write the three most likely reasons, and fix the cheap ones in the design now.

Reason: generating candidates before judging them is the single best protection against settling on the first idea.

## Step 5 - Radical innovation (DEEP only)

Load `references/triz.md` and follow it: bedrock invariants, the technical contradiction, the Ideal Final Result, resource inventory, inventive principles, cross-domain analogies. The rule behind it: do not accept a trade-off until you have tried to remove it. Then label the novelty honestly (see "Novelty labels" below).

## Step 6 - Build and verify with evidence

Build the complete deliverable. Then verify using the strongest available method, in this order:

1. Execute it (run the code, run the query, render the page).
2. Test it (tests written from the success criteria and edge cases).
3. Look it up (search for facts, versions, APIs, standards).
4. Recompute independently (derive it a second way without looking at the first).
5. Re-read against the success criteria (weakest method; a model tends to confirm its own mistakes).

Details, procedures and checklists: `references/verification.md` and `references/domain-checklists.md`.

If no tool can run code, trace the code by hand through these inputs: empty, one element, typical, maximum size, invalid, and (where relevant) concurrent. Then state plainly: "traced by hand, not executed."

### Confidence labels

Attach one label to each material claim or deliverable component:

- VERIFIED: directly executed via a tool command in THIS session, tested against a live runtime, or retrieved via a real-time search tool. (Never label pre-training memory, unexecuted formulas, or unverified claims as VERIFIED).
- LIKELY: standard practice, pre-training factual recollection, or reasoned deduction that was not actively executed or searched in this session.
- UNVERIFIED: working assumption, recollection, or untestable here.

Never use "guaranteed", "zero bugs" or "cannot fail" unless a proof or exhaustive test backs it. A calibrated answer is more useful than a confident wrong one.

## Novelty labels

When presenting an idea as new, check it first ("is this already known?"), searching if a search tool exists. Then label it as one of:

- KNOWN TECHNIQUE: exists under some name. Give the name.
- NOVEL COMBINATION: known parts, new arrangement or application.
- POSSIBLY NEW: no prior art found. Add "searched: yes or no" and what was searched.

Words like "patent-grade" or "unprecedented" are not allowed without a prior-art check. Honest labeling is what makes the real inventions credible.

## Step 7 - Deliver

Match the user's language and dialect (Arabic in, Arabic out; English in, English out). Do not add self-referential stamps, banners or mentions of this skill's name. Users want the result, not the machinery.

Default structure for STANDARD and DEEP answers:

1. Core idea: 2 to 4 sentences on the key insight or decision, and why the obvious route was not chosen (if it was not).
2. Deliverable: the work itself (see the environment rules below).
3. Evidence: what was actually run, tested or sourced, with the labels above.
4. Risks and limits: remaining uncertainty, what was not covered, how to extend.

QUICK answers: the result, then one line on what was checked.

## Environment rules

- File tools available: write code and long documents to files, and reply with the file path, a short brief, and the commands to run it. Do not paste large files into the chat. Short snippets (under about 15 lines) and diffs may go inline.
- No file tools: put the code in the chat in fenced blocks, split into labeled parts when long.
- Browser or preview tool available and the task produces an interface: serve it, open it, check the layout, the interactions and the console, and fix what you see. If no such tool exists, say the interface was not visually verified.
- Link format: use whatever link format the current tool uses. Never invent paths or URLs.
- Interfaces and visual design: load `references/ui-taste.md`.
- Tasks that will not fit in one response: load `references/long-tasks.md`.
- Worked examples of each level: `references/examples.md`.

## Admit limits

If you cannot reach a reliable solution, say so, show what you tried and why it failed, and give the best partial result with its gaps marked. An honest partial answer beats an invented complete one. Invented APIs, fake citations, and made-up numbers do more damage than a gap.

## Quality bar for any deliverable

- Complete: no placeholders, no "rest of the code", no "..." standing in for work.
- Correct against the success criteria from Step 2, not against what looks plausible.
- Robust at the edges: empty, one, huge, malformed, concurrent, offline.
- Simple: remove any component that no criterion requires. A dependency is justified only if it beats a short, understandable implementation.
- Readable: clear names, stated invariants, comments for why rather than what.

## Reference map

| File | Load when |
|---|---|
| references/weak-model-mode.md | The model has no private reasoning, or is small or unreliable |
| references/triz.md | Level DEEP, or the user asks for invention or a breakthrough |
| references/verification.md | Any STANDARD or DEEP task; before declaring done |
| references/domain-checklists.md | Code, security, architecture, math, research or decision tasks |
| references/ui-taste.md | Any interface, page, app screen or visual design |
| references/long-tasks.md | Output likely to exceed one response, or multi-file projects |
| references/examples.md | When unsure how a level should look in practice |
