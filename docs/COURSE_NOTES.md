# Course Notes — Day 80: The Tragic Discovery of Handwashing

## Course context

**Course:** 100 Days of Code — The Complete Python Pro Bootcamp  
**Day:** 80  
**Topics:** Pandas data analysis, Matplotlib / Plotly / Seaborn visualisation, statistical significance testing

---

## Exercise brief

Step into the role of Dr Ignaz Semmelweis, a 19th-century Hungarian physician at Vienna General Hospital. Using real 1840s hospital records, use data to reconstruct and validate his discovery that handwashing dramatically reduced maternal mortality from childbed fever (puerperal fever).

### Key questions to answer

1. How dangerous was childbirth in Vienna in the 1840s?
2. Which maternity clinic (Clinic 1 vs Clinic 2) had a higher death rate, and why?
3. Did mandatory handwashing introduced in June 1846 reduce the monthly death rate?
4. Is the reduction statistically significant (p < 0.01)?

---

## Key concepts covered

### Data exploration
- `.shape`, `.info()`, `.describe()` — understand dataset dimensions and types
- `.duplicated()`, `.isna()` — check data quality
- `pd.to_datetime()` — parse date columns

### Data transformation
- Column arithmetic: `df['pct_deaths'] = df.deaths / df.births`
- Boolean filtering: `df[df.date < cutoff_date]`
- `np.where()` — categorical labelling from conditions
- `.rolling(window=6).mean()` — 6-month rolling average
- `.set_index()` — date-indexed DataFrames

### Visualisation
- Matplotlib twin y-axes (`ax.twinx()`)
- `mdates.YearLocator` / `mdates.MonthLocator` — time axis formatting
- Plotly line charts (`px.line`) — births, deaths, pct_deaths by clinic
- Plotly box plot (`px.box`) — before vs after handwashing distribution
- Plotly histogram (`px.histogram`) with `histnorm='percent'` and KDE marginal
- Seaborn KDE plot (`sns.kdeplot`) with `clip` to prevent negative rates

### Statistical testing
- `scipy.stats.ttest_ind()` — independent samples t-test
- Interpreting p-values: p < 0.01 → 99% confidence the effect is real
- Null hypothesis vs alternative hypothesis framing

---

## Dataset sources

Dr Semmelweis published his research in 1861. The original tables (in German) are archived at:
- Full text: http://www.deutschestextarchiv.de/book/show/semmelweis_kindbettfieber_1861
- English translation (PDF): http://graphics8.nytimes.com/images/blogs/freakonomics/pdf/the%20etiology,%20concept%20and%20prophylaxis%20of%20childbed%20fever.pdf

### Key finding

Handwashing reduced the average monthly death rate from **~10.5%** to **~5%** — a reduction that is statistically significant at p < 0.01, meaning there is less than a 1% probability the improvement was due to chance.
