# Worked Examples by Triage Level

Study these examples to see how each triage level, confidence labeling, and working mode behaves in practice.

---

## Example 1: QUICK Level (Defect Fix)

### User Prompt
> "Here is our binary search function:
> ```python
> def binary_search(arr, target):
>     low, high = 0, len(arr) - 1
>     while low < high:
>         mid = (low + high) // 2
>         if arr[mid] == target:
>             return mid
>         elif arr[mid] < target:
>             low = mid + 1
>         else:
>             high = mid
>     return -1
> ```
> It fails to find the last element in some arrays. Fix it."

### Output
```markdown
Level: QUICK - Isolated defect in binary search loop condition and upper bound update.

### Deliverable
The loop condition terminated early when `low == high`, and the upper bound did not exclude `mid`.
Corrected implementation:

```python
def binary_search(arr, target):
    low, high = 0, len(arr) - 1
    while low <= high:
        mid = (low + high) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            low = mid + 1
        else:
            high = mid - 1
    return -1
```

### Verification
- [LIKELY] Traced by hand across boundary cases (not executed in active runtime):
  * Target at last element: `arr=[2, 5, 8]`, `target=8`:
    - Step 1: low=0, high=2, mid=1 (arr[1]=5 < 8) -> low becomes 2
    - Step 2: low=2, high=2, mid=2 (arr[2]=8 == 8) -> returns index 2 (Fixed: previously terminated without evaluating index 2).
  * Target absent: `arr=[2, 5, 8]`, `target=1`:
    - Step 1: low=0, high=2, mid=1 (5 > 1) -> high becomes 0
    - Step 2: low=0, high=0, mid=0 (2 > 1) -> high becomes -1
    - Step 3: low=0 > high=-1 -> loop exits and returns -1 correctly.
```

---

## Example 2: STANDARD Level with Worksheet (Weak Model Mode)

### User Prompt
> "We need to cache user session permissions in a Node.js API service. 50,000 active users, low latency required on every request, permissions update rarely but must invalidate within 2 seconds across all server instances."

### Output
```markdown
### Working Sheet
Level: STANDARD - System design choice between centralized, in-memory, and hybrid cache invalidation.
- Goal & Constraints: <1ms permission check latency, max 2.0s propagation of updates, 50k active sessions, Node.js cluster.
- Success Criteria: 1) Read latency < 1ms, 2) Propagation <= 2.0s, 3) Process RAM <= 150MB.
- Default Approach & Flaw: Direct Redis query on every HTTP request -> Network RTT (1.5 - 4ms) violates Criterion 1.
- Candidates:
  * A (Default): Centralized Redis query per request (Score: 10/15) - Fails Criterion 1.
  * B (Inverted): Local in-process Map with Redis Pub/Sub invalidation bus (Score: 14/15) - Inverts read-time network dependency.
  * C (Alternative): Stateless signed JWT token with 2s expiry (Score: 9/15) - Creates high token-refresh churn and header bloat.
- Decision: Candidate B selected. Scores highest on latency (local memory read <0.05ms) and invalidation speed (~30ms over Redis Pub/Sub).
- Pre-Mortem: If Redis connection drops, local caches will serve stale permissions indefinitely.
  -> Cheap fix: Add a hard local TTL ceiling of 60 seconds to force background refresh even if a Pub/Sub message is lost.
- Verification Plan: Measure local Map lookup speed with `performance.now()`; calculate upper-bound memory usage for 50k sessions.

---

### Core Architecture
We adopt **Candidate B**: a localized in-memory LRU cache inside each Node.js process, synchronized via Redis Pub/Sub for immediate invalidation upon permission mutation.

```typescript
// src/cache/sessionCache.ts
import { createClient, RedisClientType } from 'redis';

interface UserPermissions {
  roles: string[];
  permissions: Set<string>;
  cachedAt: number;
}

export class SessionPermissionCache {
  private localCache = new Map<string, UserPermissions>();
  private readonly MAX_TTL_MS = 60_000; // 60s fallback to heal partition drops

  constructor(
    private redisSub: RedisClientType,
    private redisData: RedisClientType
  ) {}

  async initialize(): Promise<void> {
    await this.redisSub.subscribe('permissions:invalidate', (userId: string) => {
      this.localCache.delete(userId);
    });
  }

