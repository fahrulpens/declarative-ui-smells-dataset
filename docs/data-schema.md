# DUIS Dataset Data Schema

This document describes the released schema of the **DUIS Dataset** (**Declarative UI Smell Dataset**), including the data files, record structure, label fields, and the 20 extracted metric fields included in the dataset.

---

## 1. Released Data Files

The DUIS Dataset is distributed in two equivalent formats:

```text
data/
├── duis_dataset.csv
└── duis_dataset.jsonl
```

Both files contain the same samples and the same columns/fields. The CSV version is intended for tabular processing, while the JSONL version is intended for code-oriented and programmatic workflows.

---

## 2. Record Granularity

Each record represents **one Flutter source-code snippet** extracted from a selected open-source Flutter project.

A snippet may correspond to:

* A class
* A method or function
* A widget declaration
* Another UI-related source-code component used during the dataset construction process

Each snippet is associated with:

1. A unique sample identifier
2. The original Flutter code snippet
3. Three binary declarative UI smell labels
4. Twenty extracted metrics

---

## 3. Schema Overview

The merged DUIS Dataset uses the following field order:

```text
sample_id,
code_snippet,
nested_view,
mutable_view,
blocking_view,
LoC,
NoM,
NoP,
CC,
MND,
NoF,
CR,
NoW,
MNW,
SCCL,
sStC,
PBM,
FAC,
MC,
API,
DbC,
SyncIO,
ImgC,
AsyncUI,
TmrStr
```

---

## 4. Core Fields

| Field          | Type    | Description                                                                                                        |
| -------------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| `sample_id`    | Integer | Unique identifier for a code snippet.                                                                              |
| `code_snippet` | String  | Flutter source-code snippet retained as text. The value may contain multiple lines and code formatting characters. |

### 4.1 Notes on `code_snippet`

* The field stores the source code as a string.
* Newline characters are preserved in JSONL and represented according to the chosen CSV export rules in the CSV file.
* The snippet is not guaranteed to be a complete standalone Dart file; it is a code unit extracted for dataset construction and annotation.

---

## 5. Label Fields

The DUIS Dataset is released as a **multi-label dataset**. Each code snippet has one binary label for each of the three target smell types.

| Field           | Type    | Allowed Values | Description                       |
| --------------- | ------- | -------------- | --------------------------------- |
| `nested_view`   | Integer | `0`, `1`       | Label for **Nested View (NV)**.   |
| `mutable_view`  | Integer | `0`, `1`       | Label for **Mutable View (MV)**.  |
| `blocking_view` | Integer | `0`, `1`       | Label for **Blocking View (BV)**. |

### 5.1 Label Interpretation

| Value | Meaning                                      |
| ----- | -------------------------------------------- |
| `0`   | The target smell is absent from the snippet. |
| `1`   | The target smell is present in the snippet.  |

A single sample may contain:

* No smell labels with value `1`
* Exactly one smell label with value `1`
* Multiple smell labels with value `1`

---

## 6. Metric Fields

The dataset includes **20 Dart/Flutter code metrics** extracted from each snippet. These metrics capture general code properties, widget-structure characteristics, state mutation signals, and runtime/blocking-effect indicators.

### 6.1 General Structural Metrics

| Field | Type    | Full Name             | Description                                                                                |
| ----- | ------- | --------------------- | ------------------------------------------------------------------------------------------ |
| `LoC` | Integer | Lines of Code         | Number of non-empty, non-comment source-code lines.                                        |
| `NoM` | Integer | Number of Methods     | Number of declarations that resemble functions, methods, or constructors.                  |
| `NoP` | Integer | Number of Parameters  | Maximum number of parameters among methods/functions detected in the snippet.              |
| `CC`  | Integer | Cyclomatic Complexity | McCabe-style complexity score computed as one plus the number of detected decision points. |
| `NoF` | Integer | Number of Fields      | Number of class-level fields detected in class bodies.                                     |
| `CR`  | Float   | Comment Ratio         | Ratio of comment lines to lines of code, rounded to three decimal places.                  |

---

### 6.2 Widget-Structure Metrics

| Field  | Type    | Full Name                         | Description                                                                                             |
| ------ | ------- | --------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `NoW`  | Integer | Number of Widgets                 | Number of capitalized constructor-like calls used as a heuristic for widget occurrences.                |
| `MND` | Integer | Maximum Nesting Depth | Deepest block nesting level found in the snippet, excluding the outermost scope.           |
| `MNW`  | Integer | Maximum Nested Widget Tree Depth  | Maximum depth of nested capitalized constructor calls, used as an approximation of widget-tree nesting. |
| `SCCL` | Integer | Single-Child Wrapper Chain Length | Length of the longest nested `child:`-based wrapper chain detected in the snippet.                      |

---

### 6.3 State Mutation and Side-Effect Metrics

| Field  | Type    | Full Name                             | Description                                                                                                                           |
| ------ | ------- | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `sStC` | Integer | `setState` Call Count                 | Number of `setState(...)` calls detected in the snippet.                                                                              |
| `PBM`  | Integer | Provider/Bloc Mutation Count          | Number of detected state mutations through common state-management patterns such as Provider, Bloc, or Riverpod-like access patterns. |
| `FAC`  | Integer | Field Assignment Count                | Number of writes to class or widget fields, such as assignments through `this.` or `widget.`.                                         |
| `MC`   | Integer | Mutable Collection Modification Count | Number of detected collection mutation operations, such as `.add(...)`, `.remove(...)`, `.clear(...)`, or indexed assignments.        |


