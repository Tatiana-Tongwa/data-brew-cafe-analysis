# Data Brew Cafe — A System Dynamics Feasibility Study

> *Can a small café in Helsinki turn a profit? A simulation-based feasibility study using Monte Carlo methods, global sensitivity analysis, and simulation decomposition.*

---

## Project Overview

**Data Brew Cafe** is a System Dynamics model that evaluates the 24-month financial viability of opening a small café in Helsinki. The model tracks a single stock — the café's **cash balance** — which grows or shrinks each month based on the difference between revenue inflows and operating-cost outflows, after deducting a one-time startup investment.

The central question: *under realistic uncertainty about demand, pricing, and costs, is this business likely to be profitable — and which factors matter most?*

The core relationship modeled is:

```
24-Month Balance = (Monthly Revenue − Monthly Costs) × 24 − Initial Investment

where:
  Monthly Revenue = Daily Customer Traffic × Average Spend × Working Days
  Monthly Costs   = (Monthly Revenue × Raw Material Cost %) + Rent
```

The model combines **five uncertainty variables** with **two decision variables**, then explores the resulting outcome space through sensitivity analysis, Monte Carlo simulation, multiple sampling strategies, global sensitivity analysis, and simulation decomposition (SimDec).

---

## Assignment Background

This project was developed for the course **A220A6501 – System Dynamics with Applications** at **LUT Business School** (instructor: Mariia Kozlova).

The assignment asked students to build a model of their choice (a startup plan, in this case), define a minimum number of uncertain and decision variables, and then apply a structured set of analytical techniques: basic sensitivity analysis, uncertainty/sampling modeling, global sensitivity analysis, and model-behavior visualization, each contributing to the grade. The brief allowed a free choice of modeling environment; this team chose **Python**.

---

## Methodology

The analysis proceeds through six stages, mirroring the structure of the assignment.

**1. Model definition.** Five uncertainty variables and two decision variables, with base values grounded in Helsinki market research:

| Type | Variable | Base value / range |
|------|----------|--------------------|
| Uncertainty | Daily customer traffic | ~55/day (range 35–70) |
| Uncertainty | Average spend per customer | ~€8 (range €6–10) |
| Uncertainty | Raw material cost (% of sales) | ~30% (range 25–35%) |
| Uncertainty | Monthly rent | ~€1,050 (range €900–1,300) |
| Uncertainty | Initial investment | ~€32,000 (range €28k–38k) |
| Decision | Location | City Centre / Neighbourhood |
| Decision | Business model | Café only / Full menu |

**2. Basic sensitivity analysis.** A tornado plot (one-at-a-time, ±20%), a heat map (two-at-a-time: customer traffic × average spend), and a three-state scenario analysis (optimistic / expected / pessimistic).

**3. Monte Carlo simulation.** 10,000 simulations using triangular distributions for each uncertain input, reporting the mean, median, standard deviation, 5th/95th percentiles, and probability of loss, visualized as a histogram.

**4. Uncertainty modeling and sampling.** Four distribution types assigned to the uncertain inputs — **truncated normal** (traffic), **lognormal** (spend), **beta** (raw material cost), and **uniform** (rent) — followed by three sampling strategies: **simple random sampling**, **quasi-Monte Carlo (Sobol sequences)**, and **partial factorial + random design**.

**5. Global sensitivity analysis.** Two methods compared: **Sobol indices** (simple binning approach) and **regional sensitivity analysis** (Monte Carlo filtering, using the Kolmogorov–Smirnov statistic to separate successful from unsuccessful outcomes).

**6. Visualization (SimDec).** Simulation decomposition of the 24-month balance by the two most influential variables, plus a two-output scatter plot (12-month vs. 24-month balance, encoded by location and staffing) and a parallel coordinates plot.

---

## Technologies Used

