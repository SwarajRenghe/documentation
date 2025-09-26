Perfect—this is exactly what I needed. I’ve turned your README into a sharp, interview-ready **ASSIGNMENT-DEFENSE.md** plus a 60-second pitch, diagram notes, and a bank of likely follow-ups with crisp answers. Drop this next to your repo and rehearse it once a day.

---

# ASSIGNMENT-DEFENSE.md

## 60-second overview (say this verbatim)

“I built a small but well-structured **.NET CLI** that calls Funda’s API, **streams and aggregates listings**, and outputs the **top makelaars**, optionally filtered (e.g., *tuin*). I used **DDD** to keep the **Domain** stable, with **edge-mapping** at the boundaries so external models adapt to my domain language. The **ApiClient** uses **`HttpClientFactory` with a reusable `SocketsHttpHandler`**, a **sliding-window rate limiter**, **retries with exponential backoff + jitter**, and **pagination**. Everything is **cancellation-aware**; progress is reported via `IProgress<T>`. The **CLI** composes it all, with configuration layering (defaults → `appsettings.json` → flags). I wrote unit + integration tests, and left a roadmap for production hardening: parallelism within limits, compression, metrics, and policy-based timeouts. Happy to dive into streaming details, EF/LINQ shaping if we made it a service, or how I’d scale this to 10×.”

---

## Architecture at a glance

**Modules**

* **Funda.Domain** — canonical models + business rules; consumers must speak domain terms (edge-mapping).
* **Funda.ApiClient** — URL construction, paging, rate limiting, retries, streaming parse; swappable transport.
* **Funda.Services** — use-cases (e.g., compute top makelaars, apply filters), progress reporting contract.
* **Funda.CLI** — composition root: DI, configuration (defaults/JSON/flags), rendering.

**Flow (ASCII)**

```
CLI (args + appsettings)
    ↓ DI wires options, HttpClientFactory, Services
ApiClient (typed client)
    ├─ URL builder → paged GETs
    ├─ Sliding-window limiter (N req / 60s)
    ├─ Retry policy (exp backoff + jitter, honors Retry-After)
    └─ Async stream → parser
        ↓ (Domain edge-mapping)
Services (TopMakelaars)
    ├─ Aggregates counts
    └─ Reports progress via IProgress<PaginationProgress>
CLI Presenter
    └─ Renders table / summary
```

**Why DDD + edge mapping here?**

* Keeps the **Domain stable** as the language of the problem (Makelaars/Properties) and forces adapters (API/file/cache) to align.
* Enables swapping **ApiClient** for a **file cache** or **queue worker** without leaking API shape into business logic.
* Small assignment, but this choice pays off immediately in **testability** and **maintainability**.

---

## Key decisions & trade-offs

* **CLI vs service**: chose CLI for fast iteration + deterministic runs; left seams so the **entry layer** can later be a worker/web app.
* **Sliding-window limiter**: caps **total requests over any 60s window** (e.g., 100/min ≈ one every ~625ms on average). Better than per-second buckets under bursty patterns.
* **Retries**: **exponential backoff + jitter**; treats 408/429/5xx as transient and respects `Retry-After`.
  *Trade-off:* slightly longer tail latencies under load; acceptable for a batch CLI.
* **`HttpClient` lifetime**: `IHttpClientFactory` + **reused handler** to avoid socket exhaustion; **DNS** updates handled by handler settings.
  *Trade-off:* more plumbing, but production-grade by default.
* **Cancellation everywhere**: all long-running ops accept `CancellationToken`.
  *Trade-off:* slightly noisier method signatures, big win for robustness.
* **Configuration layering**: baked-in defaults → `appsettings.json` → **CLI flags override**.
  *Trade-off:* two sources to validate, but gives great ergonomics.
* **Progress reporting**: `IProgress<PaginationProgress>`; non-blocking and UI-agnostic.
* **Singletons in CLI**: OK for single-run process; would switch lifetimes in a hosted service.

---

## Testing approach

* **Unit**: mappers, URL builder, counting/aggregation logic, validators.
* **Integration**: DI-wired tests using real implementations, validating paging/aggregation on sample fixtures.
* **Parser correctness**: “golden files” for sample API responses to lock format; add fuzz inputs for resilience.

---

## API feedback (two concrete items)

1. **Quota returns 401**: returning **`429 Too Many Requests`** would align with standard retry semantics and libraries.
2. **Pagination start index**: both 0 and 1 accepted; prefer one canonical origin for client simplicity.

---

## What I’d add for production

1. **Parallelism under a global limiter**: bounded concurrency (e.g., per-host semaphore) while respecting the sliding window to improve throughput when per-request latency > 625ms.
2. **Policy-based timeouts**: split **queue wait** from **request execution** (client timeout vs policy timeout), so queueing doesn’t eat the whole budget.
3. **Opt-in response caching**: local FS cache (e.g., `--cache` folder) or distributed cache for reproducible runs.
4. **Compression**: `Accept-Encoding: gzip` to shrink repetitive JSON.
5. **HTTP/2 multiplexing** (if server supports) to reduce head-of-line blocking.
6. **Observability**: counters (total calls, retries, error rate), histograms (latency p50/p95/p99), correlation IDs, minimal tracing (OpenTelemetry).
7. **XML formatter**: plug-in alternative parser if needed.

---

## Likely deep-dive prompts & crisp answers

**Q: Why CLI and not a background worker?**
A: Turnaround speed and determinism. The seams are there—**entry layer is swappable**. To go worker: host with `IHostedService`, move progress to metrics, and shift output to storage.

**Q: Why DDD here; isn’t it overkill?**
A: The **Domain** is tiny but central (Makelaars/Properties). Edge mapping keeps the **ApiClient replaceable** (file/queue/cache) and makes business logic testable with pure domain fixtures. The extra ceremony is a dozen lines but prevents tight coupling.

