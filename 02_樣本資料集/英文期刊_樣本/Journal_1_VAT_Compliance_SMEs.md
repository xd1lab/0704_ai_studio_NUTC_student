> ※ 本文件為教學用合成範例,情節與人物純屬虛構,非真實個案,不得作為法律依據。
> (This is a fully SYNTHETIC teaching sample. All authors, institutions, data, and findings are fictional. Not a real study. Do not cite.)

# Digital Invoicing and VAT Compliance Among Small and Medium Enterprises: Evidence from a Synthetic Panel

**Authors (fictional):** L. Chen, M. Rivera, P. Okafor
**Affiliation (fictional):** Institute of Fiscal Studies, Meridian University
**Journal (fictional):** *Journal of Public Finance and Tax Policy*, Vol. 18(2), pp. 114–139 (synthetic)

---

## Abstract

This study examines whether the adoption of mandatory electronic invoicing (e-invoicing) improves value-added tax (VAT) compliance among small and medium enterprises (SMEs). Using a synthetic panel of 4,200 fictional firms observed over six fiscal years, we estimate the effect of e-invoicing mandates on the VAT compliance gap—defined as the difference between VAT theoretically due and VAT actually remitted. Exploiting a staggered rollout across regions, we apply a difference-in-differences design. We find that e-invoicing adoption is associated with a reduction in the compliance gap of approximately 4.6 percentage points, with the largest effects concentrated among micro-firms in cash-intensive sectors. The results suggest that transaction-level digital reporting narrows opportunities for under-reporting, though effects attenuate where enforcement capacity is weak. All data are simulated for instructional purposes.

## Method

We construct a balanced synthetic panel of 4,200 firms across four regions (North, South, East, West) for fiscal years Y1–Y6. E-invoicing mandates were introduced on a staggered schedule: North in Y2, East in Y3, South in Y4, and West in Y5. The outcome variable is the firm-level VAT compliance gap (%), estimated as `(VAT_due − VAT_remitted) / VAT_due × 100`.

The baseline specification is a two-way fixed-effects model:

```
Gap_it = β0 + β1·Einvoice_it + γ·X_it + μ_i + λ_t + ε_it
```

where `Einvoice_it` equals 1 in periods after a firm's region is subject to the mandate, `X_it` is a vector of firm controls (log turnover, sector, firm age), `μ_i` are firm fixed effects, and `λ_t` are year fixed effects. Standard errors are clustered at the region level. We treat pre-mandate years as controls and conduct an event-study check for parallel pre-trends.

## Results

Table 1 reports the mean VAT compliance gap (%) by region and year. The compliance gap declines in each region following the introduction of the e-invoicing mandate (mandate onset shown in **bold**).

**Table 1. Mean VAT compliance gap (%) by region and fiscal year (synthetic data)**

| Region | Y1   | Y2       | Y3       | Y4       | Y5       | Y6   |
|--------|------|----------|----------|----------|----------|------|
| North  | 18.2 | **17.9** | 15.1     | 13.8     | 13.0     | 12.6 |
| East   | 17.6 | 17.4     | **17.0** | 14.2     | 13.5     | 13.1 |
| South  | 19.0 | 18.7     | 18.3     | **18.0** | 15.2     | 14.6 |
| West   | 16.8 | 16.5     | 16.1     | 15.9     | **15.4** | 12.9 |

Consistent with Table 1, the compliance gap **falls** after each mandate onset. Averaged across regions, the gap declines from 17.9% in Y1 to 13.3% in Y6, a drop of 4.6 percentage points. The difference-in-differences estimate of β1 is −4.6 (p < 0.01), indicating that e-invoicing is associated with a statistically significant reduction in under-reporting. Event-study estimates show no significant pre-trends in the two years before each mandate.

## Conclusion

Our synthetic evidence indicates that mandatory e-invoicing can meaningfully reduce the VAT compliance gap among SMEs, particularly for smaller, cash-intensive firms. The direction of the effect in the underlying data table is internally consistent: the reported narrowing of the gap in the text matches the declining values shown in Table 1. Policymakers considering digital reporting reforms should pair mandates with adequate enforcement capacity to sustain compliance gains. As all data are fictional, these findings are intended solely for teaching how to read, tabulate, and cross-check empirical claims.
