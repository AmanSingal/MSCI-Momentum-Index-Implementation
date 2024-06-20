# MSCI-Momentum-Index-Implementation
## Assumptions & Procedures Followed for implementation:-

Most of the variable names are in accordance with the research paper given here - [MSCI Momentum Indexes Methodology, Aug 2021](https://www.msci.com/eqb/methodology/meth_docs/MSCI_Momentum_Indexes_Methodology_Aug2021.pdf). The data is obtained from NSE-bhavcopy and the analysis is based on that only.

The data files are stored in respective year directories (5-yr data is present). Each file has the following columns:

- **SYMBOL**: The ticker symbol for the security. For example, "EICHERMOTORS".
- **SERIES**: Indicates the series to which the security belongs. Here, it is "EQ" which stands for Equity.
- **OPEN**: The opening price of the security for the trading day.
- **HIGH**: The highest price at which the security traded during the day.
- **LOW**: The lowest price at which the security traded during the day.
- **CLOSE**: The closing price of the security for the trading day.
- **LAST**: The last traded price of the security for the day.
- **PREVCLOSE**: The closing price of the security for the previous trading day.
- **TOTTRDQTY**: The total quantity of shares traded during the day.
- **TOTTRDVAL**: The total traded value in monetary terms for the day.
- **TIMESTAMP**: The date of the trading record.
- **TOTALTRADES**: The total number of trades executed for the security on that day.
- **ISIN**: The International Securities Identification Number for the security.

Only instruments belonging to the 'EQ' (Equity) series are taken as those are more liquid (more than 2.3k such instruments were present in the dataset).

Weekly returns are calculated starting from the 2nd week of Jan '18 to the last week of Dec '22 for each instrument for the whole of the time-period of the data (for the 5-yr period).

To calculate weekly returns, the last market open day for each week is discovered and used to fetch closing prices for any particular instrument. The weekly returns are then saved into a CSV file (`weekly_return_values.csv`).

The rebalancing date (when the index is created) is taken to be the 1st week of Feb '21 as by then we have annualized weekly return values which require past 3-yr data. Therefore, according to the research paper, our T-1 date would be the 1st week of Jan '21, and the methodology aims to construct the index with an aim to attain a high exposure to the Momentum factor while maintaining sufficient index market capitalization and number of securities coverage in the period of 2020-2021.

A dataframe, named `final_df_one`, is maintained for further computations. It is gradually built starting from computing 6 & 12-month price momentum for stocks from their past 1-month, 7-month, and 13-month data files. Only stocks that had at least 6-mo price momentum data (more than 1.3k securities) were taken as per criteria mentioned in the paper.

Thereafter, the annualized standard deviation of weekly returns is computed, and then risk-adjusted price momentum values for 6-mo & 12-mo are calculated. These values are then standardized to 6-mo & 12-mo Z-score values, which are appropriately weighted (0.5, 0.5) to calculate C-values.

The C-values are then standardized into Z-scores which are then winsorized to bounds of +/- 3, and then the final momentum score is computed.

The final rankings of stocks are based on these momentum scores. If multiple securities have the same momentum score, then preference is given to the one which has a larger Total Traded Value for that particular day (last market open day of the 1st week of Jan '21). This closely resembles the procedure stated in the paper which mentions giving preference according to the constituent's weight in the parent index.

The paper also states that rebalancing is done around semi-annually. Though I've done it for one instance, the same code can be run for a different starting date. My data limits this to 4-times (2-times in a year and years available are 2021 & 2022).

The risk-free rate is taken to be 0, which can also be customized based on actual market rates during that time.

The number of securities to be selected for this momentum index creation is dependent on free-float data which I didn't have, therefore I've given a complete ranking of securities present in the parent index.
