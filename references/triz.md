# Systematic Invention Protocol (TRIZ & Inversion)

Load this reference when triage level is **DEEP**, or when the user explicitly asks for breakthrough thinking, patent-level problem solving, or radical innovation.

---

## 1. Bedrock Invariants & Assumption Stripping
- Identify the physical, mathematical, computational, or thermodynamic constants that cannot be altered (e.g., speed of light in fiber, Shannon capacity, memory bus bandwidth, state machine invariants).
- Strip every organizational, historical, or habitual assumption:
  - *"Why is it done this way traditionally?"*
  - *"What part of the current solution exists purely to solve a problem created by another part?"*
  - *"If this convention did not exist, what is the simplest theoretical mechanism to achieve the function?"*

---

## 2. Contradiction Analysis: Technical & Physical

### Technical Contradiction (Parameter vs Parameter)
A trade-off where improving one characteristic degrades another:
`If we improve [Parameter A], then [Parameter B] degrades.`
*(e.g., If we increase cache locality, memory usage blows up; if we enforce zero trust verification, request latency increases).*

### Physical Contradiction (Opposite States in One Parameter)
The same parameter must exist in two opposite states simultaneously:
`Parameter X must be [STATE 1] to satisfy Requirement A, but must be [NOT STATE 1] to satisfy Requirement B.`
*(e.g., The payload must be massive to resist impact, but must be weightless to reduce transit cost; the cache must be infinitely large for hits, but tiny to fit in L1).*

### The Separation Principles (Resolving Physical Contradictions)
1. **Separation in Time**: Parameter is State 1 at time $T_1$, and State 2 at time $T_2$ (e.g., batch processing at night, instant streaming during day).
2. **Separation in Space**: Parameter is State 1 in Zone A, and State 2 in Zone B (e.g., hot path in memory, cold archive on disk; localized immutability).
3. **Separation upon Condition**: Parameter switches state based on operational context (e.g., lock-free under low contention, fallback to mutex under heavy contention).
4. **Separation between Parts and Whole (System Hierarchy)**: The individual components possess State 1, but the macro-system exhibits State 2 (e.g., unreliable commodity nodes forming a Byzantine fault-tolerant cluster).

---

## 3. The Ideal Final Result (IFR) & Resource Inventory

### Ideal Final Result (IFR)
The ultimate benchmark of an invention is when the system performs the desired function **by itself**, using ambient resources with zero additional overhead.
- Formula: `IFR = (Benefits) / (Cost + Harm) -> Infinity`
- Guiding question: *"How can the system achieve the goal without adding new hardware, new microservices, or new runtime dependencies?"*

### Resource Inventory
Before adding new components, audit ambient resources already present:
- **Spatial Resources**: Unused memory offsets, spare bits in protocol headers, CPU cache lines, empty disk sectors.
- **Temporal Resources**: Idle CPU cycles between I/O waits, scheduled maintenance windows, network keepalive intervals.
- **Informational / Data Resources**: Log streams already being written, metadata, client-side state, hash collisions.
- **Systemic Energy / Waste**: Heat, discarded return values, error callbacks, ambient network traffic.

---

## 4. Key Inventive Principles (Altshuller)

| Principle | Meaning & Operational Application |
|---|---|
| **Segmentation** | Divide an object or system into independent, modular parts; transition to micro-elements or sharded state. |
| **Inversion (Taking the Opposite Action)** | Invert the operation: instead of pulling, push; bring the worker to the work instead of work to the worker; validate on write rather than read. |
| **Dynamicity / Adaptability** | Allow characteristics of the system to adapt dynamically to find optimal performance at each phase. |
| **Prior Counter-Action / Pre-Execution** | Absorb or nullify the cost before the critical path executes (e.g., compile-time lookup tables, kernel bypass, pre-computed hashes). |
| **Self-Service & Ambient Utilization** | Make the system serve itself by carrying out auxiliary functions and utilizing waste energy or existing headers. |
| **Equipotentiality** | Change the conditions of the work so the system does not need to overcome energy or computational barriers (e.g., zero-copy memory buffers, ring buffers without boundary reallocation). |
| **Feedback** | Introduce cross-boundary feedback loops to monitor and correct deviations dynamically. |
| **Intermediary** | Use a temporary carrier or intermediate process that merges or separates actions, and easily vanishes after use. |
| **Copying / Cheap Disposable** | Replace an expensive, fragile object with multiple cheap, ephemeral copies (e.g., stateless container replicas, ephemeral tokens). |
| **Parameter Changes** | Change physical or logical state (e.g., transition from contiguous array to bitmask, compress state density, change degree of flexibility). |
| **Another Dimension** | Move from linear 1D arrays to multidimensional tensors, spatial grids, or hierarchical trees to bypass linear scan limits. |

---

## 5. From Idea to Candidates (The Generation Procedure)
1. Formulate the Contradiction (Technical & Physical).
2. Attempt resolution via Separation Principles and the Resource Inventory.
3. Apply 2 to 3 Inventive Principles to generate concrete, divergent technical candidates.
4. If a contradiction cannot be completely removed, state the fundamental physical or theoretical limit, evaluate standard compromises, and choose the most robust candidate.

---

## 6. Honest Novelty Verification
Before declaring a solution an invention or breakthrough:
1. Search (if search tools exist) for prior art, academic literature, or existing industry patterns.
2. Tag the novelty accurately:
   - `KNOWN TECHNIQUE`: Standard practice under an established name (e.g., "Power-loss protection via capacitor-backed flush is a known enterprise storage pattern").
   - `NOVEL COMBINATION`: Applying established techniques in an unusual arrangement across domains.
   - `POSSIBLY NEW`: No prior art found after searching. State what was searched.
