---
layout: post
title: "Exploring Bigfoot Reports with Altair & Vega-Lite"
tags: [data-visualization, altair, vega-lite, is445]
---

<!-- Buttons for Data and Analysis -->
<p>
  <a class="btn btn-primary" href="https://raw.githubusercontent.com/UIUC-iSchool-DataViz/is445_data/main/bfro_reports_fall2022.csv" target="_blank">
    The Data
  </a>
  &nbsp;
  <a class="btn btn-secondary" href="https://github.com/pratyushagarwal22/pratyushagarwal22.github.io/blob/main/python_notebooks/bigfoot_reports_assignment.ipynb" target="_blank">
    The Analysis
  </a>
</p>

---

## Plot 1 – Bigfoot reports per year

<div id="bigfoot-chart-1"></div>

**What is being visualized**

The first plot shows the total number of Bigfoot (BFRO) reports per calendar year in the Bigfoot reports dataset. By aggregating reports to the yearly level, the chart highlights long-term trends in reported sightings, such as periods of higher or lower activity and whether reports are becoming more or less common over time.

**Design choices – encodings**

I used a line chart with points. The horizontal axis encodes **year** as an ordinal variable (`year:O` on the x-axis) and the vertical axis encodes the **count of reports** (`count` on the y-axis). Each point represents one year, with the line connecting them to emphasize the temporal trend. Tooltips are enabled so that hovering on a point reveals the exact year and number of reports, making it easier to read precise values without cluttering the chart with labels.

**Design choices – color / colormap**

For this plot I did **not** use a multivariate color scheme. All points and the line share a single color. Because the goal is to emphasize the overall time trend (upward or downward movement across years) rather than compare sub-groups, additional hues would add visual noise without adding much meaning. The default single color is sufficient to clearly show the pattern.

**Data transformations**

On the analysis side, I read the data from the course GitHub URL into a pandas DataFrame. I ensured a `year` column existed by parsing the `date` column to a proper timestamp when needed and then extracting the year component. I then grouped the data by `year` and counted the number of rows in each group to compute a yearly report count. The resulting table (year, count) was passed to Altair to generate the line chart.

---

## Plot 2 – Interactive exploration of reports by year and state

<div id="bigfoot-chart-2"></div>

**What is being visualized**

The second visualization is a linked, interactive pair of bar charts. The **top** chart shows the number of reports per year, just like in Plot 1 but in bar form. The **bottom** chart shows the number of reports per state, filtered to only include reports from the years selected in the top chart. This allows you to explore how report counts are distributed across states for different time periods.

**Design choices – encodings**

In the top chart, **year** is encoded on the x-axis as an ordinal variable and the **count of reports** is encoded on the y-axis as a quantitative variable. Each bar represents the total number of reports in that year.

In the bottom chart, **state** is encoded on the y-axis as a nominal variable (sorted by descending count) and the **count of reports** is encoded on the x-axis. This horizontal bar layout makes it easier to read state names and compare their lengths. Additionally, I used color to encode **season** as a nominal variable, which produces stacked bars per state that show how reports are distributed across seasons. Tooltips display the state, season, and the corresponding count when hovering over a bar segment.

**Design choices – color / colormap**

For this plot I used a categorical color scale for the `season` variable. Distinct hues make it easy to distinguish between seasons such as Spring, Summer, Fall, and Winter, and the stacked bars reveal whether certain states report more sightings in particular seasons. A categorical colormap is appropriate here because `season` is nominal, and using multiple colors adds a meaningful extra dimension to the comparison without overwhelming the viewer.

**Data transformations**

The main transformations for this plot were:
- Filtering to rows with non-missing `state` and `year` values.
- Aggregating by year in the top chart (`count()` of rows per year).
- Aggregating by both state and season in the bottom chart via Altair’s `count()` aggregation, restricted to the subset of rows that fall within the selected year interval.
These transformations were done with a mix of pandas (for cleaning and ensuring `year` exists) and Altair’s built-in aggregation and filtering in the chart definitions.

---

## Interactivity – How it helps

Interactivity is provided using an **interval selection** (`alt.selection_interval`) on the x-axis of the top bar chart (year). When you click and drag over a subset of years in the top chart, that selection is stored in a brush and applied as a filter to the bottom chart via `transform_filter(brush)`. As a result, the state-level bar chart updates immediately to show only reports from the selected years.

This interaction makes the visualization much more informative and engaging. Instead of showing one static distribution of reports by state, it lets you dynamically compare different time periods. For example, you can brush over early years to see which states had the most reports historically, then compare to a more recent period to see if the geographic pattern of reports has shifted. The interaction turns a pair of static summaries into an exploratory tool for understanding temporal and spatial patterns in the data.

---

## Technical details – embedding the Altair JSON

The Altair charts were exported from the notebook as Vega-Lite JSON files and saved under `assets/json/` in this repository:

- Plot 1 JSON: `assets/json/bigfoot_year_counts.json`
- Plot 2 JSON: `assets/json/bigfoot_year_state_interactive.json`

Below, I embed them using Vega-Embed from a CDN and the local JSON files generated in the notebook.

<script src="https://cdn.jsdelivr.net/npm/vega@5"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-lite@5"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-embed@6"></script>

<script type="text/javascript">
  // Plot 1
  const spec1Url = "{{ '/assets/json/bigfoot_year_counts.json' | relative_url }}";
  vegaEmbed('#bigfoot-chart-1', spec1Url, {actions: false}).catch(console.error);

  // Plot 2
  const spec2Url = "{{ '/assets/json/bigfoot_year_state_interactive.json' | relative_url }}";
  vegaEmbed('#bigfoot-chart-2', spec2Url, {actions: false}).catch(console.error);
</script>
