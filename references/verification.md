# Evidence-Based Verification Protocol

Self-critique without external grounding is fundamentally flawed; models frequently re-affirm their own hallucinations with total confidence. True verification requires execution, empirical testing, adversarial falsification, or independent re-derivation.

---

## 1. The Verification Hierarchy (Strongest to Weakest)

Always verify using the highest tier available in the current environment:

### Tier 1: Real Execution
- Run code directly via available execution tools (`run_command`, CLI, test runner).
- Execute SQL queries against a live or sandbox database.
- Check actual exit codes and stderr streams.
- If execution fails: capture exact error messages, adjust implementation, and re-execute until passing.

### Tier 2: Automated Tests
- Write concrete assertion tests derived directly from the success criteria.
- Test the full boundary matrix:
  - Empty collection (`[]`, `""`, `None`, `null`)
  - Single element (`[1]`, single-character string)
  - Boundary limits (`MAX_INT`, boundary dates, exact buffer capacity)
  - Malformed & unexpected inputs (type mismatch, invalid tokens)
  - Concurrency & contention (thread safety, race conditions, async deadlocks)

### Tier 3: External Grounding & Tool Lookup
- Look up API documentation, official specifications, RFCs, or package changelogs via search tools.
- Never guess function signatures, library flags, or protocol semantics when tools can retrieve them.

### Tier 4: Independent Recomputation
- Derive the mathematical result, state machine, or algorithm logic a second time using a completely different method (e.g., iterative vs recursive, geometric vs algebraic).

### Tier 5: Manual Symbolic Dry-Run (Fallback Only)
Use only when no execution tools or runtimes are available:
- Trace execution line-by-line using a mental state table (Variable | Value at Step T).
- Test with 3 explicit inputs: Empty, Typical, Extreme/Malformed.
- Disclose transparently: *"Traced by hand; not executed in an active runtime."*

---

## 2. Advanced Verification Procedures

### Chain-of-Verification (CoVe) Procedure
1. **Extract Core Claims**: Break down the deliverable into factual assertions, mathematical claims, or API dependencies.
2. **Draft Independent Factored Questions**: Formulate verification questions that check each claim *without* quoting the original answer (to avoid biasing the check).
   - *Example*: Instead of "Is my function using `Array.prototype.flat()` correctly?", ask: "What are the arguments, browser compatibility, and return type of `Array.prototype.flat()`?"
3. **Execute Verification Checks**: Answer each verification question using tools, documentation, or formal derivation.
4. **Synthesize & Correct**: Compare answers against the deliverable. If any claim fails, revise the deliverable before final submission.

### Mutation Testing (Falsifying the Tests)
- Before trusting an automated test suite, intentionally break one line in the implementation (e.g., change `<` to `<=`, or return a hardcoded value).
- Verify that the test suite **fails**. If the tests still pass when the code is broken, the tests are ineffective and must be rewritten.

### Differential Testing
- When implementing an optimized, complex algorithm (e.g., $O(N \log N)$ or bitwise trick), write a simple, brute-force, undeniably correct reference implementation ($O(N^2)$).
- Generate random inputs and compare outputs across both implementations. Any divergence isolates an edge-case bug.

### Pre-Mortem Analysis
- Assume the deliverable has been deployed to production and experienced a critical failure 30 days later.
- Identify the 3 most probable failure vectors (e.g., unhandled network partition, memory leak under continuous load, unexpected schema drift).
- Patch the cheap vulnerabilities immediately in the current design.

---

## 3. Definition of Done (DoD)

A deliverable is strictly **DONE** when:
1. All success criteria from Step 2 are met and demonstrably verified.
2. Edge cases (empty, single, extreme, malformed) are handled or explicitly defended with assertions.
3. No placeholders, stubs, or unfinished branches exist.
4. Each material claim is calibrated with a confidence label.
5. Verification evidence (execution log, test output, or dry-run matrix) is explicitly recorded.

---

## 4. Calibrated Confidence Standards

Attach one label to each material claim or deliverable component:
- `[VERIFIED]`: Directly executed via a tool command in THIS session, tested against a live runtime, or retrieved via a real-time search tool. (Never label pre-training memory, unexecuted formulas, or unverified claims as VERIFIED).
- `[LIKELY]`: Standard practice, pre-training factual recollection, or reasoned deduction that was not actively executed or searched in this session.
- `[UNVERIFIED]`: Working assumption, recollection, or theoretical extrapolation.

**Strict Prohibition**:
- A fact known from pre-training without live tool lookup must be labeled `[LIKELY]`, NEVER `[VERIFIED]`.
- Never use words like "guaranteed", "zero-bug", or "cannot fail" unless backed by formal mathematical proof or exhaustive automated verification.
