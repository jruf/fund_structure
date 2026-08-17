# Growth and Fund Structures

Replication code for the empirical analysis in

**Constantinos Kardaras, Hyeng Keun Koo, and Johannes Ruf,  
*Growth and Fund Structures*.**

This repository contains the empirical implementation used in the paper, together with a fully public implementation based on Fama--French data.

## Notebooks

### `fund_structure.ipynb`

This is the **main empirical analysis reported in the paper**.

It uses daily data obtained through WRDS:

- the CRSP S&P 500 value-weighted total return series, including dividends;
- the daily Fama--French risk-free rate.

Access to WRDS and the Python package `wrds` are required to run this notebook.

### `fund_structure_public.ipynb`

This is a **fully public implementation** of the empirical analysis using the daily Fama--French market and risk-free returns.

No WRDS or CRSP subscription is required. The data are downloaded directly from Kenneth French's public data library.

The Fama--French market portfolio represents a broader US equity portfolio than the CRSP S&P 500 series used in the paper. The results in this notebook should therefore be interpreted as a **public-data robustness check rather than an exact replication of the CRSP results**.

Apart from the data source, the notebook applies the same estimation, shrinkage, portfolio construction, and performance calculations as the main empirical analysis.

## Empirical implementation

Let $r_t$ denote the daily discounted simple return of the market portfolio. For a total market return $R^m_t$ and risk-free return $R^f_t$, it is constructed as

```math
r_t = \frac{1+R^m_t}{1+R^f_t}-1.
```

The daily empirical counterparts of the continuous-time return and quadratic-variation processes are

```math
R_t = \sum_{s\leq t} r_s,
\qquad
C_t = \sum_{s\leq t} r_s^2.
```

Under the market-index fund structure studied in the paper, the estimated growth-optimal market exposure is

```math
\widehat{\theta}_t = \frac{R_t}{C_t}.
```

The shrunk exposure is

```math
a_t\widehat{\theta}_t,
```

where the shrinkage factor is calculated from

```math
\psi_t = \frac{27}{8}\widehat{\theta}_t^2 C_t
```

using the shrinkage formula derived in the paper.

Portfolio positions estimated at date $t$ are applied to the following daily return. Hence the implementation does not use future information.

Portfolio wealth is compounded exactly from daily discounted simple returns. The continuous-time growth process $F$ is represented by its daily empirical approximation

```math
F_t
=
\frac{1}{2}
\sum_{s\leq t}
\widehat{\theta}_{s-1}^{\,2} r_s^2.
```

## Sample

The underlying daily data begin in July 1926.

The empirical quantities $R$ and $C$ accumulate information from the beginning of the available sample. The figures and performance evaluation start after the first 1,000 observations, on **12 November 1929**.

To reproduce the sample used in the paper, both notebooks impose the cutoff

```text
31 December 2025
```

before constructing the reported results.

## Portfolio implementation

Three portfolios are considered:

- **Market:** unit exposure to the market portfolio;
- **Unshrunk:** exposure $\widehat{\theta}$;
- **Shrunk:** exposure $a\widehat{\theta}$.

The strategies are rebalanced daily.

The empirical implementation does **not** impose a leverage constraint, does not truncate extreme observations, and does not deduct transaction costs.

The main notebook verifies that all realised daily gross wealth factors of the estimated portfolios remain positive over the evaluation period.

## Performance statistics

For the market, unshrunk, and shrunk portfolios, the notebooks report:

- annualised discounted log growth;
- nominal compound annual growth rate (CAGR);
- annualised volatility;
- Sharpe ratio;
- maximum drawdown of discounted wealth;
- terminal discounted wealth;
- mean absolute market exposure;
- annualised one-way turnover;
- the terminal realised difference between log wealth and $F$.

Returns and wealth are discounted unless stated otherwise. The nominal CAGR is computed from nominal wealth obtained by reinvesting at the risk-free rate.

CAGR and annualised log growth use elapsed calendar time. Volatility, Sharpe ratios, and turnover use 252 trading days per year.

One-way turnover accounts for the change in the existing market exposure caused by the daily market return before rebalancing to the new target exposure.

## Discretisation diagnostics

The main notebook also reports diagnostics for interpreting the daily implementation through the continuous-time growth identities.

For each estimated portfolio, it compares exact daily log-wealth increments,

```math
\log(1+\pi_{t-1}r_t),
```

with the corresponding quadratic approximation,

```math
\pi_{t-1}r_t
-
\frac{1}{2}(\pi_{t-1}r_t)^2.
```

The notebook reports:

- the terminal discrepancy between the two calculations;
- the largest one-day discrepancy and its date;
- the minimum realised daily gross wealth factor and its date;
- the minimum and maximum market exposures.

These diagnostics make explicit that the continuous-time processes $R$, $C$, and $F$ are implemented using daily empirical approximations, while portfolio wealth itself is compounded exactly from daily simple returns.

## Requirements

The notebooks use Python 3 and the following packages:

```text
numpy
pandas
matplotlib
seaborn
```

The main CRSP notebook additionally requires

```text
wrds
```

The public notebook does not require `wrds`.

## Running the main CRSP notebook

To run

```text
fund_structure.ipynb
```

you need a WRDS account with access to the relevant CRSP data.

Set your WRDS username near the beginning of the notebook:

```python
WRDS_LOGIN = 'your_wrds_username'
```

The main figure is saved in the `plots` directory.

## Running the fully public notebook

To run

```text
fund_structure_public.ipynb
```

no subscription or login is required.

The notebook downloads the daily Fama--French research factors directly from Kenneth French's public data library.

The total market return is reconstructed from the market excess return and the risk-free rate as

```math
R^m_t = (R^m_t-R^f_t)+R^f_t.
```

The exact discounted daily simple return is then constructed as

```math
r_t = \frac{1+R^m_t}{1+R^f_t}-1,
```

after which the same empirical procedure as in the main notebook is applied.

Running the notebook from top to bottom produces the corresponding public-data figure and performance statistics.

An internet connection is required for the initial data download.
