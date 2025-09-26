
# Non-negotiables (daily, ~75–100 min)

* **25 min DSA reps** (arrays/hash map/two-pointers/sliding window/binary search).
* **25 min .NET topic** (one/night → add to a single cheat sheet).
* **10–15 min assignment micro-drill** (60-sec pitch + 1 curveball).
* **10–20 min behavioral** (1 STAR story/day).
* **5 min written debrief** (wins, stuck points, fix for tomorrow).

**Progress bar (check nightly)**

* DSA: ≥3 focused problems, ≥80% solved within 20 min each.
* .NET: +1 “90-sec explanation” added to your cheat sheet.
* Assignment: deliver 60-sec overview **without notes** + answer 1 curveball.
* Behavioral: +1 STAR with numbers (latency, users, €, %).

**If off-track**

* Drop trees/graphs/DP. Double down on arrays/hash/BS/window.
* Replace long videos with micro-exercises + flash cards.
* System design: do 10-min skeleton → 10-min single bottleneck deep-dive.

---

# Dated itinerary (Europe/Amsterdam)

## Fri Sep 26 (Tonight, 18:30–22:15) — Kickstart (capture-only)

**18:30–19:10 — Assignment capture (40m)**
Bullet the 60-sec pitch, architecture, top 5 trade-offs, 1-day/1-week roadmap.

**19:10–20:10 — .NET refresh #1 (60m)**
Async/await pitfalls, ConfigureAwait, DI lifetimes, HttpClient lifetime; write 10-bullet cheat sheet.

**20:10–20:30 — Break**

**20:30–21:10 — DSA trio (40m)**
Contains duplicate, Two-sum, Kadane → 2-line pattern notes each.

**21:10–21:35 — Behavioral (25m)**
STAR #1: production incident you led (with metrics).

**21:35–22:15 — Assignment micro-drill (40m)**
60-sec pitch ×2 + 1 curveball (rate-limit vs retry).

---

## Sat Sep 27 (10:00–18:00) — ASP.NET Core + EF Core + sliding window

**10:00–12:00** ASP.NET Core internals (pipeline, filters, model validation, cancellation).
*Micro-exercise (15m):* minimal API + Typed HttpClient + Polly retry + logging scope.

