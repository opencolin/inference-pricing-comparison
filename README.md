# Baseten vs Nebius Token Factory — DeepSeek V4 Flash pricing

A cost comparison of serving [DeepSeek V4 Flash](https://artificialanalysis.ai/models/deepseek-v4-flash) on **Baseten** and **Nebius Token Factory** at high throughput.

**Finding:** list prices sit within 8% of each other, so the rate card is not the deciding factor. Prompt caching is. Baseten prices cached input at $0.028/1M — a 78% discount — while Nebius has no billable cached-input rate. On an agentic workload with 90% prefix reuse, that opens a 74% gap.

## Pricing (per 1M tokens, August 2026)

| Provider | Input | Cached input | Output |
|---|---|---|---|
| Baseten | $0.13 | $0.028 | $0.26 |
| Nebius Token Factory | $0.14 | none | $0.28 |

## Reference workload

2M tokens/min at 1,000 req/min, 70/30 input/output, 30-day month (~86B tokens/month):

| Input cache hit rate | Baseten | Nebius | Nebius premium |
|---|---|---|---|
| 0% | $14,602 | $15,725 | +8% |
| 50% | $11,517 | $15,725 | +37% |
| 90% | $9,050 | $15,725 | +74% |

Two caveats. Both vendors offer volume discounts and **neither publishes the numbers** — Nebius would need roughly 7% off to match Baseten at zero cache reuse, or 42% at 90% hits, but whether that's on offer is a question for a quote. And Baseten's self-serve tiers cap at **120 RPM**, so the reference workload requires an Enterprise agreement there. See the full write-up for both.

## Running locally

Static site, no build step:

```bash
python3 -m http.server 8000
```

## Caveats

Nebius pricing was read from the Token Factory console directly. Third-party aggregators carried a materially different figure for this model, so verify any rate against the vendor's own console. Prices change frequently — check both vendors before making a decision.

Full method, caveats, and sources are in [`index.html`](index.html).
