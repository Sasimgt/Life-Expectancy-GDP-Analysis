# Investigating the Relationship Between GDP and Life Expectancy

## Introduction

Economic development and public health are often closely related. Countries with stronger economies tend to have better healthcare systems, improved infrastructure, and broader access to essential services. Because of this connection, it is reasonable to ask whether economic growth is associated with longer life expectancy.

In this project, I analyzed data from the World Health Organization (WHO) and the World Bank to explore the relationship between Gross Domestic Product (GDP) and life expectancy across six countries. The goal was to understand whether countries with higher economic output also tend to have populations that live longer.

Using Python and common data science libraries including Pandas, NumPy, Matplotlib, and Seaborn, I explored the dataset through data cleaning, exploratory analysis, and multiple visualizations. These visualizations help highlight patterns between economic growth and population health over time.

The main research question guiding this analysis is:

**Is there a correlation between a country's GDP and the life expectancy of its population??**

By analyzing trends across multiple countries and years, this project aims to provide a clearer picture of how economic development may relate to global health outcomes.

## Why These Visualizations Matter

The goal of this project is to understand whether there is a meaningful relationship between GDP and life expectancy. Since no single chart can answer that completely, I used multiple visualizations to look at the question from different angles.

The first step was exploring the dataset itself. This helped confirm which countries and years were included, what the key columns were, and whether the data looked clean enough to analyze.

The line plots for life expectancy and GDP show how both variables change over time. Together, they provide the foundation for the project: one captures health outcomes, and the other captures economic growth. These trends make it easier to compare how countries develop across the same period.

The scatter plot is one of the most important visuals because it directly compares GDP and life expectancy. I used a log transformation for GDP to make the chart easier to read, since raw GDP values differ so much across countries. This helps reveal the overall relationship more clearly.

The regression plot and correlation analysis add statistical support to the visual findings. The regression line highlights the general upward trend, while the correlation gives a numerical summary of how strongly GDP and life expectancy move together.

The bar chart of average life expectancy makes country-level comparison simple and direct. Instead of looking year by year, it shows which countries maintain higher or lower life expectancy overall.

The histogram and violin plot focus on distribution. The histogram shows how life expectancy values are spread across the full dataset, while the violin plot breaks that distribution down by country. This makes it easier to see variation, concentration, and consistency in health outcomes.

Finally, the facet plots provide a more detailed country-by-country view. By separating the charts into individual panels, they make it easier to study each country’s GDP trend and the relationship between GDP and life expectancy without the visual clutter of combining everything in one chart.

Taken together, these visualizations make the analysis more complete. They do not all show the same thing. Instead, each one adds a different piece of evidence, helping build a clearer and more convincing answer to the project’s main question.

## Tools Used

The analysis was conducted using the following Python libraries:

- **Pandas** for data manipulation
- **NumPy** for numerical transformations
- **Matplotlib** for visualization
- **Seaborn** for statistical plotting

These tools enabled efficient data loading, exploration, visualization, and statistical analysis.

## Loading and Exploring the Dataset

The first step was to load the dataset and inspect its structure.

````python
import os
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
import numpy as np
from matplotlib.ticker import FuncFormatter

# Create folder for graphs
os.makedirs("images", exist_ok=True)

# Load dataset
df = pd.read_csv("all_data.csv")

print(df.head())
print(df.info())
print(df.describe())

print(df.Country.unique())
print(df.Year.unique())
print(df.columns)

print(df.groupby('Country')[['GDP', 'Life Expectancy']]
      .mean()
      .sort_values(by='GDP', ascending=False))

print(df[df['Country'] == 'China'])

This initial exploration confirmed:
- Six countries are present in the dataset
- Data spans multiple years
- Key variables include GDP and Life Expectancy

## Life Expectancy Over Time

To understand how life expectancy changed over time, I created a line plot using Seaborn.

