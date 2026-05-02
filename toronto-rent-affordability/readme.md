# Toronto Rent Affordability Index (2018–2026)

**How many months of income does a Toronto renter need to cover one month of rent — and how has that changed?**

This project builds a Toronto Rent Affordability Index combining official rental survey data, median income statistics, and CPI inflation adjustment to answer one specific, hard question across 8 years, 4 bedroom types, and 10 GTA neighbourhoods.

Built as a portfolio project by [Aishwarya Senthil](https://www.linkedin.com/in/aishwarya-senthil/) — Data & Business Analyst based in Mississauga, Ontario.

---

## The headline finding

Not one GTA neighbourhood is affordable for a median-income single renter. The cheapest option — Oshawa — still consumes **44.8% of monthly income** on a 1-bedroom unit. Toronto has been continuously above the 30% affordability threshold since at least 2018.

---

## Project structure

```
toronto-rent-affordability/
├── data/
│   ├── raw/
│   │   ├── cmhc/                        # 7 CMHC Excel files (2019–2025)
│   │   ├── statcan/                     # Income + CPI CSVs
│   │   └── manual/                      # Rentals.ca and liv.rent 2026 bridge data
│   └── processed/
│       ├── master_rent_series.csv       # CMHC + 2026 bridge, with CPI deflator
│       ├── affordability_index.csv      # Rent burden % by bedroom type and year
│       ├── recovery_gap.csv             # How much rent must fall to restore 2019 affordability
│       └── neighbourhood_snapshot.csv  # 10 GTA neighbourhoods, Q1 2026
├── notebooks/
│   └── toronto_rent_analysis.ipynb
├── visuals/
│   ├── chart1_rent_burden.html
│   ├── chart2_nominal_vs_real.html
│   ├── chart3_vacancy_rent.html
│   ├── chart4_neighbourhood.html
│   ├── chart5_recovery_gap.html
│   └── chart6_all_trends.html
├── dashboard/
│   ├── toronto_rent_page1_executive.html
│   ├── toronto_rent_page2_trends.html
│   ├── toronto_rent_page3_supply_neighbourhood.html
│   └── toronto_rent_page4_recommendations.html
├── requirements.txt
└── README.md
```

---

## Data sources

| Dataset | Source | Years | Notes |
|---|---|---|---|
| CMHC Rental Market Survey | Canada Mortgage and Housing Corporation | 2018–2025 | Gold standard. Surveyed paid rents, October snapshot each year. Table 1.1.2 (avg rent) and 1.1.1 (vacancy). Toronto CMA row. |
| Median after-tax income | Statistics Canada Table 11-10-0190-01 | 2018–2023 | Persons not in an economic family, Toronto CMA, 2023 constant dollars. |
| CPI — Rented accommodation | Statistics Canada Table 18-10-0004-01 | 2018–2025 | Used to compute annual deflators. Base year: 2019 = 100. |
| Asking rents (2026 bridge) | Rentals.ca monthly reports | Q1 2026 | Advertised listings — structurally higher than CMHC surveyed. Used only for 2026 trend extension. |
| GTA neighbourhood rents | liv.rent monthly Ontario reports | Q1 2026 | 10 GTA neighbourhoods, 1BR and 2BR, Jan–Mar 2026 averaged. |

---

## Methodology

### Affordability index formula

```
rent_burden_pct = (monthly_rent / monthly_income) × 100
monthly_income  = annual_median_after_tax_income / 12
```

The 30% threshold is the widely-used national benchmark: housing costs above 30% of gross income are considered unaffordable. All figures in this project use after-tax income, which is a stricter (more realistic) measure than gross income.

### Inflation adjustment

Nominal rents are converted to real rents using the CPI Rented Accommodation component (StatCan 18-10-0004-01), annual average, base year 2019:

```
real_rent = nominal_rent × cpi_deflator
```

CPI deflators used:

| Year | Deflator |
|---|---|
| 2018 | 1.0262 |
| 2019 | 1.0000 (base) |
| 2020 | 0.9836 |
| 2021 | 0.9672 |
| 2022 | 0.9242 |
| 2023 | 0.8693 |
| 2024 | 0.8056 |
| 2025 | 0.7679 |

### Income estimates (2024–2026)

StatCan income data runs to 2023. Years 2024, 2025, and 2026 are estimated using LFS average wage growth as a proxy at +3.5% per year:

| Year | Estimated median annual income |
|---|---|
| 2024 | $42,021 |
| 2025 | $43,523 |
| 2026 | $45,066 |

### CMHC data extraction

Each CMHC Excel file contains two years side by side (e.g. the 2019 file contains Oct-18 and Oct-19 columns). The notebook takes the newer column from each file, except from the 2019 file where it also extracts the older column to get 2018 data. A custom `clean_val()` function strips CMHC quality flags (letter suffixes a/b/c/d, arrows, asterisks) before converting to floats.

---

## Key findings

**1. No GTA neighbourhood is affordable.** Not one of the 10 GTA neighbourhoods analysed falls below the 40% burden mark for a 1BR unit in Q1 2026. The cheapest — Oshawa at $1,683/month — still consumes 44.8% of median monthly income. The most expensive — Oakville at $2,199 — reaches 58.5%.

**2. Toronto has been continuously unaffordable since 2018.** The lowest rent burden recorded in the entire dataset is Bachelor units in 2021 at 34.0% — still 4 percentage points above the 30% threshold.

**3. The crisis peaked in 2023.** The 3-bedroom+ burden hit 64.8% of income. To afford a 3BR at 30% of income, a renter would need to earn $7,300 per month.

**4. Surveyed rents have not declined.** Despite "rent cooldown" narratives in asking-rent platforms, no CMHC bedroom type showed a decline through 2025. The gap between asking rent trends and surveyed paid rents is a significant and counterintuitive finding.

**5. Supply drives price (correlation: −0.540).** When vacancy rose to 4.80% in 2021, rent growth fell to 2.19%. When vacancy fell to 1.48% in 2023, rent growth hit 9.08%. The relationship is clear.

**6. In real terms, 1BR rent has nearly returned to 2019 levels.** Inflation has done the work, not rent cuts. Nominal 1BR rent is still ~$400 above 2019. In 2019 dollars, real rent has fallen back close to the 2019 baseline — a nuance most rent analyses miss.

**7. The recovery gap grows with bedroom size.** To restore 2019 affordability at current income levels, rents need to fall by: Bachelor −$233/mo, 1BR −$263/mo, 2BR −$327/mo, 3BR+ −$409/mo.

---

## BA recommendations

**Rec 1 — Target purpose-built 1BR and 2BR supply.** Generic "build more housing" policy misses the unit-mix problem. 1BR and 2BR show the largest burden gaps vs 2019, affecting the broadest share of renters. Province-level incentives should be calibrated to these unit types specifically. Evidence: every 1pp rise in vacancy correlates with approximately 1.5% lower rent growth.

**Rec 2 — Expand transit to reduce the proximity premium.** The outer GTA is only "more affordable" in absolute rent terms — poor transit links force concentrated demand into expensive inner zones. Oshawa is 23% cheaper than Oakville for the same unit type, but commute viability is the constraint.

**Rec 3 — Retire the 30% benchmark for Toronto.** The 30% rule is a 1980s national standard. In Toronto, it fails as a policy signal: it cannot distinguish between "stretched" and "in crisis" because every household is above it. Toronto-specific benchmarks stratified by household size and transit access would better direct subsidies and social housing allocation.

---

## Limitations

- CMHC data represents an October snapshot each year, not a continuous monthly average.
- Bachelor unit data for 2025 was suppressed by CMHC due to small sample size (shown as NaN).
- Income figures for 2024, 2025, and 2026 are estimates based on LFS wage growth proxy — not published StatCan data.
- 2026 data uses Rentals.ca asking rents (advertised listings), which are structurally higher than CMHC surveyed paid rents. The two series are not directly comparable and are distinguished clearly in all charts.
- liv.rent neighbourhood data covers asking rents only and represents a limited sample of actively listed units.
- The 30% affordability threshold is a national benchmark, not calibrated to Toronto's specific income and cost structure.

---

## How to run

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/toronto-rent-affordability.git
cd toronto-rent-affordability

# Install dependencies
pip install -r requirements.txt

# Launch the notebook
jupyter notebook notebooks/toronto_rent_analysis.ipynb
```

All charts are saved as interactive HTML files in `/visuals/`. Open in any browser — no server required.

---

## Requirements

```
pandas
openpyxl
plotly
jupyter
scipy
```

---

## About

Aishwarya Senthil is a Data and Business Analyst with 3+ years of experience in healthcare and consumer goods analytics. Based in Mississauga, GTA. Open to DA and BA roles across the Greater Toronto Area.

[LinkedIn](https://www.linkedin.com/in/aishwarya-senthil/) · [GitHub](https://github.com/aishwaryas15)