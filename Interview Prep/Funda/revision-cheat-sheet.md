awesome — here’s both deliverables, ready to use.

# 1-pager: Assignment Crib Sheet (printable)

**60-sec pitch**

> Built a structured **.NET CLI** that calls Funda’s API, **streams & aggregates listings**, and outputs **top makelaars** (optionally filtered, e.g., *tuin*). Kept a stable **Domain** with **edge mapping** at boundaries. The **ApiClient** uses `IHttpClientFactory` + reusable handler, **sliding-window rate limiting**, **retries with exp backoff + jitter**, and **pagination**. All long ops honor **CancellationToken** and report progress via `IProgress<T>`. The **CLI** is the composition root with config layering (defaults → `appsettings.json` → flags). Tests cover URL building, mapping, aggregation, and DI-wired flows. To productionize: bounded parallelism under the limiter, gzip, policy-based timeouts, metrics/tracing.

**Architecture (mini map)**

```
CLI (args + appsettings)
  → DI (typed HttpClient, options, services)
  → ApiClient (URL builder, paging, limiter, retries, streaming parse)
     → Edge mapping → Domain models
  → Services (TopMakelaars aggregation + progress)
  → Presenter (tables/summary)
```

**Key decisions & trade-offs (remember 5)**

1. **CLI now, service later** — fast iteration; seams to swap entry layer.
2. **DDD + edge mapping** — Domain stays pure; adapters conform.
3. **Sliding-window limiter** — caps N/60s windows; prevents burst cliffs.
4. **Retries (408/429/5xx) w/ jitter** — respects `Retry-After`; avoids storms.
5. **`HttpClientFactory` + handler reuse** — prevents socket exhaustion; handles DNS.

**Deep-dive menu (pick one if asked)**

* Streaming correctness: chunk boundaries, UTF-8 splits, cancellation propagation.
* Rate limit vs retry layering; global vs per-request timeouts.
* Progress & resumption: pages done, listings processed; how I’d checkpoint.
* Scaling to 10×: bounded parallelism, gzip, projection-only, queue+worker.

**Numbers to quote**

* Example limiter: **100 req / 60 s → ~625 ms** average spacing (single-thread).
* Under parallelism, keep a **global token bucket** to respect N/60s.
* Metrics first: error rate, 429s, retry count, p95/p99 latency, throughput.

**API feedback (2 items)**

* Quota returns **401** → prefer **429 Too Many Requests** for library-friendly retries.
* Pagination origin accepts **0 or 1** → choose one canonical start.

**Production roadmap**

* **1 day:** gzip, counters + latency histograms, split queue-wait vs exec timeouts, tighten retry policy, golden-file parser tests.
* **1 week:** bounded parallelism under limiter, file-cache option, HTTP/2 (if server), convert to HostedService, OpenTelemetry traces + SLOs.

**Decorator (30-sec answer)**

> Wrap an interface to add cross-cutting behavior without changing the concrete. In C#: `IService` → `LoggingDecorator(IService inner)`. Unlike **Strategy**, it doesn’t swap the algorithm; it composes behavior.

