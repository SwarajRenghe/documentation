# Non-negotiables (daily, ~75–100 min)

* 25 min DSA reps (arrays/hash map/two-pointers/sliding window/binary search).
* 25 min .NET fundamentals (one topic/day → add to a single cheat sheet).
* 15 min “assignment defense” drill (aloud).
* 10–20 min behavioral (1 STAR story/day; aim for 7–8 total by Thu Oct 2).
* 5 min written debrief (what improved, what to fix tomorrow).

**Progress bar (check nightly):**

* DSA: ≥3 focused problems/day, ≥80% solved within 20 min each.
* .NET: 1 new “90-second explanation” added to your cheat sheet/day.
* Assignment: can deliver a 60-sec overview + answer 3 tough follow-ups without notes.
* Behavioral: +1 STAR story/day with numbers (latency, throughput, users, € saved).

If you slip: skip trees/graphs/DP; double down on arrays/hash/BS/window. Replace videos with micro-exercises and flash cards.

---

# Dated plan (Europe/Amsterdam)

## Fri Sep 26 (Tonight, 18:30–22:30) — Assignment reboot + .NET “gotchas”

**18:30–19:50 — Assignment Defense One-Pager (80m)**
Create `ASSIGNMENT-DEFENSE.md` with:

* Problem & constraints (1–2 bullets each).
* Architecture (CLI entry → domain services → HTTP/stream → parse/compute → output).
* Key decisions (DDD modules, HttpClient using SocketsHttpHandler, streaming approach, parsing approach, error handling, cancellation).
* Trade-offs (why CLI vs worker, why DDD here, why sync/async choices, memory vs throughput).
* Testing strategy (unit boundaries; contract test for API; golden file for parser).
* Observability (structured logs, metrics you *would* add: p95 latency, error rate, throughput).
* “If I had 1 day / 1 week” roadmap (perf, retries/backoff, backpressure, progress UI, telemetry).

**19:50–20:10 — Break**

**20:10–21:10 — .NET Core refresh #1 (60m)**
Async/await pitfalls, ConfigureAwait, DI lifetimes (Transient/Scoped/Singleton), HttpClient lifetime, retry/timeouts. Produce a 10-bullet cheat sheet.

**21:10–21:50 — DSA set (40m)**

* Contains duplicate (set)
* Two-sum (hash)
* Max subarray/Kadane
  → Write 1–2 line pattern note after each.

**21:50–22:10 — Behavioral (20m)**
Draft STAR #1 (biggest incident you led).
**22:10–22:30 — Assignment 60-sec pitch (20m)** (say it out loud twice)

---

## Sat Sep 27 (10:00–18:00) — ASP.NET Core + EF Core + sliding window

**10:00–12:00 — ASP.NET Core internals (120m)**
Middleware order, model binding/validation, filters, minimal APIs vs controllers, cancellation tokens flowing to I/O.
Micro-exercise (15 min): minimal API + Typed HttpClient + Polly transient retry + logging scope.

**12:00–12:30 — DSA (30m)**
Sliding window template:

* Longest substring without repeat
* Fixed-size max sum (size K)
  Write the *invariant* you maintain and when you expand/shrink.

**12:30–13:30 — Lunch**

**13:30–15:00 — EF Core performance (90m)**
AsNoTracking, projection vs Include, N+1, compiled queries, concurrency tokens, indexes.
Deliverable: 10 “anti-pattern → fix” flash cards.

**15:00–15:20 — Break**

**15:20–16:20 — Patterns (60m)**
Decorator vs Strategy vs Factory. Prepare 90-sec explanations + tiny C# sketches.

**16:20–17:20 — Assignment Q&A (60m)**
Answer these aloud and jot bullets:

* Why CLI vs worker/service?
* Why DDD for a small tool? What bounded contexts?
* Streaming: how do you handle partial chunks, UTF-8 splits, cancellation, backpressure?
* HttpClient lifetime & DNS changes; when to use SocketsHttpHandler; how to avoid socket exhaustion.
* How would you expose progress, retries, and idempotency?
* Parsing correctness tests (golden files) & fuzz cases.
* Metrics you’d add and alerts you’d wire.

**17:20–18:00 — Behavioral (40m)**
STAR #2 (0→1 feature), #3 (conflict you resolved)

---

## Sun Sep 28 (10:00–17:30) — Testing, Observability, System Design lite

**10:00–11:30 — Testing (90m)**
xUnit, Moq/fakes, WebApplicationFactory for an API, contract tests for external API.
Write 2 tests around the trickiest parser/stream logic.

