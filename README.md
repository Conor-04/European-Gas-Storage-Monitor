**European Gas Balance — Storage Monitor & Spread View**

A daily model of European gas fundamentals that turns storage tightness into a falsifiable view on the TTF summer–winter spread.

**Thesis:** Storage level, pace, and position vs the seasonal norm are fundamental to the European gas market, this notebook quantifies the storage side, models the two biggest suppliers (Norwegian pipe, LNG send-out), and closes with a view on the TTF summer–winter spread.

Built in Python (Colab). Storage systems covered: Germany, France, Italy, Netherlands, Austria — together ≈73% of EU working gas volume.

**What it produces**

The output is a computed desk note, not hand-written. Example run (2026-06-30 data):

EVIDENCE  (as of 2026-06-30; anomalies = 4wk mean vs 2023+ norm, GWh/d)
  storage    : 48.0% full, -98 TWh vs norm; flow -115 (below-normal injection)
  end-season : ~84% by 1 Nov on current gap (-6 pts vs 90% target)
  supply     : Norway +102 | LNG -20
  demand res : +197 (above norm; tracks HDD, r = -0.79)
  curve      : summer-winter -1.29 EUR/MWh vs ~2.5 carry (2026-07-02)

DESK NOTE
  * EU-5 48% full, -98 TWh vs norm -> ~84% end-season (short of the 90% target).
  * Mix: firm demand only partially offset by firm Norwegian flows.
  * Curve: -1.29 vs ~2.5 carry -> summer over winter — no tightness priced; AT ODDS with fundamentals.
  * Skew: one-sided — firm demand persists OR the offset (firm Norwegian flows) fades into winter; either slips fill below ~84% -> spread biased wider.

Read top to bottom, the balance is running ~100 TWh tight versus the seasonal norm and tracking short of the 90% target, the curve is pricing no tightness at all, and the risk is one-sided. The note regenerates on every run, so it reads correctly whatever the market is doing, not just this snapshot.

**How it's built**

1 · Data layer — every source pulled, cleaned, cached (refreshed once daily), and coverage-verified, then merged onto one daily frame:

Storage — GIE AGSI+ (per-country stock, injection/withdrawal, working gas volume)
Norwegian pipeline supply — ENTSOG via eurogastp (metered North Sea entry points)
LNG send-out — GIE ALSI (NW-European regas terminals feeding the EU-5 grid)
Weather — Open-Meteo (heating/cooling degree days, gas-demand-weighted to one EU series)
Price — TTF front-month (Yahoo TTF=F)

2 · Storage monitor — each country is benchmarked against its own five-year history: seasonal fan charts, a percentile-and-tightness read, pace scoring, days-of-cover, and per-country projections to national fill targets.

3 · Storage ↔ price — shows why the storage tightness is more applicable to the spread, not the outright price: the storage–front-month link was strong pre-2022, broke in the crisis, and has been weak-to-negative since. Scarcity is priced in the shape of the curve.

4 · Market Implications — the evidence block and desk note above.

**Design choices worth reading**

- Norway and LNG are modelled because they are the biggest contributors and the swing legs where short-term supply variance is most applicable. Unmodelled legs (Algerian/Azeri pipe into IT, TurkStream, UK interconnectors, trade with the rest-of-EU 27%) largely sit inside the norm and cancel; off-pattern behaviour lands in the residual as minor terms alongside demand.
- Median-and-MAD benchmarking, notebook-wide. The seasonal norms read off the median with a robust (MAD) scale to avoid the 2022–24 crisis inflating the distribution and flattening real signals.
- Windowed pooling (±7 days, wrapping the year boundary) for stable seasonal quantiles from thin daily history.
- Capacity-normalised levels, so fullness compares across capacity eras rather than raw TWh.
- Coverage guards on every aggregate — partial-reporting days are masked, not silently summed to a false low, on both the LNG panel and the weather series.
- Deviation-carry projection. End-season fill is projected by carrying today's gap-to-norm forward to the November peak, rather than extrapolating a noisy 30-day pace across four months.

**Scope and limitations**

- Seasonal-spread strip prices are entered manually (no open historical feed exists); the storage→spread link is carried by cost-of-carry logic, not a fitted coefficient. A staleness guard warns if the manual print lags the storage data.
- The demand residual carries off-pattern unmodelled flows as minor terms; the HDD validation is in winter observations, so summer residual anomalies read as power burn / industry rather than heating.
- TTF TTF=F is a proxy series, adequate for the §3 motivation charts only.



**Running it**

Colab, top to bottom. Requires a free GIE API key added as the Colab secret AGSI_KEY (one key covers both AGSI+ storage and ALSI LNG).

---

*Built as a self-directed project ahead of an MSc in Finance, oriented toward
commodities/energy markets analysis. The reasoning behind each analytical choice is
documented inline in the notebook.*
