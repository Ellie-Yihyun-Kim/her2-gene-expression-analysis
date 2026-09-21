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
    - Values = preprocessed, log2-normalized microarray expression intensities
- In Pandas, this tabular structure can be represented as a DataFrame
- The feature IDs are platform-specific microarray IDs, not necessarily gene symbols. Gene annotation is needed to connect features to genes such as ERBB2.

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