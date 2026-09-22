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