  async getPermissions(userId: string): Promise<UserPermissions | null> {
    const cached = this.localCache.get(userId);
    if (cached && (Date.now() - cached.cachedAt) < this.MAX_TTL_MS) {
      return cached;
    }

    const raw = await this.redisData.get(`user:perm:${userId}`);
    if (!raw) return null;

    const parsed = JSON.parse(raw);
    const entry: UserPermissions = {
      roles: parsed.roles,
      permissions: new Set(parsed.permissions),
      cachedAt: Date.now()
    };
    this.localCache.set(userId, entry);
    return entry;
  }
}
```

### Evidence & Verification
- [LIKELY] Memory estimation: Assuming an average payload of 200 bytes per session record, 50,000 entries require approximately 10MB of heap (well below the 150MB budget).
- [VERIFIED] Local cache lookup benchmarks executed on V8: `Map.get()` executes in < 0.01ms (exceeds < 1ms requirement).
- [LIKELY] Redis Pub/Sub propagation across local cluster typically completes within 20-50ms (satisfies the 2.0s ceiling).
- Risks & Limits: In a network partition where Redis is completely unreachable, cache falls back to 60s TTL; true instant revocation requires network connectivity.
```

---

## Example 3: DEEP Level (Technical Contradiction & Honest Novelty)

### User Prompt
> "We are designing telemetry logging on an embedded Linux device with slow, wear-prone SPI NOR flash. Direct synchronous writes wear out sectors and stall the sensing thread. Storing in volatile RAM risks losing vital crash logs when power cuts unexpectedly."

### Output
```markdown
Level: DEEP - Technical and physical contradiction between flash write longevity, real-time sensing latency, and unexpected power-cut data safety.

### 1. Contradiction & Ideal Final Result (TRIZ)
- **Technical Contradiction**: If we flush synchronously to Flash, data is safe from power cuts, but flash wear accelerates and sensing threads block on erase cycles.
- **Physical Contradiction**: The write buffer must be in volatile fast storage (RAM) for speed and longevity, but must simultaneously be non-volatile (persistent) during power loss.
- **Ideal Final Result (IFR)**: Data resides in fast volatile memory during normal operation, and transfers to persistent storage only when power is actually failing, utilizing existing reserve energy.

### 2. Resolution via Separation Principles & Resource Inventory
- **Separation in Time**: Operate purely in RAM during normal runtime (0 flash writes); commit to Flash only during the transient shutdown phase.
- **Resource Inventory**: Power supply hold-up capacitors (rail capacitance) provide a predictable energy window (e.g., 15 to 30ms) between a "voltage dropping" interrupt and brown-out reset.

### 3. Novelty Assessment
- **KNOWN TECHNIQUE**: Power-Loss Protection (PLP) via early power-fail interrupt (PFI) and capacitor hold-up flush. This is an established pattern in industrial embedded systems and enterprise solid-state drives.
- *Alternatives considered*: Ferroelectric RAM (FRAM) or Magnetoresistive RAM (MRAM) offer byte-addressable persistence without wear limits, but require dedicated hardware redesign and higher BOM cost.

### 4. Architecture & Implementation Plan
1. Configure MCU Hardware Comparator / ADC to monitor input voltage rail ($V_{in}$) against a threshold ($V_{thresh} = 4.3V$ on a $5.0V$ supply).
2. Trigger non-maskable Power-Fail Interrupt (PFI) when voltage crosses below $V_{thresh}$.
3. In the ISR/high-priority handler:
   - Shut down peripheral clocks and radios to minimize current draw ($I_{drain}$).
   - Blast the condensed memory ring buffer (using raw SPI DMA page write) into a reserved flash swap sector.

### 5. Evidence & Verification Calculations
- [VERIFIED] Energy hold-up formula: $C = \frac{2 \times P_{flush} \times t_{flush}}{V_{start}^2 - V_{min}^2}$.
  * If $t_{flush} = 10\text{ms}$, $P = 150\text{mW}$, $V_{start} = 4.3\text{V}$, $V_{min} = 3.0\text{V}$:
    Required capacitance $C \approx \frac{2 \times 0.0015}{18.49 - 9.0} \approx 316\,\mu\text{F}$.
- [UNVERIFIED] Requires hardware oscilloscope validation to measure exact capacitor discharge curve under maximum system load.
```
