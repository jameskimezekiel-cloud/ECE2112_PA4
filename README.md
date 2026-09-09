# PA4_ECE2112_JAMES, KE
---

### Experiment 4: Data Wrangling and Data Visualization
**Submitted by:** James, Kim Ezekiel G.| 2ECE-A | 09/09/2026

This notebook works with a board-exam-style student dataset (`board2.xlsx`) using `pandas` for data wrangling and `matplotlib` for visualization. It covers filtering data into targeted DataFrames, computing group averages, and building a summary bar-chart figure.

## Objectives
---
##### At the end of this laboratory activity, the student should be able to:
1. filter tabular data using several categorical and numerical conditions;
2. construct focused DataFrames by selecting relevant features;
3. summarize the relationship between categorical features and a numerical variable; and
4. communicate a data comparison using clear and correctly labeled plots.

## What the Notebook Does
- **Input file:** `board2.xlsx`, expected in the same directory as the notebook.
- Expected columns include: `Name`, `Gender`, `Hometown`, `Track`, `Math`, `Electronics`, `GEAS`, `Communication`.
- The notebook adds a computed `Average` column as the row-wise mean of `Math`, `Electronics`, `GEAS`, and `Communication`.

### Setup — Load Data & Compute `Average`

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_excel("board2.xlsx")

print("ORIGINAL DATASET")
display(df)

print("\nNumber of rows:", len(df))

df["Average"] = df[
    ["Math", "Electronics", "GEAS", "Communication"]].mean(axis=1)

display(df)
```

### A. Visayas Communication DataFrame
---

Filters students whose `Hometown` is Visayas and `Track` is Communication, then keeps only `Name, Gender, Math, Electronics, Average`. Displays the DataFrame and its row count.

```python
VisComm = df[
    (df["Hometown"] == "Visayas") &
    (df["Track"] == "Communication")
][["Name", "Gender", "Math", "Electronics", "Average"]]

print("A. VISAYAS COMMUNICATION DATAFRAME")
display(VisComm)

print("Number of rows:", len(VisComm))
```

### B. Visayas Female DataFrame
---

Filters students whose `Hometown` is Visayas and `Gender` is Female, keeping `Name, Track, GEAS, Electronics, Average`. Displays the full DataFrame, then a separate view (`VisFemale_60`) showing only rows with `Average >= 60`, without modifying the original `VisFemale`.

```python
VisFemale = df[
    (df["Hometown"] == "Visayas") &
    (df["Gender"] == "Female")
][["Name", "Track", "GEAS", "Electronics", "Average"]]

print("B. VISAYAS FEMALE DATAFRAME")

print("Complete VisFemale DataFrame:")
display(VisFemale)

VisFemale_60 = VisFemale[
    VisFemale["Average"] >= 60
]

print("VisFemale students with Average >= 60:")
display(VisFemale_60)
```

### C. Category-Average Visualization
---

**1. Compute group means**

```python
track_mean = df.groupby("Track", as_index=False)["Average"].mean()
print("MEAN AVERAGE BY TRACK")
display(track_mean)

gender_mean = df.groupby("Gender", as_index=False)["Average"].mean()
print("MEAN AVERAGE BY GENDER")
display(gender_mean)

hometown_mean = df.groupby("Hometown", as_index=False)["Average"].mean()
print("MEAN AVERAGE BY HOMETOWN")
display(hometown_mean)
```

**2. Plot the three bar charts in one figure**

```python
fig, axes = plt.subplots(1, 3, figsize=(18, 6))

# BAR CHART 1: TRACK
axes[0].bar(track_mean["Track"].astype(str), track_mean["Average"])
axes[0].set_title("Mean Average by Track")
axes[0].set_xlabel("Track")
axes[0].set_ylabel("Mean Average")
axes[0].tick_params(axis="x", rotation=45)

# BAR CHART 2: GENDER
axes[1].bar(gender_mean["Gender"].astype(str), gender_mean["Average"])
axes[1].set_title("Mean Average by Gender")
axes[1].set_xlabel("Gender")
axes[1].set_ylabel("Mean Average")
axes[1].tick_params(axis="x", rotation=45)

# BAR CHART 3: HOMETOWN
axes[2].bar(hometown_mean["Hometown"].astype(str), hometown_mean["Average"])
axes[2].set_title("Mean Average by Hometown")
axes[2].set_xlabel("Hometown")
axes[2].set_ylabel("Mean Average")
axes[2].tick_params(axis="x", rotation=45)

plt.tight_layout()
plt.show()
```

**3. Identify the highest-mean category per feature**

```python
highest_track = track_mean.loc[track_mean["Average"].idxmax()]
highest_gender = gender_mean.loc[gender_mean["Average"].idxmax()]
highest_hometown = hometown_mean.loc[hometown_mean["Average"].idxmax()]

print("Highest sample mean for each feature:")

print(
    f"1. Among the recorded tracks, "
    f"{highest_track['Track']} has the highest sample mean "
    f"Average of {highest_track['Average']:.2f}."
)

print(
    f"2. Among the recorded genders, "
    f"{highest_gender['Gender']} has the highest sample mean "
    f"Average of {highest_gender['Average']:.2f}."
)

print(
    f"3. Among the recorded hometown categories, "
    f"{highest_hometown['Hometown']} has the highest sample mean "
    f"Average of {highest_hometown['Average']:.2f}."
)
```
To view the program for PA4: download [ECE2112_PA4](https://github.com/jameskimezekiel-cloud/ECE2112_PA4/blob/main/ADPROG_PA4.ipynb), open on Jupyter Notebook, and run all cells.

## README file Version History
- September 9, 2026 - Upload .ipnyb file
- September 9, 2026 - Upload README file


