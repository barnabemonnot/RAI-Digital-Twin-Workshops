
# Challenge 1 - Navigating the RAI cadCAD Op-DT model


- (*CONCEPTUAL*) **What are the advantages and disadvantages of breaking down the entire workflow on discrete steps of preparing -> backtesting -> identification -> prediction -> output? Could it be made differently?**

A modular design of an Op-DT is key as tt is relatively easy to maintain a divisible data science workflow configuration. In PLM (Product Lifecycle Management) in general, integrating the digital twin within a modular design can be very advantageous, especially when there are various people, data & processes involved. I think BlockScience is doing awesome efforts giving the first steps towards a Modularized Digital Twin Framework to ease the creation of Op-DT on cryptoeconomic protocols/projects

Following, I list some advantages/disadvantages of of a modular design

* *Advantages*
    - Modular ETL processes, data analytics, system parameters estimation & reporting give us the freedom to perform quantification analysis on each focus area
    - Plug-in different data sources, tools & frameworks
    - Possiblity to reuse existing modules into other projects (e.g.ETL for downloading historical prices on any pairs from a DEX)
    - Abstract out Logic to Improve Readability

* *Disadvantages*
    - Would not provide any benefits if you don't provide data types checking & establish standard ways to forward information between modules


- (*HANDS-ON*) **Can you execute the RAI Digital Twin through the CLI? Does the output matches the one that we've seen on the slides?**

* When using the default parameters from the README, the model produced different results to as the ones shown during the last session, mainly because it uses the `-l` flag, that loads data extracted from the last simulation performed (in this case June 2021). Moreover, the extrapolation timestemps is shorter than default (7*24)

* In order to try to reproduce those results, you need to use `-p 14` (how many days from today you want to go in history) and set the `historical_lag` parameter on the [extrapolation_cycle](https://github.com/reflexer-labs/reflexer-digital-twin/blob/master/rai_digital_twin/execution_logic.py#L205) function from default (0) to the No of days from Nov 1st (9 days on the day I run the simulations). However, you'll notice that [extrapolation results](reproducing_results.html) (future outcomes) are slightly different, which may be due to some stochastic processes involved during the entire workflow including the cadCAD simulation.

- (*HANDS-ON*) **What happens if you use different CLI arguments?**
    - **For instance, what happens if extrapolation is done for 2 months rather than 2 weeks?**
        - Given that Hours is the timestemps unit for the model, executin a two-month extrapolation compared to the default (7 days -> 7*24) makes the simulation execution time to increase (Exec time: ~26min: `Entire Simulation: (Models, Unique Timesteps, Params, Total Runs, Sub-States) = (1, 1440, 14, 40, 8)`) as it produced a wider view of the future value of the different system parameters & KPIs. [Results](reproducing_results.html) showed that redemption price & rate tends to become a bit more unstable at the end of the extrapolation period. Example command: `python -m rai_digital_twin -p 14 -e 1440` (60*24 -> 1440)
    - **Or if the past days to be downloaded are 3 days rather than 14 days?**
        - `Past days` parameter is tied to the `historical_interval` which sets the time in the past (in days) where we want to start collecting data for backtesting, extrapolation & finally prediction. By setting its value to 3 days, you'll get the following error: `numpy.linalg.LinAlgError: SVD did not converge in Linear Least Squares`. Seems there are some NaN on the input parameters sent to a VAR model fitting task that is called when the `p_user_action` policy from `token_state` is called. A deeper investigation on the issue made me realize that the No of extrapolation timesteps (-e) should be at most half of the days set in `historical_interval` . Results can be seen [here](3past_days_extrapolation.html). Example command: `python -m rai_digital_twin -p 3 -e 36` (1.5 * 24 -> 36)


## Mapping Op-DT execution modules & stack

### Parameters

- Interval for retrieving and backtesting data
    - historical_interval: Days = 14
    - historical_lag: Days = 9

- Extrapolation
    - price_samples: int = 10
    - extrapolation_samples: int = 1
    - extrapolation_timesteps: int = 7 * 24

- Number of Monte Carlo runs for the USD/ETH price
    - Looks like right this value cannot be changed through the CLI. It it set to 1 by default when calling cadCAD `easy_run`

### Modules

- Retrieve Data: uses the RAI mainnet subgraph to retrieve all historical data (by block) required for backtesting & extrapolation
    * hourly stats
    * systemState
    * safes history

- Prepare Data:
    * load_backtesting_data: format data to provide a state on a block basis for the token (reserve, debt & eth locked) PID Controller (redemption price & rate, Proportional & Integral errors). Also extract exogenous events (like eth price & market price in USD)
    * load_governance_events: loads the governance params (kp,ki,leaky_factor,period) as a series of events (timeseries) based on block number. See [Controller Params](https://github.com/reflexer-labs/reflexer-digital-twin/blob/master/data/controller_params.csv)

- Backtest Model: prepares cadCAD default model (`rai_digital_twin.models.digital_twin_v1`) and feeds it with params and initial state. At th end of the simulation, a backtesting loss is calculated using the gathered simulation data and historical data. Loss is based on the average absolute error on redemption price & rate

- Stochastic Fit: uses exogenous event data to estimate the value of ETH by performing Bayesian Inference

- Extrapolation: is performed in two steps: 1) Extrapolate Signals (Eth price) using obtained stochastic params, and 2) Extrapolate future data by running cadCAD model with extrapolated signals, backtesting results & governance events


### Data Science stack

- cadCAD (definion & execution of the digital twin)
- cadCAD Tools (great utils like `prepare_params` & `easy_run`)
- pymc3 (for performing Bayesian statistical modelling & probabilistic ML)
- Pandas (for ETL tasks)
