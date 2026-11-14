# NIFTY Call Option Pricing and Delta Hedging

A computational finance mini-project on pricing and hedging European call options on the NIFTY index using market data. The project combines Black–Scholes pricing, numerical implied-volatility estimation, spot–futures parity, dynamic delta hedging, and Monte Carlo experiments to study hedging error and tail risk.

## Project Objectives

- Estimate implied volatility from observed option prices using the Black–Scholes model and bisection root-finding.
- Infer the implied net cost of carry and dividend yield from spot and futures prices.
- Construct a self-financing delta-hedged portfolio and track its hedging error through expiry.
- Use Monte Carlo simulation to examine how rebalancing frequency affects hedging error, and compare hedged and unhedged risk.

## Methodology

### 1. Market-data preparation

The data contains daily NIFTY spot prices, futures prices, and European call-option market prices for the 27 March 2025 expiry. Time to maturity is calculated using a 365-day convention:

\[
\tau = \frac{T-t}{365}.
\]

`process_data.ipynb` demonstrates how the option and futures files can be filtered by strike and merged on symbol, date, and expiry. The prepared datasets are supplied as CSV files (see [Data files](#data-files)).

### 2. Black–Scholes pricing and implied volatility

The notebook implements Black–Scholes pricing for European calls and puts, with a continuous dividend yield. Implied volatility is obtained by solving

\[
C_{BS}(S,K,\tau,r,\hat{\sigma},q) = C_{market}
\]

using the bisection method. The search interval is `[0.0001, 3.0]` and the numerical tolerance is `1e-6`. The resulting volatility estimates are checked by repricing the option and comparing the model price with the observed market price.

### 3. Implied carry and dividend yield

Using spot–futures parity,

\[
F(t) = S(t)e^{(r-q)\tau},
\]

the implied net carry is estimated as

\[
r-q = \frac{\log(F(t)/S(t))}{\tau}.
\]

Given a risk-free-rate proxy \(r\), the implied dividend yield is then calculated as \(q=r-(r-q)\). The notebook plots the estimated net carry and dividend yield over time.

### 4. Historical delta-hedging strategy

The project computes Black–Scholes delta and uses it to rebalance a portfolio consisting of the underlying index and a cash account. The cash account accrues at the assumed risk-free rate, and the historical hedging calculation includes estimated dividend income. The portfolio value is compared with the option market price during the life of the option and with its intrinsic payoff at expiry. Daily and cumulative hedging errors are plotted.

### 5. Monte Carlo hedging experiment

Risk-neutral underlying-price paths are simulated using geometric Brownian motion (GBM):

\[
dS_t = (r-q)S_t\,dt + \sigma S_t\,dW_t.
\]

The delta-hedging strategy is applied to 10,000 simulated paths. Daily, half-daily, and hourly rebalancing frequencies are compared using the distribution of terminal hedging errors. The analysis also compares hedged and unhedged short-option P&L and calculates Value at Risk (VaR) and Expected Shortfall (ES) at 95% and 99% confidence levels. A separate experiment studies the effect of a 0.1% proportional transaction cost on hedging errors.

## Repository Structure

```text
.
├── Project_Code.ipynb   # Main pricing, hedging, and Monte Carlo analysis
├── process_data.ipynb   # Option/futures data preparation
├── output.csv           # Prepared dataset, strike 21,800
├── output_atm.csv       # Prepared dataset, strike 23,650
└── output_otm.csv       # Prepared dataset, strike 25,300
```

Each supplied CSV contains 18 dated observations for the 27 March 2025 expiry and includes:

- `Date`
- `Spot Price`
- `Futures Price`
- `Option Market Price`
- `Strike Price`
- `Expiry Date`
- `Time to Maturity`

## Setup and Usage

### Requirements

- Python 3
- NumPy
- pandas
- SciPy
- Matplotlib
- Jupyter Notebook or JupyterLab

Install the main dependencies:

```bash
python -m pip install numpy pandas scipy matplotlib jupyter
```

### Run the analysis

1. Clone or download this repository.
2. Install the dependencies listed above.
3. Launch Jupyter:

   ```bash
   jupyter notebook
   ```

4. Open `Project_Code.ipynb` and run the cells in order.
5. The main notebook uses `output.csv` by default. To analyse another strike, change the `data_path` variable near the beginning of the notebook to `output_atm.csv` or `output_otm.csv`.

### Regenerating the prepared data (optional)

`process_data.ipynb` reads raw option and futures CSV files from the local `Options Data/NIFTY_27-Mar-2025/` directory. Update the file paths in that notebook to match your local data layout before running it. The strike is selected with the `k` variable; the supplied notebook currently sets `k = 25300` and writes `output_otm.csv`.

## Modelling Assumptions and Parameters

- Options are treated as European-style contracts and priced with the Black–Scholes framework.
- Time to maturity is annualised using 365 days.
- The historical implied-volatility and spot–futures calculations use a 6% annual risk-free-rate proxy in the corresponding notebook sections.
- The Monte Carlo experiment uses a 7% risk-free-rate proxy and estimates the dividend-yield input from the median observed implied net carry.
- GBM paths are simulated under the risk-neutral measure with 10,000 paths and a fixed random seed (`42`).
- The baseline Monte Carlo hedge excludes transaction costs; the additional transaction-cost experiment uses a 0.1% proportional cost.

These are modelling choices for the project analysis, not calibrated trading recommendations. Results depend on the selected strike, market-data sample, rate assumptions, volatility estimate, and rebalancing convention.

## Tools

Python, NumPy, pandas, SciPy, Matplotlib, and Jupyter Notebook.

## Contributions

The project was completed collaboratively, with the following division of work:

- **Varivashya Poladi:** Data collection and market setup; implied-volatility estimation; inference of the implied funding rate and dividend yield; and the Monte Carlo hedging experiment.
- **Shivey Ravi Guttal:** Construction of the delta-hedged portfolio and analysis of hedging errors.
- **Fale Sanyat Vinod:** Implied-volatility estimation; inference of the implied funding rate and dividend yield; and the Monte Carlo hedging experiment.

---

*Academic mini-project; not financial advice or a production trading system.*
