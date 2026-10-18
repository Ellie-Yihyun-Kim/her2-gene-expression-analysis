# Data Exploration Notes

## GEO Structure
- **GEO** : Gene Expression Omnibus
- **GSE (GEO Series)**: A collection representating an entire study or experiment.
- **GSM (GEO Sample)**: An identifier for an individual sample within a GEO study

## Dataset Structure
- **Metadata**: Information that describes the dataset or samples, such as patient ID and timepoint.
- **Expression matrix**: Numerical expression measurements arranged in rows and columns.
    - Rows = expression features
    - Columns = samples
    - Values = preprocessed microarray expression measurements
- In Pandas, this tabular structure can be represented as a DataFrame
- The feature IDs are platform-specific identifiers, not necessarily gene symbols. Gene annotation is needed to connect features to genes such as ERBB2.

## Bash Commands
- `zcat`: Reads a gzip-compressed file and sends the decompressed
  contents to standard output without creating a new uncompressed file.
- `|` (pipe): Passes the output of one command as the input to the next command.
- `grep`: Searches text for lines matching a specified pattern.
- `sed`: A stream editor used to select or modify text.
  - Example: `sed -n '62,67p'` selects and prints only lines 62–67.
  - `p` = print
- `gunzip`: Decompresses a gzip (`.gz`) file.

## Concepts I Clarified
- stdout: standard output, 명령어가 결과를 내보내는 기본 출력 통로

## Python / Pandas Concepts

### Object, Attribute, and Method
- **Object**: something that Python can store and work with.
  - Example: `expression_df` refers to a Pandas DataFrame object.
- **Attribute**: information or a property that an object has.
  - Example: `expression_df.shape` gives the number of rows and columns.
- **Method**: an action that an object can perform.
  - Example: `expression_df.head()` shows the first 5 rows.
  - Example: `expression_df.tail()` shows the last 5 rows.

### DataFrame and Series
- **DataFrame**: a two-dimensional tabular data structure with rows and columns.
- **Series**: a one-dimensional data structure.
  - Selecting one column from a DataFrame returns a Series.
  - Example: `expression_df["ID_REF"]`

### Index
- The **index** is used by Pandas to label and identify rows.
- In this dataset, Pandas automatically assigned row indices starting from 0.
- The index is not the same as a data column such as `ID_REF`.

### Boolean Filtering
- A comparison can create a Series of `True` and `False` values.
- Example:
  `expression_df["ID_REF"] != "!series_matrix_table_end"`
- `!=` means "not equal to."
- Using this Boolean Series inside a DataFrame selects only rows where the condition is `True`.

### `.copy()`
- `.copy()` creates a separate copy of selected data.
- It is useful when creating a new DataFrame that will be modified independently from the original DataFrame.
- Example:
  `expression_clean_df = expression_df[condition].copy()`

### Sample Metadata and String Cleaning

- `.columns` returns the column labels of a DataFrame.
  - Example: `expression_clean_df.columns`
- Slicing `[1:]` selects elements from position 1 through the end.
  - In this dataset, this was used to exclude the `ID_REF` column and select the 64 sample columns.
- `len()` returns the number of items in an object.

- `.split()` separates a string using a specified delimiter.
  - Example: `"Pt16_Baseline".split("_")` gives `["Pt16", "Baseline"]`.

- `.strip()` removes characters or whitespace from the beginning and end of a string.
  - `.strip()` removes surrounding whitespace such as `\n`.
  - `.strip('"')` removes quotation marks from the ends of a string.

- `.unique()` returns the unique values in a Pandas Series.
  - It was useful for detecting an unexpected `Baseline"\n` value during metadata cleaning.

### DataFrame Column Assignment

- A new column can be added to a DataFrame using:
  `df["new_column"] = values`
- Example:
  `metadata_df["patient"] = patients`

### Jupyter Execution Order

- Re-running an earlier cell does not automatically update variables created in later cells.
- If an upstream variable changes, dependent cells need to be run again.

### `.sort_values()`

- Sorts a DataFrame by the values in a selected column.
- Example: `paired_df.sort_values("change").head()`
- Used to find the patients with the largest decreases in expression.

### `.loc[]`

- Selects data using row and column labels.
- Syntax: `df.loc[row_label, column_label]`
- Example: `paired_df.loc["Pt10", "Baseline"]`
  - `"Pt10"` is the row label (patient).
  - `"Baseline"` is the column label (timepoint).

### `.pivot()`

- Reshapes a DataFrame by organizing data into new rows and columns.
- Example: `analysis_df.pivot(index="patient", columns="timepoint", values="ERBB2_expression")`
  - `index="patient"`: Uses patient IDs as row labels.
  - `columns="timepoint"`: Creates Baseline and Post columns.
  - `values="ERBB2_expression"`: Fills the table with expression values.
- Used to compare Baseline and Post measurements for each patient.

## Statistical Concepts

### Outlier

- **Outlier**: A data point that is unusually high or low compared to other values.
- Outliers can affect the mean more strongly than the median.
- An outlier is not necessarily an error. It may reflect real biological variation.

**In this project:**
- The mean expression change (-1.21) was more negative than the median (-0.80).
- Some patients with relatively large decreases may have influenced the mean.
- However, this difference alone does not confirm the presence of outliers.