```python
plt.figure(figsize=(10,6))

sns.lineplot(
    data=df,
    x='Year',
    y='Life Expectancy',
    hue='Country',
    marker='o'
)

plt.title("Life Expectancy Over Time")
plt.ylabel("Life Expectancy (years)")
plt.xlabel("Year")
plt.legend(title="Country")

plt.tight_layout()
plt.savefig("images/life_expectancy_over_time.png", dpi=300)
plt.show()
plt.clf()

### Figure 1 — Life Expectancy Over Time
![Life Expectancy Over Time](images/life_expectancy_over_time.png)

### Observations
- Germany, Chile, and the United States maintain high life expectancy.
- Zimbabwe begins with significantly lower life expectancy.
- Zimbabwe also shows noticeable improvement over time.

### Interpretation
Life expectancy tends to improve slowly as healthcare systems evolve, living conditions improve, and public health initiatives become more effective.

Zimbabwe’s pattern is particularly notable because it shows recovery from a low starting point, suggesting significant health improvements during the observed period.

---

## GDP Growth Over Time
Next, I analyzed GDP trends for each country.

Because GDP values are extremely large, the y-axis was formatted in trillions of dollars.

```python
def trillions(x, pos):
    return '$%1.1fT' % (x * 1e-12)

formatter = FuncFormatter(trillions)

f, ax = plt.subplots(figsize=(10,6))

sns.lineplot(
    data=df,
    x='Year',
    y='GDP',
    hue='Country',
    marker='o',
    ax=ax
)

plt.xticks(rotation=70)
ax.yaxis.set_major_formatter(formatter)

plt.title("GDP Over Time")
plt.ylabel("GDP in Trillions of U.S. Dollars")
plt.xlabel("Year")
plt.legend(title="Country")

plt.tight_layout()
plt.savefig("images/gdp_over_time.png", dpi=300)

plt.show()
plt.clf()
### Figure 2 — GDP Over Time
![GDP Over Time](images/gdp_over_time.png)

### Observations
- The United States maintains the highest GDP.
- China shows the most dramatic GDP growth.
- Chile and Mexico show steady growth.
- Zimbabwe remains the lowest.

### Interpretation
China's economic transformation during the early 2000s is clearly visible in the dataset.

---

## Researching Data Context

### What happened in China that increased GDP so drastically?

One of the strongest patterns in the dataset is China’s rapid GDP growth during the 2000 to 2015 period shown in the visualization. A major reason is China’s long phase of market-oriented reform and export-led industrial expansion, which accelerated its integration into the global economy.

Another important milestone was China’s entry into the World Trade Organization in 2001. WTO membership expanded China’s access to global markets and was followed by policy and regulatory changes that made it easier for trade, manufacturing, and foreign investment to scale.

So, when the visualization shows China’s GDP rising much faster than the other countries, that pattern reflects a real-world combination of economic reforms, export growth, industrialization, urbanization, and global trade expansion.

---

## Relationship Between GDP and Life Expectancy

To explore the relationship between economic output and health outcomes, I created a scatter plot comparing GDP and life expectancy.

Because GDP values vary widely, I applied a logarithmic transformation.

```python
df['log_GDP'] = np.log10(df['GDP'])

plt.figure(figsize=(10,6))

sns.scatterplot(
    data=df,
    x='log_GDP',
    y='Life Expectancy',
    hue='Country',
    marker='o',
    alpha=0.7,
    s=60
)

plt.title("Life Expectancy vs Log10(GDP)")
plt.xlabel("Log10(GDP)")
plt.ylabel("Life Expectancy (years)")
plt.legend(title="Country")

plt.tight_layout()
plt.savefig("images/life_expectancy_vs_log_gdp.png", dpi=300)

plt.show()
plt.clf()
### Figure 3 — Life Expectancy vs Log10(GDP)
![Life Expectancy vs Log10(GDP)](images/life_expectancy_vs_log_gdp.png)

### Observations
- Higher GDP countries tend to have higher life expectancy.
- Zimbabwe appears in the lower GDP and lower life expectancy region.
- Developed economies cluster in the higher life expectancy range.

### Interpretation
The scatter plot suggests a positive relationship between GDP and life expectancy.

Using a logarithmic transformation helps reveal the underlying pattern that might otherwise be hidden due to the large differences in GDP values across countries.
---

## Regression Analysis

To further examine the relationship between GDP and life expectancy, I added a regression line.

```python
plt.figure(figsize=(10,6))

sns.regplot(
    data=df,
    x='log_GDP',
    y='Life Expectancy',
    scatter_kws={'alpha':0.6, 's':60},
    line_kws={'linewidth':2}
)

plt.title("Regression: Life Expectancy vs Log10(GDP)")
plt.xlabel("Log10(GDP)")
plt.ylabel("Life Expectancy (years)")

plt.tight_layout()
plt.savefig("images/regression_life_expectancy_vs_log_gdp.png", dpi=300)

