# PA4_ECE2112_JAMES, KE
---

### Experiment 4: Data Wrangling and Data Visualization
**Submitted by:** James, Kim Ezekiel G.| 2ECE-A | 09/09/2026

This notebook works with a board-exam-style student dataset (`board2.xlsx`) using `pandas` for data wrangling and `matplotlib` for visualization. It covers filtering data into targeted DataFrames, computing group averages, and building a summary bar-chart figure.
---

## 📋 Table of Contents

- [Overview](#-overview)
- [Method Summary](#-method-summary)
- [Problem Specifications & Solutions](#-problem-specifications--solutions)
  - [Part A: Visayas Communication DataFrame](#part-a-visayas-communication-dataframe)
  - [Part B: Visayas Female DataFrame](#part-b-visayas-female-dataframe)
  - [Part C: Category-Average Visualization](#part-c-category-average-visualization)
- [Project File Structure](#-project-file-structure)
- [Prerequisites & Requirements](#-prerequisites--requirements)
- [How to Run](#-how-to-run)
  - [Using Terminal / Command Prompt](#using-terminal--command-prompt)
  - [Using Jupyter Notebook](#using-jupyter-notebook)
- [Key Constraints & Edge Cases Handled](#-key-constraints--edge-cases-handled)

---

## 📌 Overview

This repository contains Python solutions for **Experiment 4: Data Wrangling and Data Visualization**. The activity demonstrates DataFrame filtering, column subsetting, groupby aggregation, and bar-chart visualization on the `board2.xlsx` student-scores dataset using **Pandas** and **Matplotlib**, without mutating the underlying raw DataFrame.

---

## ⚙️ Method Summary

| Method / Syntax | Input Parameters | Return Type | Key Logic |
| :--- | :--- | :--- | :--- |
| `df[(cond1) & (cond2)][cols]` | Boolean conditions, target column list | DataFrame | Filters rows on two conditions simultaneously, then subsets to the specified columns. |
| `df[df['col'] >= value]` | Threshold condition on an existing filtered DataFrame | DataFrame | Applies a second, independent filter without overwriting the source DataFrame. |
| `df.groupby('col', as_index=False)['target'].mean()` | Grouping column, target column | DataFrame | Computes the mean of a numeric column for every category of a categorical feature. |
| `pd.concat([...], keys=[...])` | List of DataFrames, list of keys | DataFrame | Stacks multiple summary tables into a single labeled, multi-index DataFrame. |
| `axes[i].bar(x, y)` | Category labels, aggregated values | Matplotlib Axes | Plots a bar chart on a subplot axis within a shared figure. |
| `df.loc[df['col'].idxmax()]` | Column to evaluate | Series | Retrieves the full row corresponding to the maximum value in a column. |

---

## 💻 Problem Specifications & Solutions

### Setup: Load Data and Compute Average

**Requirement:** Load `board2.xlsx` into a DataFrame and add an `Average` column computed from the four subject scores.

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_excel('board2.xlsx')
display(df)

df['Average'] = (df.Math + df.Electronics + df.GEAS + df.Communication) / 4
print("UPDATED DATAFRAME:")
display(df)
```

---

### Part A: Visayas Communication DataFrame

**Requirement:** Create a DataFrame named `VisComm` containing students whose `Hometown` is Visayas and whose `Track` is Communication. Retain only `Name, Gender, Math, Electronics, Average`, in that order. Both filtering conditions must be applied before the columns are selected. Display the DataFrame and its row count.

```python
VisComm = df[
    (df["Hometown"] == "Visayas") &
    (df["Track"] == "Communication")
][["Name", "Gender", "Math", "Electronics", "Average"]]

print("A. VISAYAS COMMUNICATION DATAFRAME")
display(VisComm)

print("Number of rows:", len(VisComm))
```

**Output:** A DataFrame limited to `Name, Gender, Math, Electronics, Average` for students meeting both conditions, followed by a printed row count (values depend on the contents of `board2.xlsx`).

---

### Part B: Visayas Female DataFrame

**Requirement:** Create a second DataFrame named `VisFemale` containing students whose `Hometown` is Visayas and whose `Gender` is Female. Retain only `Name, Track, GEAS, Electronics, Average`. Display `VisFemale`, then display only the rows with `Average >= 60` **without overwriting** `VisFemale`.

```python
VisFemale = df[
    (df["Hometown"] == "Visayas") &
    (df["Gender"] == "Female")
][["Name", "Track", "GEAS", "Electronics", "Average"]]

print("B. VISAYAS FEMALE DATAFRAME")
print("Complete VisFemale DataFrame:")
display(VisFemale)

VisFemale_60 = VisFemale[VisFemale["Average"] >= 60]

print("VisFemale students with Average >= 60:")
display(VisFemale_60)
```

**Output:** The full `VisFemale` DataFrame, followed by a separately named subset (`VisFemale_60`) showing only rows meeting the `Average >= 60` threshold, with the original `VisFemale` left unmodified.

---

### Part C: Category-Average Visualization

**Requirement:** Examine how `Average` differs across `Track`, `Gender`, and `Hometown`.
a. Compute the mean `Average` per category for each feature.
b. Display the three summary tables.
c. Plot one figure with three bar charts (mean `Average` by Track, Gender, Hometown).
d. Identify the category with the highest sample mean per feature.
**Interpretation rule:** Describe the observed dataset only — a difference in group means does not establish causation.

```python
# a. Compute mean Average per category
track_mean = df.groupby("Track", as_index=False)["Average"].mean()
gender_mean = df.groupby("Gender", as_index=False)["Average"].mean()
hometown_mean = df.groupby("Hometown", as_index=False)["Average"].mean()

print("1. MEAN AVERAGE BY TRACK")
display(track_mean)
print("2. MEAN AVERAGE BY GENDER")
display(gender_mean)
print("3. MEAN AVERAGE BY HOMETOWN")
display(hometown_mean)

# b. Display combined summary tables
display(pd.concat([track_mean, gender_mean, hometown_mean],
                   keys=['Track', 'Gender', 'Hometown']))

# c. Plot three bar charts in one figure
fig, axes = plt.subplots(1, 3, figsize=(18, 6))

axes[0].bar(track_mean["Track"].astype(str), track_mean["Average"])
axes[0].set_title("Mean Average by Track")
axes[0].set_xlabel("Track")
axes[0].set_ylabel("Mean Average")
axes[0].tick_params(axis="x", rotation=45)

axes[1].bar(gender_mean["Gender"].astype(str), gender_mean["Average"])
axes[1].set_title("Mean Average by Gender")
axes[1].set_xlabel("Gender")
axes[1].set_ylabel("Mean Average")
axes[1].tick_params(axis="x", rotation=45)

axes[2].bar(hometown_mean["Hometown"].astype(str), hometown_mean["Average"])
axes[2].set_title("Mean Average by Hometown")
axes[2].set_xlabel("Hometown")
axes[2].set_ylabel("Mean Average")
axes[2].tick_params(axis="x", rotation=45)

plt.tight_layout()
plt.show()

# d. Identify highest-mean category per feature
highest_track = track_mean.loc[track_mean["Average"].idxmax()]
highest_gender = gender_mean.loc[gender_mean["Average"].idxmax()]
highest_hometown = hometown_mean.loc[hometown_mean["Average"].idxmax()]

print("Highest Sample Mean")
print("  1. In the Track feature, the highest sample mean is in the category",
      highest_track['Track'], "with an average of", highest_track['Average'])
print("  2. In the Gender feature, the highest sample mean is in the category",
      highest_gender['Gender'], "with an average of", highest_gender['Average'])
print("  3. In the Hometown feature, the highest sample mean is in the category",
      highest_hometown['Hometown'], "with an average of", highest_hometown['Average'])
```

**Output:** Three grouped-mean summary tables, a combined multi-index table, a single figure containing three side-by-side bar charts, and three printed statements naming the top-performing category for Track, Gender, and Hometown (exact categories and values depend on the contents of `board2.xlsx`).

---

## 📁 Project File Structure

```text
.
├── board2.xlsx        # Source student-scores dataset
├── ADPROG_PA4.ipynb   # Jupyter Notebook implementation
└── README.md          # Project documentation
```

---

## 🛠️ Prerequisites & Requirements

* **Python 3.8+**
* **Pandas:** Install via `pip install pandas`
* **Matplotlib:** Install via `pip install matplotlib`
* **Jupyter Notebook / JupyterLab**

---

## 🚀 How to Run

### Using Terminal / Command Prompt

1. Place `board2.xlsx` in the root directory alongside the notebook.
2. Convert and run the notebook as a script if desired:

```bash
jupyter nbconvert --to script ADPROG_PA4.ipynb
python ADPROG_PA4.py
```

### Using Jupyter Notebook

1. Launch Jupyter Notebook:

```bash
jupyter notebook
```

2. Open `ADPROG_PA4.ipynb`.
3. Execute all cells (`Cell` → `Run All`).

---

## 🛡️ Key Constraints & Edge Cases Handled

* **Dual-Condition Filtering:** Both filtering conditions (e.g., `Hometown` and `Track`, or `Hometown` and `Gender`) are applied to the source DataFrame **before** column subsetting, as required.
* **Non-Destructive Secondary Filtering:** The `Average >= 60` filter on `VisFemale` is assigned to a new variable (`VisFemale_60`), leaving `VisFemale` unmodified.
* **Category-wise Aggregation:** Uses `groupby(as_index=False)` so each summary table retains its category column instead of becoming an index.
* **Unified Multi-Feature Comparison:** `pd.concat` with `keys=` produces one labeled table combining all three grouped-mean summaries.
* **Single-Figure, Multi-Chart Layout:** All three bar charts are rendered as subplots within one `fig, axes` figure rather than as separate plots.
* **Descriptive, Non-Causal Interpretation:** Findings are reported strictly as observed differences in sample means, explicitly avoiding causal claims.
## README file Version History
- September 9, 2026 - Upload .ipnyb file
- September 9, 2026 - Upload README file


