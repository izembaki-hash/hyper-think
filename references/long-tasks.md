# Long Tasks & Multi-Turn Projects Protocol

Load this file when a task involves multi-file architectures, complex refactorings, or deliverables that exceed a single conversation turn.

---

## 1. The Context Boundary Problem
Language models operating under high token load suffer from degradation:
- Truncating code halfway through (`// rest of implementation here...`).
- Silently forgetting invariants established in earlier steps.
- Hallucinating imports or signatures of files they haven't re-read.

---

## 2. Upfront Shared Contract (Signatures & Schemas First)
Before writing any module implementations:
1. Define the **Shared Contract** in an interface or types file:
   - Exported function signatures with argument types and return types.
   - Core data structures, schemas, and error types.
   - Storage/API schemas and boundary wire protocols.
2. Freeze this contract. All subsequent modules must implement against this exact specification.

---

## 3. Chunking & State Persistence

### The Project State File (`.hyper-state.md` or `STATE.md`)
Maintain a lightweight progress log in the project root:
```markdown
# Project State
- Phase: [e.g., 2 of 4 - Core Engine Implementation]
- Contract Status: [FROZEN]
- Completed Modules: [src/types.ts (VERIFIED), src/parser.ts (VERIFIED)]
- Current Module: [src/engine.ts (IN PROGRESS)]
- Next Module: [src/cli.ts]
- Known Issues / Technical Debt: [None]
```

### Execution Rules
1. **One Cohesive Unit Per Step**: Never attempt to generate 5 large files in one prompt turn. Implement one complete file or coherent module at a time.
2. **Re-Read Before Editing**: Before modifying any existing file in turn $N+1$, always re-read the file or line range first using file reading tools to anchor reality.
3. **Stop at Clean Boundaries**: If context window limits approach or response length is heavy, stop cleanly after completing a standalone module or test. Never rush or compress code to fit into one turn.
4. **Final Assembly & Integration Test**: Once all modules are written, run the end-to-end integration test suite, verify compilation/build across all linked files, and confirm zero circular dependencies.
