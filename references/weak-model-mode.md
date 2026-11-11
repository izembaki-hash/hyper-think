# Weak Model Mode (Worksheet Protocol)

Use this reference when the running model lacks an internal reasoning scratchpad, is a smaller/faster model (such as 7B/8B class), or when previous responses show signs of jumping to conclusions, skipping edge cases, or hallucinating.

Without an internal scratchpad, a model that does not write its intermediate thinking skips the protocol steps. The worksheet forces deliberate, visible reasoning before generating the deliverable.

---

## 1. The Worksheet Format (Compact 4-Line Mode)

Place this worksheet at the very beginning of the response. Keep it concise, bulleted, and strictly factual.

```markdown
### Working Sheet
- Goal: [Target in 1 phrase] | Hard constraints: [time, language, memory]
- Primary Inversion: [What standard assumption we reject]
- Decision & Pre-Mortem: [Chosen approach] | If this fails: [primary risk] -> [instant mitigation]
- Verification: [Exact command, test script, or hand-trace inputs]
```

For STANDARD design tasks with multiple competing architectures, models may optionally expand the `Decision` bullet into 3 candidate choices (`A: default, B: inverted, C: alternative`), but must never exceed 8 lines total to prevent planning paralysis.

---

## 2. Operating Rules for Non-Reasoning Models

1. **Write the Worksheet First**: Never write the deliverable before completing the worksheet. If the pre-mortem exposes a fatal flaw, change the decision in the worksheet before writing code.
2. **Ambiguity Rule (Ask Exactly One Question)**: If a missing piece of information would fundamentally change the design, and the user is reachable, stop and ask exactly one clear question. If the user is unreachable, state your working assumption in one line in the worksheet and proceed.
3. **Inversion Forcing**: At least one candidate must explicitly invert an assumption of the default approach (e.g., precompute on write instead of query on read; client-driven instead of server-driven; push instead of poll).
4. **Weighted Scoring**: Rate each candidate against the success criteria (1 to 5 per criterion). The candidate with the highest sum wins; if a lower-scoring candidate is chosen, state the override reason.
5. **Admit Limits ("Say I Don't Know")**: If a reliable solution cannot be reached after two independent attempts, state clearly what was attempted, where the block occurred, and what remains unverified. Never fabricate APIs, citations, or benchmark figures.
6. **No Placeholder Code (Precise Ban)**:
   - **Banned**: Any placeholder standing in for missing or unfinished logic (e.g., `// TODO`, `/* implement later */`, `# rest of code here`, or comments skipping repetitive branches).
   - **Allowed**: Valid language syntax where required by design (e.g., Python `pass` in abstract base classes, `...` for type annotations / Ellipsis, JavaScript spread `...args`).
