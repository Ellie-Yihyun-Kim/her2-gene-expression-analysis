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

Mean paired change = −1.2077

SD = 1.4742

SE = 0.2606

t-statistic ≈ −4.63

df = 31

p-value ≈ 0.0000611

95% CI = [−1.7392, −0.6762]

26 patients decreased; 6 patients increased

The observed average ERBB2-associated expression change was negative, but individual patients showed different directions and magnitudes of change.

The paired t-test showed a statistically significant mean change (p < 0.001). The 95% confidence interval did not include zero, which is consistent with the t-test result.

However, statistical significance does not establish that treatment caused the observed change.

#### 5. Paired t-test and p-value
##### What is a paired t-test?

A paired t-test evaluates whether the population mean difference between two related measurements is significantly different from a hypothesized value, usually zero.

##### Why did I use it in this project?

- I analyzed Baseline and Post measurements from the same 32 patients.
- Because the measurements were paired within each patient, I used a paired t-test rather than an independent two-sample t-test.
- The test evaluates whether the population mean of the patient-level differences (Post - Baseline) differs from zero.

*Hypotheses:*

- Null hypothesis (H₀): The population mean paired difference equals zero.
- Alternative hypothesis (H₁): The population mean paired difference does not equal zero.

##### How does the t-test work?
- The t-statistic measures how far the observed mean difference is from the hypothesized value, in units of standard error.
- If the mean difference stays the same but SE increases, the absolute t-statistic decreases.
- With the same degrees of freedom, a smaller absolute t-statistic corresponds to a larger two-sided p-value.

##### What does the p-value mean?
- The p-value is the probability of obtaining a test statistic at least as extreme as the observed one, assuming the null hypothesis is true.
- It is not the probability that the null hypothesis is true.

Python:
```
t_stat, p_value = stats.ttest_rel(
    paired_df["Post"],
    paired_df["Baseline"]
)
```
##### Interpretation in this project:

The paired t-test produced a p-value of approximately 0.0000611, providing strong statistical evidence against the null hypothesis of zero population mean change.

However, this result does not establish that treatment caused the observed expression change.

#### 6. Understanding the 95% Confidence Interval

##### What is a 95% Confidence Interval (CI)?

A 95% confidence interval estimates a range of plausible values for the unknown population mean.

If we repeatedly collected samples and calculated a 95% CI from each sample, approximately 95% of those intervals would contain the true population mean, assuming the statistical assumptions hold.

It does not mean that there is a 95% probability that the fixed population mean lies within this particular interval.

##### How is the 95% CI calculated?

For the mean paired difference:

\[
95\%\ CI = \bar{d} \pm t_{\text{critical}} \times SE
\]

- `mean_change` is the observed mean paired difference.
- `standard_error` measures uncertainty in the sample mean.
- `t_critical` determines the margin of error based on the confidence level and degrees of freedom.

##### Why do we use `stats.t.ppf(0.975, 31)`?

For a two-sided 95% CI:

- The central 95% of the t-distribution is used.
- The remaining 5% is divided equally between the two tails (2.5% each).
- The cumulative probability to the left of the positive critical t-value is 97.5%, or 0.975.
- The degrees of freedom are `n - 1 = 31` because there are 32 paired patients.

`stats.t.ppf()` returns the t-value corresponding to a specified cumulative probability.

```python
t_critical = stats.t.ppf(0.975, 31)
```

The critical t-value is approximately 2.04.

##### Python Code and Independent Validation

```python
t_critical = stats.t.ppf(
    1 - 0.05 / 2,
    degrees_freedom
)

ci_lower = mean_change - t_critical * standard_error
ci_upper = mean_change + t_critical * standard_error
```

I independently checked the result using:

```python
ci_check = stats.t.interval(
    confidence=0.95,
    df=degrees_freedom,
    loc=mean_change,
    scale=standard_error
)
```

Both approaches produced the same confidence interval.

##### Interpretation in This Project

- Mean paired change: −1.2077
- 95% CI: [−1.7392, −0.6762]
- The interval does not contain zero.
- This is consistent with the statistically significant two-sided paired t-test (p < 0.001).

The estimated population mean ERBB2-associated expression change is negative, although the analysis does not establish that treatment caused the change.

#### 7. Python Functions for Statistical Analysis

| Function | Purpose |
|---|---|
| `stats.ttest_rel()` | Performs a paired t-test and returns the t-statistic and p-value. |
| `stats.t.ppf()` | Returns the t-value corresponding to a specified cumulative probability. |
| `stats.t.cdf()` | Returns the cumulative probability to the left of a specified t-value. |
| `stats.t.interval()` | Calculates an interval from the t-distribution using a specified confidence level, degrees of freedom, location, and scale. |

**Key distinction:**

- `ttest_rel()` tests a hypothesis about the mean paired difference.
- `t.ppf()` finds a critical t-value from a cumulative probability.
- `t.cdf()` converts a t-value into a cumulative probability.
- `t.interval()` can calculate a confidence interval for the mean when `loc` is the sample mean and `scale` is its standard error.