### Direction and Magnitude

- **Direction**: Whether a value increased or decreased.
- **Magnitude**: How large a change is, regardless of direction.
- Example:
  - Baseline = 12.5, Post = 9.0
  - Change = Post - Baseline = -3.5
  - Direction = decrease
  - Magnitude = 3.5

### Mean vs. Median

- **Mean**: The average of all values.
- **Median**: The middle value when the data are sorted.
- The mean is more sensitive to extreme values than the median.

**In this project:**
- Mean expression change = -1.21
- Median expression change = -0.80
- Some large decreases may have influenced the mean.
- This does not necessarily mean that outliers are present.

### Population v.s Sample

- Population: The entire group we want to draw conclusions about.
- Sample: The group actually observed or measured
- Sample mean: The average calculated from the observed sample.
- Population mean: The true average in the population, which is usually unknown.

**In this project**
32 paired patients are the analyzed sample. The observed mean ERBB2-associated expression change is -1.21. Statistical inference helps us evaluate hypotheses about the population mean using sample data.

### Variability vs. Sampling Variability

- Variability: Differences among individual observations within a sample.
- Sampling Variability: Differences in sample statistics (e.g., sample means) across different samples drawn from the same population.

**In this project**
- The SD of paired expression changes (1.47) describes variability among the 32 patients.
- If we repeatedly sampled different groups of 32 patients, their sample mean changes would vary due to sampling variability.

Larger, appropriately sampled groups generally provide more precise estimates of the population mean.

### Statistical Terminology and Notation

#### 1. Basic Symbols

| Symbol | Term | Meaning in This Project |
|---|---|---|
| \(n\) | Sample size | Number of paired patients (32) |
| \(d_i\) | Individual paired difference | Post − Baseline for patient \(i\) |
| \(\bar{d}\) | Sample mean difference | Average of the 32 paired changes (−1.2077) |
| \(\mu_d\) | Population mean difference | True mean paired change in the target population (unknown) |
| \(SD\) or \(s_d\) | Standard deviation | Spread of individual paired changes (1.4742) |
| \(SE\) | Standard error | Estimated variability of the sample mean across repeated samples (0.2606) |
| \(H_0\) | Null hypothesis | Population mean paired change equals zero |
| \(H_1\) | Alternative hypothesis | Population mean paired change does not equal zero |
| \(t\) | t-statistic | Standardized distance between the observed mean and the hypothesized mean |
| \(df\) | Degrees of freedom | \(n-1=31\) for this paired t-test |
| \(p\) | p-value | Probability, assuming \(H_0\) is true, of a test statistic at least as extreme as the observed one |

#### 2. Key Formulas

**Paired difference**

\[
d_i=Post_i-Baseline_i
\]

**Standard error**

\[
SE=\frac{SD}{\sqrt{n}}
\]

**Paired t-statistic**

\[
t=\frac{\bar{d}-\mu_0}{SE}
\]

Here, \(\mu_0=0\) under the null hypothesis.

**Degrees of freedom**

\[
df=n-1
\]

#### 3. Understanding the Differences

- **Mean vs. SD:** The mean describes the average change; SD describes how much individual patients' changes vary.
- **SD vs. SE:** SD describes variation among patients; SE describes uncertainty in estimating the population mean from a sample.
- **Sample mean vs. population mean:** The sample mean is calculated from observed data; the population mean is generally unknown.
- **t-statistic vs. p-value:** The t-statistic measures standardized distance from the null hypothesis; the p-value evaluates how unusual that statistic is under the null hypothesis.
- **Statistical significance vs. individual responses:** A statistically significant mean change does not imply that every patient changed in the same direction.
- **Statistical significance vs. causation:** A statistically significant change alone does not establish that treatment caused the change.

#### 4. ERBB2 Project Example

For 32 paired patients:

- Mean paired change = −1.2077
- SD = 1.4742
- SE = 0.2606
- t-statistic ≈ −4.63
- df = 31
- 26 patients decreased; 6 patients increased

The observed average ERBB2-associated expression change is negative, but individual patients showed different directions and magnitudes of change.

The p-value has not yet been calculated.


## Data Visualization Concepts

### `plt.plot(x, y)`
- Plots points using corresponding x and y values.
- Example: `plt.plot(["Baseline", "Post"], [13.02395, 8.553489])`
- Creates two points:
  - (Baseline, 13.02395)
  - (Post, 8.553489)
- Connects the points with a line.
- A downward line indicates a decrease from Baseline to Post.

### Overplotting
- Overplotting occurs when multiple lines or data points overlap, making a graph difficult to read.
- In this project, all 32 patients' expression changes were plotted together.
- Although all 6 increasing lines were visible, some lines overlapped or were very close to each other.
- This made it difficult to distinguish individual patient changes.

### Histogram vs. Paired Line Plot
- **Histogram**: Shows the distribution of expression changes across patients.
  - Useful for seeing where values are concentrated and how widely they vary.
- **Paired Line Plot**: Shows how expression changes from Baseline to Post for each patient.
  - Useful for comparing the direction and size of individual patient changes.
- The two plots show different aspects of the same data.