**12:00–12:30** DSA: sliding window (longest substring; fixed-K max sum).
**12:30–13:30** Lunch
**13:30–15:00** EF Core perf (AsNoTracking, projection vs Include, N+1, compiled queries) → 10 anti-pattern→fix cards.
**15:20–16:20** Patterns: Decorator vs Strategy vs Factory (90-sec contrasts + tiny C# sketches).
**16:20–17:00** Assignment micro-drill (40m): streaming chunks, cancellation, DNS/handler.
**17:00–18:00** Behavioral: STAR #2 (0→1 feature), #3 (conflict).

---

## Sun Sep 28 (10:00–17:30) — Testing, Observability, SD lite

**10:00–11:30** Testing: xUnit, Moq/fakes, WebApplicationFactory; write 2 tests for parser/stream logic.
**11:30–12:00** DSA: binary search patterns (left/right bias, first/last).
**12:00–13:00** Lunch
**13:00–15:00** System design (Azure): API/Function → Queue (Service Bus) → Worker → Redis → SQL; idempotency, poison queue, backpressure.
**15:20–16:00** Observability: logging scopes, correlation IDs, p95/p99, health/readiness, OpenTelemetry basics; add “what I’d add” bullets.
**16:00–17:30** Behavioral: STAR #4 (mentoring), #5 (risk raised early), #6 (reliability/cost).

---

## Mon Sep 29 (18:30–21:45) — Recruiter screen prep + .NET

**18:30–19:00** Recruiter pitch (90-sec intro, 3 wins with metrics, role prefs, notice/comp stance).
**19:00–19:40** DSA: array/hash quick set (group anagrams, majority element, product except self).
**19:40–20:40** .NET #2: HttpClient + Polly (timeouts, transient vs fatal); 5 Q&A cards.
**21:00–21:45** Assignment micro-drill (45m): tests, perf levers, 10× traffic.

---

## Tue Sep 30 (18:30–22:00) — EF deepening + sliding-window hard

**18:30–19:30** EF Core #2: batching, SaveChanges vs SaveChangesAsync, optimistic concurrency, migrations, isolation levels.
**19:30–20:10** DSA: min window substring *or* longest repeating replacement; write your template with comments.
**20:30–21:00** Patterns in DI: Strategy in real code (parsers/validators).
**21:00–22:00** Behavioral: STAR #7 (influencing), #8 (disagree & commit). Micro-drill (10m).

---

## Wed Oct 1 (18:30–22:10) — Mock + pipeline + **Full assignment rehearsal #1**

**18:30–19:30** Mock coding (1 medium array/hash/BS). After: write 5-line pattern summary.
**19:30–20:10** ASP.NET lifecycle: filters vs middleware; model validation traps; exception handling.
**20:30–21:30 — Full assignment rehearsal #1 (60m)**
8–10 min pitch + diagram → interviewer-style grill (rate limit vs retry, streaming edge cases, DNS/handler, timeouts layering).
**21:30–22:10** Convert any rambling answers into 2–3 bullet sound bites.

---

## Thu Oct 2 (18:30–21:30) — Polish + **Full assignment rehearsal #2**

**18:30–19:10** DSA speed run: two repeats you’ve already solved (sub-10 min each).
**19:10–19:50** .NET perf/memory: value types vs records, Span<T>/Memory<T>, pooling, string handling; +5 bullets to cheat sheet.
**20:10–20:50 — Full assignment rehearsal #2 (40m)**
Record yourself; focus only on weak spots from Wed.
**20:50–21:30** Behavioral tidy: compress each STAR to 30-sec and 90-sec versions.

---

## Fri Oct 3 — **C# Interview Day**

**Morning (30–40m)**

* 60-sec assignment pitch + 3 diagrams.
* Decorator/Strategy/Factory 90-sec contrasts.
* 2 DSA warm-ups on paper.
* Breathing/voice warm-up; environment check.

**During**

* Coding: restate → tiny examples → pick pattern → code clean → mini tests → complexity/edges.
* Assignment: Goal → constraints → architecture → trade-offs → testing → metrics → next steps. Offer “deep-dive: streaming or retries?”
* Patterns: always anchor to C# you’d actually write.

**Post (10m)** Capture gaps for Sat.

---

## Sat Oct 4 (10:00–14:30) — Patch gaps + big-tech ramp

* Fill any weak answers from Fri (90m).
* SD rehearsal (10× ingestion/compute; partitions/idempotency) (60m).
* DSA speed set (45m): binary search & two-pointers.
* Behavioral polish: leadership/mentorship (30m).
* Write recruiter screen script (≤20 lines) & practice twice (25m).

---

## Sun Oct 5 (10:00–13:00) — Recruiter screen final

* Mock coding (45–60m).
* Azure specifics (45m): pick 2 pairs to contrast (Service Bus vs Storage Queues; Functions vs Workers; App Service vs Container Apps; Key Vault rotation).
* Final recruiter Qs (30m): role fit, teams, locations, timelines, comp philosophy.
* Early night.

---

## Mon Oct 6 — **Big Tech Recruiter Screen (Morning)**

**Opening (2–3 min)**
“I’m a senior-leaning .NET/React engineer (5 years). Recently built a streaming compute CLI over an external API with DDD; resilient HTTP, rate-limit shaping, and tests. In prod I’ve owned App Service/Functions with SQL, Redis, Key Vault, AI Search for a few thousand live users. Strongest in backend + pragmatic SD (cache + DB + queue + workers + observability). Targeting senior IC roles in Amsterdam/remote; notice period __. Happy with coding/system-design rounds.”

Have crisp answers ready: team types, relocation, timeline, comp expectations (range + flexibility).

---

# .NET topic rotation (one/night)

1. Async/await pitfalls; ConfigureAwait; cancellation propagation.
2. HttpClient lifetime; SocketsHttpHandler; DNS; Polly retries/backoff/timeouts.
3. DI lifetimes; options pattern; config sources; health/readiness.
4. ASP.NET pipeline; filters vs middleware; model validation.
5. EF Core perf; concurrency; migrations.
6. Testing: xUnit, Moq/fakes, WebApplicationFactory; contract tests; golden files.
7. Observability: logging scopes, correlation, p95/p99, tracing.
8. Patterns: Decorator (cross-cutting), Strategy (swap algorithm), Factory (creation), when **not** to use them.

---

# DSA focus set (only these)

* **Arrays/Hash map:** duplicates, two-sum variants, anagram, majority element, product except self.
* **Two-pointers:** pair sum on sorted, 3-sum pattern, move zeroes.
* **Sliding window:** longest substring w/o repeat, fixed-K max sum, min window substring (know invariant).
* **Binary search:** left/right bias, first/last occurrence, rotated array min.

---

# Checkpoints

**By Thu Oct 2 (night):**

* DSA: 18–25 problems total across 6–8 patterns; can state each invariant in 1–2 sentences.
* .NET: 8 mini cheat sheets; 1 tiny HttpClient+retry+logging demo.
* Behavioral: 6–8 STARs with metrics.
* Assignment: full pitch + two strong deep-dives (streaming & retries).

---

# Quick reference (pre-interview)

* **Decorator:** wrap interface, add cross-cutting behavior; C# sketch in your head: `IService`, `Service`, `LoggingDecorator(IService inner)`.
* **HttpClient gotcha:** socket exhaustion, handler reuse, DNS refresh, why `IHttpClientFactory`.
* **EF N+1:** projection over `Include`, `AsNoTracking`, compiled queries.
* **Rate-limit vs retry:** limiter shapes pace; retry for transient 408/429/5xx honoring `Retry-After`.
* **10× scale levers:** bounded parallelism under global limiter, gzip, projection, queue+worker, idempotency.

