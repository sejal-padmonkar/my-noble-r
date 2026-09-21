# World Development Indicators RDS data description

This document describes the dataset stored in the RDS file `worlddevrds`, which was loaded in R as a `data.frame`.

## 1. What the dataset contains

The data represents a longitudinal panel of World Bank development indicators. Each row corresponds to one country and one indicator, and the columns store that indicator’s value for each year from 1960 through 2025.

The file was inspected directly with R:

```r
x <- readRDS("worlddevrds")
class(x)
str(x)
```

The object is a `data.frame` with:

- 396,970 observations (rows)
- 70 variables (columns)
- 265 unique country names
- 1498 unique indicator names

## 2. Structural layout

The column layout is:

1. `Country Name` — text name of the country or region
2. `Country Code` — short World Bank country code such as `AFE`, `USA`, `BRA`
3. `Indicator Name` — descriptive name of the indicator
4. `Indicator Code` — World Bank code identifier such as `EG.CFT.ACCS.ZS`
5. Annual value columns for each year from 1960 to 2025

In other words, the table is shaped like this:

```text
Country Name | Country Code | Indicator Name | Indicator Code | 1960 | 1961 | ... | 2025
--------------------------------------------------------------------------------------
...          | ...          | ...            | ...            | ...  | ...  | ... | ...
```

## 3. Example of row structure

A small sample of the first rows looks like this:

```text
Country Name                     Country Code  Indicator Name                                              Indicator Code  1960  1961  1962  1963
Africa Eastern and Southern     AFE           Access to clean fuels and technologies for cooking (% of population) EG.CFT.ACCS.ZS  NA    NA    NA    NA
Africa Eastern and Southern     AFE           Access to clean fuels and technologies for cooking, rural (% of rural population) EG.CFT.ACCS.RU.ZS NA NA NA NA
Africa Eastern and Southern     AFE           Access to clean fuels and technologies for cooking, urban (% of urban population) EG.CFT.ACCS.UR.ZS NA NA NA NA
```

This shows the repeated pattern:

- same country repeated for different indicators
- each indicator measured through time across the year columns
- missing values (`NA`) for years where the indicator was not available

## 4. Data types

The dataset has a mix of data types:

| Column group | Type | Meaning |
| --- | --- | --- |
| `Country Name` | character | Country or region label |
| `Country Code` | character | World Bank code |
| `Indicator Name` | character | Human-readable metric name |
| `Indicator Code` | character | Machine-readable metric code |
| `1960` to `2025` | numeric | Indicator values for each year |

The yearly columns are numeric, and many cells are `NA` because not every country-indicator pair has data for every year.

## 5. Coverage and cardinality

The dataset spans the years 1960 through 2025, and includes:

- 265 countries or country aggregates
- 1498 distinct indicators
- a dense but incomplete matrix of annual data

The number of non-missing observations by year increases over time, which is typical of a World Bank-style time series archive with better reporting in later decades.

Example count of non-missing cell values by year:

```text
1960: 37,226
1970: 72,137
1980: 94,123
1990: 128,978
2000: 193,690
2010: 223,453
2020: 217,006
2025: 81,671
```

This demonstrates that the dataset is widest in the 2000s and 2010s, with sparse coverage in early years and a drop in most recent projections or reporting completeness.

## 6. Interpretation

This is not a tidy long-format table; it is a wide table where:

- each row = one `country × indicator`
- each column = one year
- the values are the annual metric measurements

This structure is convenient for quick comparison across years, but it is less convenient for modeling or reshaping into tidy formats because the year dimension is represented as columns instead of rows.

## 7. R code to inspect it

```r
d <- readRDS("worlddevrds")

nrow(d)
ncol(d)
length(unique(d[["Country Name"]]))
length(unique(d[["Indicator Name"]]))

head(d[, c("Country Name", "Country Code", "Indicator Name", "Indicator Code", "2000", "2010", "2020")])
```

## 8. Summary

The RDS file is a country-by-indicator annual dataset from the World Bank’s development indicators, stored as a wide `data.frame` in R. It contains a large number of economic, social, and environmental indicators across many countries and years, with a repeated row pattern for each country/indicator combination and year-specific numeric values.