**11:30–12:00 — DSA (30m)**
Binary search patterns (left-bias/right-bias; first/last occurrence).

**12:00–13:00 — Lunch**

**13:00–15:00 — System Design (120m)**
Design “Compute-heavy ingestion pipeline” in Azure: App Service/Function → Queue (Service Bus) → Worker → Redis cache → SQL.
Cover: idempotency keys, poison queue handling, cache invalidation, partitioning, backpressure.
Deliverable: 1 page diagram + bullet trade-offs.

**15:00–15:20 — Break**

**15:20–16:00 — Observability (40m)**
Logging scopes, correlation IDs, minimal metrics, health/readiness, OpenTelemetry basics.
Add “what I would add to assignment” section.

**16:00–17:30 — Behavioral (90m)**
STAR #4 (mentoring), #5 (raise a risk early), #6 (improving reliability/cost).
Finish with 60-sec assignment pitch once.

---

## Mon Sep 29 (Evening, 18:30–21:45) — Recruiter screen prep + .NET

**18:30–19:00 — Recruiter pitch (30m)**
Write 90-sec “who I am,” 3 wins with metrics, role preferences, notice period, comp/level expectations.

**19:00–19:40 — DSA (40m)**
Array/hash quick trio (Two-sum variants, group anagrams, majority element).

**19:40–20:40 — .NET Core #2 (60m)**
HttpClient+Polly (policies), transient vs fatal errors, timeouts, cancellation propagation.
Add 5 Q&A cards (e.g., “Where do timeouts belong: HttpClient vs Polly vs server?”)

**20:40–21:00 — Break**

**21:00–21:45 — Assignment drill (45m)**
10 min pitch + 5 min on streaming details + 5 min on tests + 5 min on perf + 5 min on “what if traffic 10×” + 5 min on “what would you change”.

---

## Tue Sep 30 (18:30–22:00) — EF Core deepening + sliding-window hard

**18:30–19:30 — EF Core #2 (60m)**
Batching, SaveChanges vs SaveChangesAsync, optimistic concurrency, migration strategies, long-running transactions, isolation levels.

**19:30–20:10 — DSA (40m)**
Sliding window “hard”: min window substring *or* longest repeating replacement. Write your generic template with comments.

**20:10–20:30 — Break**

**20:30–21:00 — Patterns (30m)**
Strategy in real code (e.g., parsers/pricing/validators) and how DI composes it.

**21:00–22:00 — Behavioral (60m)**
STAR #7 (influencing without authority) + STAR #8 (disagree & commit). Dry-run two out loud.

---

## Wed Oct 1 (18:30–22:00) — Mock night + ASP.NET pipeline

**18:30–19:30 — Mock coding (60m)**
1 medium (array/hash/BS) timed. After: write “pattern summary” in 5 lines.

**19:30–20:10 — ASP.NET pipeline (40m)**
Request lifecycle, filters, model validation traps, exception handling middleware vs filters.

**20:10–20:30 — Break**

**20:30–21:10 — System design (40m)**
10-minute whiteboard of your Azure reference (API→Queue→Worker→DB/Cache), then 20-minute Q&A with yourself.

**21:10–22:00 — Assignment curveballs (50m)**
Add 5 new questions you haven’t covered; answer succinctly.

---

## Thu Oct 2 (18:30–21:30) — Polish + rest-smart

**18:30–19:10 — DSA (40m)**
Two repeats you’ve seen (speed run). Aim sub-10 min each.

**19:10–19:50 — .NET perf/memory (40m)**
Value types vs classes/records, Span<T>/Memory<T> basics, pooling, string handling, logging allocations. Add 5 bullets to cheat sheet.

**19:50–20:10 — Break**

**20:10–20:50 — Patterns & Decorator (40m)**
Decorator elevator pitch + tiny C#: `IService`, `RealService`, `LoggingDecorator(IService inner)`.

**20:50–21:30 — Behavioral tidy (40m)**
Condense each STAR to a 30-sec and 90-sec version. Prioritize those relevant to the assignment.

---

## Fri Oct 3 — **C# Interview Day**

**Morning (30–40m):**

* 60-sec assignment overview + 3 diagrams.
* Decorator/Strategy/Factory 90-sec contrasts (with real-world examples).
* Two DSA warm-ups on paper.
* Breathing/voice warm-up; environment check.

**During interview — tactics**