**What I learned:**

The paired t-test and 95% CI use the same paired differences, standard error, and t-distribution, but answer different questions:

- **Paired t-test:** Is the population mean paired difference statistically different from zero?
- **95% CI:** What range of values is plausible for the population mean paired difference?

#### 8. Concepts to Review Later

**Current learning status:**

I can understand these statistical concepts when they are explained and answer guided questions, but I still need practice explaining them independently.

**Topics to review:**

- Explain what a paired t-test is and why I used it for this project.
- Explain the difference between SD and SE in my own words.
- Explain why the degrees of freedom are `n - 1` for a paired t-test.
- Explain how the t-statistic and p-value are related.
- Explain why `stats.t.ppf(0.975, 31)` is used to calculate a two-sided 95% CI.
- Explain the difference between CDF and PPF.
- Interpret a 95% CI without confusing it with a 95% probability that the population mean lies within the calculated interval.
- Explain how I independently validated the AI-generated confidence interval calculation.

**Future practice goal:**

Explain the statistical methods and results of this project in 1–2 minutes without referring to notes, and answer follow-up questions about the analysis.

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

## Microarray Concepts

### What Is a Microarray?

A gene expression microarray is an experimental technology used to measure signals associated with RNA abundance.

- RNA-derived targets are prepared from biological samples.
- Labeled targets hybridize to complementary DNA probes on the microarray.
- Fluorescence signals are measured and processed to obtain expression values.
- These values are not direct counts of RNA molecules or measurements of protein abundance.

### Probe vs. Feature

- **Probe:** A DNA sequence designed to hybridize with a complementary target sequence.
- **Feature:** A measurement unit represented in the expression dataset. Depending on the platform, it may correspond to a single probe or a summarized group of probes, such as a probe set or transcript cluster.
- **Feature ID:** A platform-specific identifier for a measurement unit, not necessarily a unique gene symbol.

**Key distinction:** One feature does not always correspond to exactly one gene.

### In This Project

- **Platform:** GPL23159
- **Feature ID:** `TC1700012249.hg.1`
- **Associated annotation:** ERBB2 and MIR4728 (to be verified against the official platform annotation).

ERBB2 is a gene, whereas HER2 is the protein encoded by ERBB2.

We describe the analyzed measurement as **ERBB2-associated expression** rather than ERBB2-specific mRNA abundance or HER2 protein expression.

### Interpretation Limitations

- Microarray expression values do not directly measure HER2 protein abundance or activity.
- A feature associated with multiple transcript annotations cannot automatically be interpreted as a gene-specific measurement.
- A statistically significant paired change does not establish that treatment caused the change.
- The results from 32 paired patients cannot automatically be generalized to all breast cancer patients.

## AI-Assisted Workflow and Git

### 1. Using GitHub Copilot Agent

**Purpose:**
I used GitHub Copilot Agent to help add a 95% confidence interval calculation to my existing ERBB2 expression analysis notebook.

**What Copilot Agent did:**
- Generated Python code to calculate the 95% confidence interval.
- Used the mean paired difference, standard error, degrees of freedom, and t-distribution.
- Helped clean up unintended changes to existing Jupyter Notebook cells after the notebook was re-executed.

**What I did:**
- Identified the analysis I wanted to add.
- Reviewed the AI-generated code and its results.
- Independently validated the confidence interval using `stats.t.interval()`.
- Confirmed that both calculation methods produced the same results.

**Key lesson:**
AI-generated code should be reviewed and validated rather than accepted without verification.

### 2. Managing Jupyter Notebook Changes

Jupyter Notebook files (`.ipynb`) store not only code but also execution counts, cell outputs, and metadata.

Re-running cells can therefore create many changes in Git, even when the code itself has not changed.

**In this project:**
- Re-executing the notebook caused unintended changes to existing cells.
- Copilot Agent helped restore the original execution counts and outputs.
- The final notebook changes were limited to two newly added code cells.
- The original 110 cells were preserved.

**Key lesson:**
Review notebook changes before committing to avoid including unrelated modifications.

### 3. Git and GitHub Workflow

**Branch:**
- I worked on a separate branch named `agentic-stats`.
- This allowed me to add and validate the confidence interval without changing the `main` branch.

**Commit:**
- A commit records a snapshot of tracked changes.
- I committed the confidence interval update with the message:
  `Add and validate 95% confidence interval for ERBB2 expression`

**Push:**
- Pushing uploads local commits to the remote GitHub repository.
- I successfully pushed the `agentic-stats` branch to GitHub.

**Key distinction:**
- `git add` stages changes.
- `git commit` records staged changes in local repository history.
- `git push` uploads local commits to the remote repository.

### 4. Lessons Learned

- AI assistance can help implement unfamiliar statistical methods, but I remain responsible for understanding and validating the results.
- Independent verification is important when using AI-generated code.
- Jupyter Notebook execution can introduce unintended file changes.
- Git branches help separate experimental work from the main project.
- Clear documentation makes it easier to explain both the analysis and the development process later.
