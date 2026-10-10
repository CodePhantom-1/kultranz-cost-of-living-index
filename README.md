# US Metro Cost of Living Index (BEA RPP) — Median Rent, Home Value & Income for 50 Metros

![License: CC BY 4.0](https://img.shields.io/badge/license-CC%20BY%204.0-blue.svg)
![Vintage](https://img.shields.io/badge/vintage-2025--26-green.svg)
![Formats](https://img.shields.io/badge/formats-CSV%20%7C%20JSON-orange.svg)

A free, open dataset of the **US metro cost-of-living index** — BEA **Regional Price Parities (RPP, all items)** — plus **median rent, median home value, median income, and population** for **50 major US metros** (2025–26 vintage). Maintained by [kultranz.com](https://kultranz.com).

## What's inside

| | |
|---|---|
| Metros | **50** major US metros across 29 states + DC |
| Cost-of-living index | BEA RPP all-items, US average = 100 — present for **50 / 50** metros |
| Median rent & median home value | present for **45 / 50** metros each |
| Median income & population | present for **45 / 50** metros each |
| Vintage | 2025–26 compilation |

Missing values are genuine gaps in the source data: they ship as empty cells (CSV) / `null` (JSON). Nothing is imputed, interpolated, or rounded.

**Row counts**

- `data/cost_of_living_index.csv` — **50 rows**, one per metro
- `data/cost_of_living_index.json` — same 50 metros + a `meta` block (sources, null policy, license)

## File map

```
kultranz-cost-of-living-index/
├── data/
│   ├── cost_of_living_index.csv   # 50 rows — city, state, rpp_all_items, median_rent,
│   │                              #         median_home_value, median_income, population
│   └── cost_of_living_index.json  # { meta, metros: [ ...50 objects ] }
├── LICENSE                        # CC BY 4.0
└── README.md
```

## Quick start

### Python — rank metros, adjust income for prices

```python
import csv

with open("data/cost_of_living_index.csv") as f:
    metros = list(csv.DictReader(f))

# 5 most expensive metros by RPP (US = 100)
ranked = sorted(metros, key=lambda m: float(m["rpp_all_items"]), reverse=True)
for m in ranked[:5]:
    print(f'{m["city"]}, {m["state"]}: {m["rpp_all_items"]}')

# Deflate a metro's median income to national-average price level
def real_income(city):
    m = next(m for m in metros if m["city"] == city)
    return float(m["median_income"]) / (float(m["rpp_all_items"]) / 100)

print(f'${real_income("San Francisco"):,.0f}')   # SF median income in "average-price" dollars
```

### JavaScript (Node)

```js
const fs = require("fs");
const { metros } = JSON.parse(
  fs.readFileSync("data/cost_of_living_index.json", "utf8")
);

// Cheapest metros by RPP
const cheapest = [...metros]
  .filter((m) => m.rpp_all_items != null)
  .sort((a, b) => a.rpp_all_items - b.rpp_all_items)
  .slice(0, 5)
  .map((m) => `${m.city}, ${m.state}: ${m.rpp_all_items}`);

console.log(cheapest);
// -> Wichita, Tulsa, El Paso, ... (US average = 100)

// Rent burden: median annual rent as a share of median income
const sf = metros.find((m) => m.city === "San Francisco");
console.log(((sf.median_rent * 12) / sf.median_income).toFixed(2));
```

## Data dictionary

| Column | Meaning |
|---|---|
| `city` | Metro's principal city |
| `state` | USPS two-letter code (`DC` = District of Columbia) |
| `rpp_all_items` | BEA Regional Price Parity, all items — cost-of-living index where **US average = 100**. Values above 100 are pricier than average; below 100 cheaper. |
| `median_rent` | Median gross rent, USD per month |
| `median_home_value` | Median home value, USD |
| `median_income` | Median household income, USD per year |
| `population` | City population |

## Sources & methodology

- **Cost-of-living index:** US Bureau of Economic Analysis, **Regional Price Parities (all items)**.
- **Rent, home value, income, population:** US Census Bureau, **American Community Survey (ACS)**.
- Compiled and cross-checked by [kultranz.com](https://kultranz.com) — see the full [methodology page](https://kultranz.com/pages/methodology/?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct).
- In the source database these metro-level fields are constant per metro; where a value was present on only some source rows, the single distinct non-null value is used. No value was imputed or rounded.

## License

[CC BY 4.0](./LICENSE). You are free to share and adapt for any purpose, including commercially, with attribution:

> **kultranz.com** — source: [kultranz cost-of-living index dataset](https://github.com/CodePhantom-1/kultranz-cost-of-living-index?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct), links to https://kultranz.com

## Commercial use & API access

The CC BY 4.0 license covers commercial use at no cost: attribution to kultranz.com is the only requirement, so you can ship this dataset inside commercial products, reports, and internal tools for free.

If your product needs live computed values rather than static files, use the hosted API instead. It powers the paycheck engine (take-home pay for any salary, state, and filing status), city-to-city comparisons, and salary percentiles, so you do not have to reimplement the tax engine yourself:

- **Free tier:** 25 requests/day
- **$5 lifetime key:** https://kultranz.com/pages/api-docs/?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct
- **RapidAPI tiers** (Pro, Ultra, Mega): https://rapidapi.com/cianot978/api/kultranz?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct
- **MCP server** for LLM and agent integration: https://api.kultranz.com/mcp

## More from kultranz.com

- 🏙️ [Cost of Living by City](https://kultranz.com/cost-of-living/?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct) — this dataset in action, metro by metro
- ⚖️ [Compare Cities](https://kultranz.com/compare/?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct) — side-by-side salary & cost-of-living comparisons across metros
- 📖 [Methodology](https://kultranz.com/pages/methodology/?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct) — how every number was sourced and checked
- 💵 [Paycheck Calculator](https://kultranz.com/tools/paycheck-calculator/?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct) · 🔌 [API (with a $5 lifetime tier)](https://kultranz.com/pages/api-docs/?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct)
- Sibling dataset: [kultranz-2026-tax-brackets](https://github.com/CodePhantom-1/kultranz-2026-tax-brackets?utm_source=github&utm_medium=organic&utm_campaign=revtest-oct) — 2026 federal + all 51 state tax parameters