* For coding: restate → small examples → choose pattern → code clean → tiny tests → complexity → edges.
* For assignment: “Goal → constraints → architecture → trade-offs → testing → metrics → what’s next.” Offer depth (“happy to dive into streaming or retries”).
* For patterns: always anchor to C# code you’d actually write (+ when *not* to use it).

**Post (10 min):** jot gaps for Sat.

---

## Sat Oct 4 (10:00–14:30) — Fill gaps + big-tech ramp

* Patch any weak answers from Fri (90m).
* System design rehearsal: ingestion+compute at 10× scale; call out bottlenecks/partitions/idempotency (60m).
* DSA speed set (45m) — binary search & two-pointers.
* Behavioral: leadership/mentorship story polish (30m).
* Write your recruiter screen script (20 lines max) & practice twice (25m).

---

## Sun Oct 5 (10:00–13:00) — Recruiter screen final

* Mock coding (45–60m).
* Azure specifics: pick 2 to recap (Service Bus vs Storage Queues, Functions vs Workers, App Service vs Container Apps, Key Vault rotation) (45m).
* Final recruiter Qs: role fit, team prefs, locations, timelines, comp philosophy (30m).
* Early night.

---

## Mon Oct 6 — **Big Tech Recruiter Screen (Morning)**

**Your opening (2–3 min):**
“I’m a senior-leaning .NET/React engineer (5 years). Recently built a streaming compute CLI over an external API using DDD; clean architecture, resilient HTTP, and solid tests. In production, I’ve owned services on App Service/Functions with SQL, Redis, Key Vault, AI Search for a few thousand live users. I’m strongest on backend and pragmatic system design (cache + DB + queue + workers + observability). I’m targeting senior IC roles in Amsterdam/remote; notice period __. Happy to do coding and system design rounds.”

Have crisp answers for: team types you like, relocation, timeline, compensation expectations (range + flexibility).

---

## Cheat-sheet topics to master (you’ll rotate these nightly)

1. Async/await pitfalls; ConfigureAwait; deadlocks in sync contexts; cancellation propagation.
2. HttpClient lifetime; SocketsHttpHandler; DNS; retries/timeouts/backoff with Polly.
3. DI lifetimes; options pattern; config sources; health/readiness.
4. ASP.NET pipeline; filters vs middleware; model validation traps.
5. EF Core perf: AsNoTracking, projection, compiled queries, concurrency, migrations.
6. Testing: xUnit, Moq/fakes, WebApplicationFactory; contract tests; golden files for parsers.
7. Observability: logging scopes, correlation IDs, p95/p99, tracing.
8. Patterns: Decorator (cross-cutting), Strategy (swap algorithm), Factory (creation), when *not* to use them.

---

## Quick-answer bank (practice aloud)

* **Decorator pattern:** “Wrap an interface to add behavior without modifying the concrete. In C#: `IService` with `LoggingDecorator(IService inner)` calls inner then logs. Use for cross-cutting like logging/caching; prefer over inheritance; unlike Strategy, it doesn’t swap the core algorithm.”
* **HttpClient gotcha:** explain socket exhaustion, handler reuse, DNS, and why `IHttpClientFactory` exists.
* **EF Core N+1:** show projection with `.Select(...)` instead of `Include` when you don’t need full graphs.
* **Streaming safety:** handling chunk boundaries, partial UTF-8, cancellation tokens, and backpressure via bounded channels/async enumerables.
* **Testing parser:** golden files + fuzz inputs; assert on structure, not whitespace.
* **Design at 10× load:** add queue+worker, cache hot reads, idempotency keys, circuit breakers, and p95 SLOs.

---

## DSA pattern list (only these)

* Arrays/Hash map: duplicate, two-sum variants, anagram, majority element, product except self.
* Two-pointers: pair sum on sorted, 3-sum pattern, move zeroes.
* Sliding window: longest substring w/o repeat, fixed window max sum, min window substring (know the invariant even if not perfect).
* Binary search: left/right bias, first/last, rotated sorted array.
  Keep tiny “templates” with comments you can recall.

---

## What to do if off-track (decision tree)

* Struggling on DSA time? → Drop min window, keep longest substring + fixed-K + BS left-bias.
* EF questions feel wide? → Anchor to 10 anti-patterns/fixes list.
* System design too big? → Open with requirements/SLA → API → data model → high-level → one bottleneck deep dive (cache invalidation or idempotency) → observability.
* Assignment fuzziness? → Re-read your README and your `ASSIGNMENT-DEFENSE.md`; re-run your 60-sec pitch.