---

### 6.4 Runtime and Blocking-Effect Metrics

| Field     | Type    | Full Name                         | Description                                                                                                                                            |
| --------- | ------- | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `DbC`     | Integer | Database Call Count               | Number of detected database or storage operations, including common sqflite, Hive, SharedPreferences, Drift/Moor, Sembast, or generic `db.*` patterns. |
| `SyncIO`  | Integer | Synchronous I/O Count             | Number of detected blocking file or I/O operations, including sync file methods and `sleep(...)`.                                                      |
| `ImgC`    | Integer | Image Codec Call Count            | Number of detected image decoding or codec-related calls that may be expensive during UI execution.                                                    |
| `AsyncUI` | Integer | Async/Await in UI Count           | Number of `await` occurrences in the snippet. This count is snippet-level and is not restricted only to `build()` methods.                             |
| `TmrStr`  | Integer | Timer/Stream Initialization Count | Number of detected timer or stream-creation patterns, such as `Timer(...)`, `Timer.periodic(...)`, or stream controller initialization.                |
| `API`  | Integer | API Call Count                        | Number of detected common network/API calls, such as HTTP, Dio, or WebSocket usage patterns.                                          |

---

## 7. CSV Format

The CSV file follows a flat tabular schema.

### 7.1 Header

```csv
sample_id,code_snippet,nested_view,mutable_view,blocking_view,LoC,NoM,NoP,CC,MND,NoF,CR,NoW,MNW,SCCL,sStC,PBM,FAC,MC,API,DbC,SyncIO,ImgC,AsyncUI,TmrStr
```

### 7.2 CSV Handling Notes

* `code_snippet` may contain commas, quotes, and line breaks; therefore, the field must remain properly escaped in valid CSV output.
* Label fields should remain integer values `0` or `1`.
* Metric fields should remain numeric.

---

## 8. JSONL Format

The JSONL file stores one JSON object per line.

### 8.1 Example Record

```json
{
  "sample_id": 1,
  "code_snippet": "Widget build(BuildContext context) {\n  return MaterialApp(...);\n}",
  "nested_view": 0,
  "mutable_view": 0,
  "blocking_view": 0,
  "LoC": 11,
  "NoM": 1,
  "NoP": 1,
  "CC": 1,
  "MND": 0,
  "NoF": 0,
  "CR": 0.0,
  "NoW": 3,
  "MNW": 2,
  "SCCL": 0,
  "sStC": 0,
  "PBM": 0,
  "FAC": 0,
  "MC": 0,
  "API": 0,
  "DbC": 0,
  "SyncIO": 0,
  "ImgC": 0,
  "AsyncUI": 0,
  "TmrStr": 0
}
```

### 8.2 JSONL Handling Notes

* Each line is a valid JSON object.
* Newline characters inside code snippets are escaped within the JSON string.
* JSONL is recommended for programmatic processing of code snippets and text-preserving data pipelines.

---

## 9. Data Integrity Constraints

The released dataset should satisfy the following constraints:

| Constraint           | Description                                                                            |
| -------------------- | -------------------------------------------------------------------------------------- |
| Unique ID            | `sample_id` should be unique across all records.                                       |
| Label domain         | `nested_view`, `mutable_view`, and `blocking_view` must be either `0` or `1`.          |
| Numeric metrics      | Metric fields must contain numeric values compatible with the type descriptions above. |
| Record consistency   | CSV and JSONL versions must contain the same records in equivalent schema form.        |
| Multi-label validity | Any combination of the three smell labels is allowed.                                  |

---

## 10. Relationship to the Original Per-Smell Files

During dataset preparation, consensus-labeled working files were maintained separately for each target smell:

```text
rawdata_nv.csv
rawdata_mv.csv
rawdata_bv.csv
```

Each file contains:

* `sample_id`
* `code_snippet`
* one binary `label` column for the corresponding smell type
* the same 20 extracted metric columns

The public release merges these three smell-specific label columns into a unified multi-label dataset:

| Original File     | Original Label Column | Released Field  |
| ----------------- | --------------------- | --------------- |
| `rawdata_nv.csv` | `label`               | `nested_view`   |
| `rawdata_mv.csv` | `label`               | `mutable_view`  |
| `rawdata_bv.csv` | `label`               | `blocking_view` |

---

## 11. Recommended Loading Examples

### 11.1 CSV with pandas

```python
import pandas as pd

df = pd.read_csv("data/duis_dataset.csv")
print(df.shape)
print(df[["nested_view", "mutable_view", "blocking_view"]].head())
```

### 11.2 JSONL with pandas

```python
import pandas as pd

df = pd.read_json("data/duis_dataset.jsonl", lines=True)
print(df.shape)
print(df[["nested_view", "mutable_view", "blocking_view"]].head())
```

---

## 12. Versioning Note

This schema describes the planned public release format for the DUIS Dataset accompanying the SIML 2026 paper. Any future schema extension should preserve backward compatibility whenever possible and be documented through repository release notes and changelog entries.

