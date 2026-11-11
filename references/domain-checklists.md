# Domain Quality Checklists

Use these checklists during Step 6 (Build and verify) to eliminate domain-specific defects before delivering.

---

## 1. Software Engineering & Systems Architecture
- [ ] **State & Boundaries**: Are off-by-one errors checked? Are empty arrays, empty strings, and null/None/undefined pointers handled safely?
- [ ] **Concurrency & Re-entrancy**: Are shared mutable resources protected against race conditions, deadlocks, and state corruption?
- [ ] **Complexity & Scale**: Does this contain hidden O(N^2) loops inside nested iterations? What happens at N = 10^6?
- [ ] **Resource Lifecycle**: Are file descriptors, sockets, database pools, and event listeners explicitly closed or disposed of?
- [ ] **Defensive Invariants**: Are function inputs validated at system boundaries before entering internal domain logic?

---

## 2. Performance Engineering
- [ ] **Allocation Hotspots**: Does the inner loop allocate unnecessary heap objects or strings?
- [ ] **Cache Locality**: Are data structures laid out sequentially in memory to optimize CPU cache lines?
- [ ] **Database & I/O Efficiency**: Are queries avoiding N+1 problems? Are indexes utilized effectively? Is pagination enforced?
- [ ] **Blocking on the Event Loop**: In async runtimes (Node.js/Python asyncio), are heavy CPU operations offloaded to worker threads?

---

## 3. Backend-as-a-Service (BaaS) & Cloud Backends (Firebase / Supabase / Postgres)
- [ ] **Security Rules / RLS**: Are Row-Level Security (RLS) policies or Firestore security rules active, tested, and denying unauthorized read/write access?
- [ ] **Atomic Mutations**: Are multi-document or multi-row balance updates wrapped in database transactions?
- [ ] **Quota & Cost Traps**: Are real-time listeners bounded by limits (`limit(50)`) rather than querying unbound collections?

---

## 4. Mobile & Touch Engineering
- [ ] **Touch Targets**: Are all interactive elements at least 44x44px (or 48x48dp)?
- [ ] **Intermittent Connectivity**: Does the app fail gracefully offline, cache local state, and retry idempotently?
- [ ] **Battery & Background**: Are location watchers, timers, and heavy polling loops paused when the app transitions to the background?

---

## 5. Security & Hardening
- [ ] **Injection Prevention**: Are all SQL, shell commands, HTML, and regex inputs parameterized or properly sanitized?
- [ ] **Authentication & Authorization**: Is there an IDOR (Insecure Direct Object Reference) risk? Are permissions verified at the data layer, not just the UI?
- [ ] **Secrets & Exposure**: Are private API keys, tokens, passwords, or PII excluded from client-side bundles and logs?
- [ ] **Fail-Secure Defaults**: When an operation encounters an error or timeout, does it fail safely (deny access) rather than fail open?

---

## 6. Mathematical & Algorithmic Rigor
- [ ] **Base Cases**: Does recursive logic reach a guaranteed, reachable base case?
- [ ] **Numerical Stability**: Are floating-point comparisons using appropriate epsilon tolerances? Are division-by-zero checks in place?
- [ ] **Overflow / Underflow**: Can integer calculations exceed bit-width limits (32-bit vs 64-bit limits)?
- [ ] **Invariants**: Is the algorithm invariant maintained across every iteration?

---

## 7. Research & Fact-Checking
- [ ] **Primary Source Verification**: Are API names, package versions, and standards cross-checked against official documentation rather than memory?
- [ ] **Historical / Factual Attribution**: Are quotes, dates, and paper titles verified via search tools?
- [ ] **No Hallucinated Citations**: If a citation or reference cannot be retrieved or verified, state plainly that it is unconfirmed.