- **Language:** Python 3
- **Environment:** Jupyter Notebook
- **Libraries:**
  - `numpy` — numerical computation and random sampling
  - `pandas` — data structuring and tabular output
  - `matplotlib` — plotting (tornado, histograms, scatter, tables)
  - `seaborn` — heat map visualization
  - `scipy` — statistical distributions, Sobol sampling (`scipy.stats.qmc`), KS test
  - `plotly` — interactive parallel coordinates plot
  - `simdec` — simulation decomposition

---

## Repository Structure

```
data-brew-cafe-simulation/
├── notebooks/
│   └── data_brew_cafe.ipynb         # Main analysis notebook
├── report/
│   └── data_brew_cafe_report.pdf    # Written report (LUT submission)
├── docs/
│   └── assignment_brief/            # Assignment description (reference)
├── figures/                         # Exported plots (optional)
├── requirements.txt                 # Python dependencies
├── .gitignore
└── README.md
```
---

## Usage

Open the notebook and run the cells top to bottom:

```bash
jupyter notebook notebooks/data_brew_cafe.ipynb
```

Each section is self-contained and produces its own output (plots, tables, and summary statistics). To explore your own scenario, adjust the `base_values` dictionary and the distribution parameters near the top of the relevant cells, then re-run.

GitHub renders `.ipynb` files directly in the browser, so reviewers can read the full analysis — code, plots, and outputs — without running anything locally.

---

## Results and Findings

The analysis is highly consistent across every method applied:

- **Demand dominates.** Daily customer traffic and average spend per customer are by far the most influential variables. In the tornado plot, each shifts the 24-month balance by roughly **±€35,000** for a 20% change. In the global sensitivity analysis, customer traffic explains **~49.5%** of output variance and average spend **~40.8%** — together over 90% — while every other variable explains under 1%.

- **Cost-side factors set the range, not the direction.** Rent, raw material cost, and initial investment affect the spread of possible outcomes but not whether the business is fundamentally viable.

- **The business looks viable under most realistic conditions.** The Monte Carlo simulation produces a mean 24-month balance of roughly **€114,000** with a very low probability of loss. The optimistic scenario (70 customers/day, €10 spend) reaches ~€253k; the pessimistic scenario (35 customers/day, €6 spend) still lands around ~€12k.

- **Decision variables matter only once demand exists.** Location and staffing influence results meaningfully only after sufficient customer traffic is established.

**Conclusion:** Financial viability depends primarily on building a reliable stream of customers and ensuring adequate per-customer spend. Operational and cost decisions improve performance but do not determine the overall trajectory. The two sensitivity methods (Sobol and regional) broadly agree on the variable ranking, reinforcing confidence in the demand-driven conclusion.

---

## Limitations

- The model assumes **fixed working days per month** and treats revenue as a simple product of traffic, spend, and days — it does not model seasonality, day-of-week effects, or ramp-up of customer numbers over time.
- Operating costs are simplified to **rent + raw materials as a % of sales**; staffing/wages, utilities, marketing, taxes, and loan financing are not separately modeled in the core formula.
- Base values and ranges are drawn from **secondary market sources** rather than primary quotes, so they are estimates.
- The Sobol indices use a **simple binning approximation** rather than a full Saltelli implementation; interaction effects are not separately quantified.
- Distribution parameters (e.g., the beta shape and lognormal fit) are assumed rather than fitted to real café data.

---

## Future Improvements

- Introduce **time dynamics**: a customer-acquisition ramp, seasonality, and month-by-month balance trajectories rather than a flat monthly average.
- Add a fuller **cost structure** (labour, utilities, marketing, financing) for a more realistic outflow model.
- Replace the binning Sobol approach with the **Saltelli implementation** (e.g., via `SALib`) to capture interaction effects.
- Calibrate distributions against **real or primary data** if available.
- Add a small **interactive dashboard** (e.g., Streamlit) so non-technical readers can adjust inputs and see outcomes live.

---

## Contributors

- **Tatiana Nanette Tongwa**
- **Maite Arrieta Soriano**

Course: *A220A6501 – System Dynamics with Applications*, LUT Business School (instructor: Mariia Kozlova).

---

## License

MIT Licence
