# European Gas Storage Monitor

A daily-updating analytical model of European natural gas storage, built to read the
physical fundamental that sits underneath the TTF forward curve.

---

## Why storage

European gas storage is the most-watched physical fundamental in the TTF complex. The
seasonal storage cycle — inject in summer when demand is low, withdraw in winter when
heating demand peaks — is the reason the forward curve carries its seasonal shape, and the
summer–winter spread is the traded expression of storage economics. The no-arbitrage
condition for injecting in summer is, roughly:

> winter price − summer price ≥ storage cost + financing cost of the gas held

When that spread is wider than carry, injection is economic and storage fills; when it is
narrow or negative, injection lags. So storage level, the *pace* of injection/withdrawal,
and where both sit relative to the seasonal norm are the first things a power & gas desk
checks — and the gap between what storage *implies* and what the market has *priced* is
where trade ideas live.

This model quantifies the first half of that sentence: **is European storage tight, loose,
or normal right now, and is the trajectory taking it somewhere the market should care
about?** It is the fundamentals-literate baseline that a price-vs-fundamentals view is
built on.

---

## What it does

Pulling daily storage data from the GIE AGSI+ transparency platform (2017–present), the
model produces, for five core EU storage countries and a capacity-weighted EU aggregate:

- **Seasonal positioning** — current storage benchmarked against a trailing 5-year
  seasonal band, with deviation from the median and a percentile rank for the calendar day
  (the "tight or loose, in one number" read).
- **Flow & momentum** — net injection/withdrawal pace, measured both as a ratio to the
  seasonal norm and as a standardised z-score that stays robust at season turns.
- **Resilience** — days-of-cover against a high-percentile historical winter draw, so the
  metric answers "how long would stock last in a real cold snap," not "in today's summer."
- **Trajectory** — a forward projection of the refill against each country's national fill
  target, framed as required-pace-vs-current-pace rather than a point forecast.
- **An EU tightness index** — the aggregate deviation expressed in standard deviations of
  the seasonal norm, a single number comparable across regimes (e.g. today vs the 2022
  crisis).
- **A generated storage brief** per country — plain-language narration of all of the above
  that reads like a desk analyst's morning note.

Output is structured for two uses: an EU roll-up and scan as a daily morning read, and
per-country drill-downs (fan chart + projection + brief) when something in the scan flags
attention.

---

## Design decisions that matter

The analytical choices are the point of the project, not the plumbing. The notable ones:

- **Median, not mean, as the seasonal benchmark.** The 2022–24 energy crisis force-filled
  storage toward 90%+, inflating the *upper half* of any reference distribution. The median
  and minimum are robust to that contamination; the mean and maximum are not. The model
  reads off the robust statistics and documents the bias rather than hiding it.
- **Windowed pooling for the seasonal band.** Statistics for each calendar day are pooled
  from a ±7-day window across the reference years (~75 observations), not the 5 values on
  that exact day — enough sample for stable quantiles.
- **A robust pace measure.** A simple pace ratio explodes near season turns, where the
  seasonal-norm denominator approaches zero. The model carries a standardised z-score
  (pace minus norm, in standard deviations of the period) that stays meaningful year-round,
  and guards the ratio against the near-zero blow-up.
- **Capacity-weighted aggregation.** The EU figure is built by summing absolute volumes and
  dividing — never by averaging the five countries' percentages, which would let a small
  country distort the aggregate.
- **Per-country self-calibration.** Each country is measured against *its own* history, so
  one engine serves five structurally different storage systems (large seasonal stores,
  fast-cycle Dutch storage, export-linked Austrian capacity) without hand-tuned thresholds.

## Scope and limitations

Stated deliberately — knowing what the model does *not* do is part of using it honestly.

- **Five countries (DE, FR, IT, NL, AT).** These hold roughly two-thirds of EU storage
  capacity. The UK is excluded: its AGSI+ reporting stopped in late 2020, which reflects
  the underlying reality — post-Rough-closure Britain holds minimal seasonal storage and
  runs on continuous LNG and Norwegian imports, making it an import-flow question, not a
  storage one.
- **Storage only.** This is the fundamentals baseline. The price layer (TTF curve,
  summer–winter spread, storage-vs-spread) is the planned next phase; clean TTF settlement
  data is the gating constraint.
- **The projection is a scenario, not a forecast.** It extrapolates a defined pace over the
  season; it carries no confidence interval and no account of why a given year might differ.
  A proper statistical forecast (SARIMA, with intervals) is a planned econometric extension.
- **The reference band includes the crisis years by design** (2021–2025), since pre-2022
  storage behaviour reflects Russian pipeline supply that no longer exists and is therefore
  structurally obsolete rather than a cleaner baseline. The model manages the resulting bias
  via robust statistics rather than excluding the data.

---

## Roadmap

1. **Price layer** — TTF front-month and the summer–winter spread overlaid on storage;
   the storage-deviation-vs-spread relationship through a cost-of-carry lens.
2. **Supply-side feeds** — LNG send-out (GIE ALSI), Norwegian pipeline flows, weather /
   heating-degree-days as a demand proxy.
3. **Econometric layer** — SARIMA forecasting with intervals; cointegration testing of the
   storage–spread and cross-country relationships.

---

## Running it

1. Register for a free AGSI+ API key at the GIE transparency platform.
2. Add it as a Colab secret named `AGSI_KEY` (or set it as an environment variable if
   running locally).
3. Run the notebook top to bottom. The data pull caches to CSV; subsequent runs read the
   cache.

Requirements: `pandas`, `requests`, `matplotlib`. Data: GIE AGSI+ (free registration).

---

*Built as a self-directed project ahead of an MSc in Finance, oriented toward
commodities/energy markets analysis. The reasoning behind each analytical choice is
documented inline in the notebook.*