**Q: How do you handle streaming and partial chunks?**
A: Use async reading; **buffer boundaries** safely (UTF-8 aware), parse incrementally, and **propagate cancellation**. If chunks are large, parse to an internal DTO then **map to domain** (edge mapping) to avoid leaking transport types.

**Q: Rate limiting vs retries—how do they interact?**
A: The **limiter** shapes outbound concurrency/pace; the **retry policy** only fires on transient failures (408/429/5xx) and respects `Retry-After`. This prevents “retry storms.” Under 429s, jitter spreads retries, lowering thundering herds.

**Q: What if we need 10× throughput?**
A: Introduce **bounded parallelism** under the same sliding window, **prefetch** next pages, enable **gzip**, and project only required fields. If that’s insufficient, split to **producer (fetcher) → queue → consumer (aggregator)** with **idempotency keys** and **poison handling**.

**Q: Where do you put mapping—API, Services, or Domain?**
A: **At the edges**: adapters return **domain models**. That keeps the Domain pure and consumers consistent. It also keeps tests simple: domain in, domain out.

**Q: How do you test cancellation works?**
A: Inject a delayable transport, trigger token cancellation during a long read, and assert that downstream tasks observe `OperationCanceledException` and that partial aggregates remain consistent.

**Q: Why not CQRS/MediatR?**
A: Value is marginal for a single use-case CLI. If this became a service with multiple flows and cross-cutting behaviors, I’d model the pipeline via MediatR or decorator chains.

**Q: How do you avoid EF/LINQ N+1 if this becomes a service?**
A: **Projection over `Include`**, `AsNoTracking` for read-paths, compiled queries for hot paths, and indexes validated in migrations. (Not in the CLI, but relevant if we persist results.)

---

## Concrete numbers to remember (easy anchors)

* **Limiter example**: 100 req / 60 s ⇒ **~625 ms** average spacing if single-threaded; with parallelism, ensure **global** rate cap still honored.
* **Retry backoff**: exponential with **jitter**; cap at ~1–2× the mean request latency to avoid user-visible stalls.
* **Progress**: pages completed / total pages, and running count of listings processed.

---

## Decorator pattern (C# micro-sketch you can speak to)

Use case: add logging around a service without changing it.

```csharp
public interface ITopMakelaarsService
{
    Task<IReadOnlyList<MakelaarScore>> ComputeAsync(SearchCriteria criteria, CancellationToken ct);
}

public sealed class LoggingTopMakelaarsService : ITopMakelaarsService
{
    private readonly ITopMakelaarsService _inner;
    private readonly ILogger<LoggingTopMakelaarsService> _log;

    public LoggingTopMakelaarsService(ITopMakelaarsService inner, ILogger<LoggingTopMakelaarsService> log)
    {
        _inner = inner;
        _log = log;
    }

    public async Task<IReadOnlyList<MakelaarScore>> ComputeAsync(SearchCriteria c, CancellationToken ct)
    {
        var sw = Stopwatch.StartNew();
        try
        {
            _log.LogInformation("Compute start {@Criteria}", c);
            var result = await _inner.ComputeAsync(c, ct);
            _log.LogInformation("Compute ok {Count} items in {Elapsed}ms", result.Count, sw.ElapsedMilliseconds);
            return result;
        }
        catch (Exception ex) when (_log.LogError(ex, "Compute failed after {Elapsed}ms", sw.ElapsedMilliseconds) == null)
        {
            throw;
        }
    }
}
```

**One-liner**: “Decorator wraps an interface to add cross-cutting behavior (logging, caching) without modifying the concrete implementation—unlike Strategy, which swaps algorithms.”

---

## “If I had 1 day / 1 week” roadmap

* **1 day:** gzip, metrics counters + latency histograms, `Retry-After` honor check, separate **queue wait** vs **request execution** timeouts, progress UI polish, expand integration tests with golden files.
* **1 week:** bounded parallelism under global rate cap; **file cache**; **HTTP/2** if supported; convert CLI to **HostedService** with background queue; add **OpenTelemetry** traces and **SLOs** (e.g., 99% pages within 2× median latency).

---

## Prepared talking points (drop-in answers)

* **401 vs 429:** “A 429 would make clients safer by default; most retry libraries key off 429 without special-casing.”
* **Pagination origin:** “Single canonical origin simplifies client paging; accepting both 0/1 adds conditional logic and foot-guns.”
* **Why `IHttpClientFactory`:** avoids socket exhaustion, centralizes policies, and enables handler reuse with DNS refresh.

---

### Bonus: 10 “curveball” questions to practice

1. How do you keep the limiter accurate across multiple parallel consumers?
2. What’s your policy for **idempotency** if a page fetch is retried after a partial parse?
3. How do you **fuzz** the parser for resilience (invalid UTF-8, truncated JSON)?
4. Where should **timeouts** live (client vs Polly vs server)?
5. How would you **resume** a partially completed run?
6. How do you validate you’re actually within **Amsterdam** (city text vs geocode vs postal code)?
7. When would you choose **Strategy** over **Decorator** in this codebase?
8. Could **HTTP/2** hurt you here? (head-of-line at TCP vs benefits of multiplexing; server support)
9. How do you prevent a **retry storm** if the API starts failing globally?
10. What metrics would you alert on first? (error rate, 429s, p95 latency, retry count)

---

## Appendix: what changed from the README (for you)

* Tightened the **design story** into an interview-friendly arc (problem → architecture → decisions → trade-offs → testing → roadmap).
* Turned “learnings” and TODOs into **defensible choices** and a **production roadmap**.
* Added **numbers** and **policies** you can quote under pressure.