plt.show()
plt.clf()
### Figure 4 — Regression: Life Expectancy vs Log10(GDP)
![Regression: Life Expectancy vs Log10(GDP)](images/regression_life_expectancy_vs_log_gdp.png)

### Interpretation
The upward slope of the regression line indicates a positive relationship between GDP and life expectancy.

While GDP does not directly determine life expectancy, the trend suggests that countries with stronger economies often have conditions that support longer lifespans.
---

## Correlation Analysis

To quantify the relationship, I computed the correlation matrix.

```python
correlation = df[['GDP', 'Life Expectancy']].corr()
print("Correlation Matrix:")
print(correlation)

log_correlation = df[['log_GDP', 'Life Expectancy']].corr()
print("Correlation Matrix with log_GDP:")
print(log_correlation)

###Average Life Expectancy by Country
To compare countries more directly, I calculated the average life expectancy for each country across the dataset.

Next, I calculated the average life expectancy for each country.

```python
avg_life = df.groupby('Country')['Life Expectancy'].mean().sort_values()

plt.figure(figsize=(10,6))

sns.barplot(
    x=avg_life.index,
    y=avg_life.values,
    hue=avg_life.index,
    palette='coolwarm',
    legend=False
)

plt.title("Average Life Expectancy by Country")
plt.xlabel("Country")
plt.ylabel("Average Life Expectancy (years)")

plt.xticks(rotation=45)

plt.tight_layout()
plt.savefig("images/average_life_expectancy_by_country.png", dpi=300)

plt.show()
plt.clf()

### Figure 5 — Average Life Expectancy by Country
![Average Life Expectancy by Country](images/average_life_expectancy_by_country.png)

Germany and Chile show the highest averages, while Zimbabwe remains the lowest.

---

## Distribution of Life Expectancy

Finally, I examined how life expectancy values are distributed.

```python
plt.figure(figsize=(8,6))

sns.histplot(
    df['Life Expectancy'],
    bins=15,
    kde=True
)

plt.title("Distribution of Life Expectancy")
plt.xlabel("Life Expectancy (years)")
plt.ylabel("Count")

plt.tight_layout()
plt.savefig("images/life_expectancy_distribution.png", dpi=300)

plt.show()
plt.clf()
### Figure 6 — Distribution of Life Expectancy
![Distribution of Life Expectancy](images/life_expectancy_distribution.png)

###Observations
Most life expectancy values cluster around the higher range.
A lower tail appears due to countries with lower life expectancy values, especially Zimbabwe.

###Interpretation
The distribution highlights differences in global health outcomes across countries with varying economic development levels.


## Life Expectancy Distribution by Country

To better understand how life expectancy varies across countries, I created a violin plot to visualize the distribution of life expectancy values for each country.

Unlike simple summary charts, violin plots display the full distribution of the data, showing where values are concentrated and how widely they vary.

```python
plt.figure(figsize=(12,6))

sns.violinplot(
    data=df,
    x='Country',
    y='Life Expectancy',
    palette='Set2',
    inner='quartile'
)

plt.title("Life Expectancy Distribution by Country")
plt.xlabel("Country")
plt.ylabel("Life Expectancy (years)")
plt.xticks(rotation=45)

plt.tight_layout()
plt.savefig("images/violin_life_expectancy_by_country.png", dpi=300)

plt.show()
plt.clf()
###Figure 7 — Life Expectancy Distribution by Country
![Life Expectancy Distribution by Country](images/violin_life_expectancy_by_country.png)

###Observations
Germany, Chile, and the United States show higher life expectancy distributions.
Zimbabwe appears noticeably lower and more spread out.

Some countries show tighter distributions, indicating more consistent life expectancy values over time.

###Interpretation
The violin plot shows the distribution and density of life expectancy values for each country. Wider sections indicate where more observations are concentrated, while narrower areas represent fewer observations.

Countries with stronger healthcare systems and higher economic development tend to cluster around higher life expectancy ranges, while countries facing economic or public health challenges display lower and more variable distributions.


## GDP as a Function of Life Expectancy by Country

To examine the relationship between GDP and life expectancy more clearly for each country, I created a facet grid of scatter plots using a log-transformed GDP scale.

Because GDP values vary dramatically across countries, plotting raw GDP makes smaller economies harder to compare. Using `Log10(GDP)` improves readability and makes country-level patterns easier to interpret.

```python
# Create log-transformed GDP column
df['log_GDP'] = np.log10(df['GDP'])

g = sns.FacetGrid(
    df,
    col="Country",
    col_wrap=3,
    height=4,
    sharex=False,
    sharey=True
)

g.map_dataframe(
    sns.scatterplot,
    x="Life Expectancy",
    y="log_GDP",
    s=60,
    alpha=0.7
)

g.set_axis_labels("Life Expectancy (years)", "Log10(GDP)")
g.set_titles("{col_name}")

for ax in g.axes.flat:
    ax.tick_params(axis='x', rotation=45)

g.figure.subplots_adjust(top=0.88)
g.figure.suptitle("GDP as a Function of Life Expectancy by Country (Log Scale)")

g.savefig("images/facet_scatter_log_gdp_vs_life_expectancy_by_country.png", dpi=300)
plt.show()
plt.clf()
###Figure 8— GDP as a Function of Life Expectancy by Country (Log Scale)
![GDP as a Function of Life Expectancy by Country (Log Scale)](images/facet_scatter_log_gdp_vs_life_expectancy_by_country.png)


###Observations
Separating the countries into facets makes country-level trends easier to compare.
Countries with higher life expectancy generally also show higher log-transformed GDP values.
Zimbabwe remains in the lower range for both life expectancy and GDP.
China and the United States still stand out economically, but the log scale makes the differences easier to interpret.

###Interpretation
Using Log10(GDP) reduces the visual dominance of very large economies and makes the relative relationship between GDP and life expectancy clearer across all countries. This helps reveal country-specific patterns that may be harder to observe in a single combined scatter plot.



##Facet Grid of Line Graphs Mapping GDP by Country
This visualization shows **each country separately**, which is exactly what Codecademy suggested.

## GDP Growth by Country

To examine economic growth trends more closely, I created a facet grid of line charts showing GDP over time for each country individually.

Facet grids allow us to break a dataset into smaller subplots, making it easier to observe patterns within each country without overlapping lines.

```python
def trillions(x, pos):
    return '$%1.1fT' % (x * 1e-12)

g = sns.FacetGrid(
    df,
    col="Country",
    col_wrap=3,
    height=4,
    sharey=False
)

g.map_dataframe(
    sns.lineplot,
    x="Year",
    y="GDP",
    marker="o"
)

g.set_axis_labels("Year", "GDP in Trillions of U.S. Dollars")
g.set_titles("{col_name}")

years = sorted(df["Year"].unique())[::2]

for ax in g.axes.flat:
    ax.set_xticks(years)
    ax.tick_params(axis='x', rotation=45)
    ax.yaxis.set_major_formatter(FuncFormatter(trillions))

g.figure.subplots_adjust(top=0.88)
g.figure.suptitle("GDP Growth Over Time by Country")

g.savefig("images/facet_gdp_by_country.png", dpi=300)

plt.show()
plt.clf()
###Figure 9— GDP Growth Over Time by Country
![GDP Growth Over Time by Country](images/facet_gdp_by_country.png)

###Observations
China shows the most dramatic GDP growth over the time period.
The United States maintains consistently high GDP values.
Chile and Mexico demonstrate steady economic growth.
Zimbabwe remains significantly lower compared to the other countries.

###Interpretation
By separating each country into its own subplot, the facet grid makes it easier to observe economic growth patterns individually. China’s rapid economic expansion becomes especially clear in this visualization, reflecting its industrialization and global trade integration during the early 2000s.


---

## Key Insights
From this analysis, several important insights emerge:

- GDP and life expectancy show a positive relationship.
- Countries with stronger economies tend to have higher life expectancy.
- China demonstrates the most dramatic GDP growth.
- Zimbabwe consistently shows the lowest GDP and life expectancy.
- Log-transforming GDP improves visualization of the relationship.

---

## Limitations
Although the analysis shows a correlation, several limitations exist:
- Only six countries are included.
- Many other factors influence life expectancy.
- Correlation does not imply causation.

Important additional factors include:
- healthcare access
- education
- sanitation
- public health policy
- social stability

---

## Conclusion
This project explored whether economic development correlates with population longevity.

Both the visualizations and statistical analysis suggest that countries with higher GDP tend to have higher life expectancy. However, the relationship is complex and influenced by multiple social and policy factors beyond economic output.

Through data visualization and exploratory analysis, this project demonstrates how data science can reveal meaningful patterns in global health and economic data.
````
