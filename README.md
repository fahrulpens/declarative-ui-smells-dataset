# DUIS Dataset

### A Declarative UI Smell Dataset for Flutter Code

The **DUIS Dataset** (**Declarative UI Smell Dataset**) is a labeled dataset of Flutter source-code snippets designed to support research on **declarative UI code smell detection**. The dataset contains **1,896 annotated code samples** collected from open-source Flutter projects and labeled for three declarative UI code smell types:

* **Nested View (NV)**
* **Mutable View (MV)**
* **Blocking View (BV)**

The dataset was introduced in the following conference paper:

> [1] F. F. Hardiansyah, D. Siahaan, and M. Aritsugi, “Evaluating Deep Learning Models for Declarative UI Code Smell Detection,” in *2026 International Conference on Smart Computing, IoT, and Machine Learning (SIML)*, 2026.

---

## 1. Overview

Declarative UI frameworks, such as Flutter, structure user interfaces as a function of application state. While this paradigm improves composability and productivity, it can also introduce specific quality issues related to widget hierarchy complexity, state mutation during rendering, and blocking or expensive operations embedded in UI declarations.

The DUIS Dataset was constructed to provide a publicly reusable dataset for studying these issues in Flutter code. It is intended to support tasks such as:

* Declarative UI code smell detection
* Binary or multi-label classification
* Comparative evaluation of machine learning and deep learning methods
* Analysis of structural and semantic characteristics of declarative UI smells
* Dataset-driven studies on software quality in Flutter applications

---

## 2. Dataset at a Glance

| Item                   | Description                                                                                      |
| ---------------------- | ------------------------------------------------------------------------------------------------ |
| Dataset name           | DUIS Dataset                                                                                     |
| Full name              | Declarative UI Smell Dataset                                                                     |
| Programming language   | Dart                                                                                             |
| Framework              | Flutter                                                                                          |
| Number of samples      | 1,896 code snippets                                                                              |
| Number of smell types  | 3                                                                                                |
| Smell types            | Nested View, Mutable View, Blocking View                                                         |
| Labeling type          | Binary label per smell (`0` = absent, `1` = present)                                             |
| Dataset representation | Multi-label dataset; one snippet may contain none, one, or multiple smells                       |
| Annotation setting     | Five professional mobile developers                                                              |
| Final-label strategy   | Majority voting over five independent annotations                                                |
| Code units             | Flutter source-code snippets extracted from classes, methods, widgets, and related UI components |
| Data source            | Open-source Flutter projects from GitHub                                                         |
| Main release formats   | CSV and JSONL                                                                                    |

---

## 3. Code Smells Covered

### 3.1 Nested View (NV)

**Nested View** refers to excessively deep or complex widget nesting in declarative UI code. This smell typically appears when a UI declaration contains long chains of nested widgets or structurally complex layout compositions that reduce readability and increase modification effort.

Typical indicators include:

* Deeply nested widget trees
* Long chains of wrapper widgets
* Large numbers of widget constructor calls within one UI-related code unit
* Complex conditional UI logic intertwined with layout structure

---

### 3.2 Mutable View (MV)

**Mutable View** refers to state mutation performed inside UI-declaration logic or during rendering. This smell violates the declarative principle that UI should be derived from state rather than mutating state as part of view construction.

Typical indicators include:

* Calling `setState()` inside a rendering-related method
* Writing to shared state during UI construction
* Mutating collections that are directly consumed by the UI
* Creating side-effectful controllers, timers, or streams during rendering

---

### 3.3 Blocking View (BV)

**Blocking View** refers to blocking or computationally expensive work executed during UI declaration or rendering logic. Such operations may degrade responsiveness, contribute to frame drops, and reduce perceived application smoothness.

Typical indicators include:

* Synchronous I/O or blocking calls
* Expensive loops or sorting operations in view construction logic
* Heavy parsing, decoding, or transformation within render-related code
* Network, database, or storage operations initiated during UI declaration

> **Note:** In earlier annotation materials, this smell may also be described as **Expensive View**. In this repository, the official term follows the paper terminology: **Blocking View (BV)**.

---

## 4. Dataset Files

The public release is organized around one merged multi-label dataset distributed in two equivalent formats:

```text
data/
├── duis_dataset.csv
└── duis_dataset.jsonl
```

### 4.1 `duis_dataset.csv`

A tabular version of the dataset intended for quick inspection and conventional data-processing workflows.

### 4.2 `duis_dataset.jsonl`

A line-delimited JSON version of the dataset intended for code-oriented and programmatic processing. Each line represents one dataset sample.

---

## 5. Dataset Schema

Each sample contains:

1. A unique sample identifier
2. A Flutter source-code snippet
3. Three binary smell labels
4. Twenty extracted metric values associated with structural and behavior-related characteristics of the code snippet

### 5.1 Core Fields

| Field           | Description                                 |
| --------------- | ------------------------------------------- |
| `sample_id`     | Unique identifier of the code sample        |
| `code_snippet`  | Extracted Flutter source-code snippet       |
| `nested_view`   | Binary label for Nested View (`0` or `1`)   |
| `mutable_view`  | Binary label for Mutable View (`0` or `1`)  |
| `blocking_view` | Binary label for Blocking View (`0` or `1`) |

### 5.2 Metric Fields

The released dataset includes the following metric columns:

```text
LoC, NoM, NoP, CC, MND, NoF, CR, NoW, MNW, SCCL,
sStC, PBM, FAC, MC, API, DbC, SyncIO, ImgC, AsyncUI, TmrStr
```

These metrics were retained from the dataset construction process and are included to support further analysis of structural and behavior-related characteristics associated with declarative UI code smells.

> Detailed definitions of each metric will be provided in `docs/data_schema.md`.

---

## 6. Label Distribution

The DUIS Dataset contains binary labels for each of the three declarative UI code smells. The label distribution reported in the associated study is summarized below.

| Smell Type         | Smelly Samples | Clean Samples | Smelly (%) | Clean (%) | Imbalance Ratio |
| ------------------ | -------------: | ------------: | ---------: | --------: | --------------: |
| Nested View (NV)   |          1,048 |           848 |       55.3 |      44.7 |        1.24 : 1 |
| Mutable View (MV)  |          1,179 |           717 |       62.2 |      37.8 |        1.61 : 1 |
| Blocking View (BV) |            391 |         1,505 |       20.6 |      79.4 |        3.85 : 1 |

---

## 7. Dataset Construction Summary

The DUIS Dataset was developed through a multi-stage construction process designed to support data quality, annotation consistency, and reuse by future studies.

### 7.1 Target Smell Definition

Three declarative UI code smells were selected and operationalized:

* Nested View (NV)
* Mutable View (MV)
* Blocking View (BV)

The smell definitions, typical symptoms, impacted quality aspects, and annotation considerations are documented in the annotation guideline.

### 7.2 Project Collection and Sampling

A set of **57 candidate open-source Flutter projects** was initially collected from GitHub. From these candidates, **five projects** were selected using **stratified random sampling based on Lines of Code (LoC)** to represent different project-size strata.

The selected projects were chosen at the 10th, 30th, 50th, 70th, and 90th percentiles.

| Project                   | Lines of Code (LoC) | Number of Samples | Percentile |
| ------------------------- | ------------------: | ----------------: | ---------: |
| `flutter-task-manager`    |               1,132 |                43 |         10 |
| `instagram-flutter-clone` |               2,444 |                95 |         30 |
| `mobile-iot-device`       |               5,240 |               275 |         50 |
| `Timecop`                 |              10,120 |               264 |         70 |
| `air-controller-desktop`  |              31,268 |             1,220 |         90 |

### 7.3 Snippet Extraction

Code samples were extracted from Flutter source code as snippet-level units. These snippets represent classes, methods, widgets, or other UI-related components relevant to declarative UI code smell analysis.

The final dataset contains **1,896 code snippets**.

### 7.4 Annotation Process

All code snippets were independently annotated by **five professional mobile developers**. Each annotator labeled the presence or absence of each target smell using binary values.

For each sample and each smell:

* `0` indicates that the smell is absent
* `1` indicates that the smell is present

The dataset supports multi-label representation, meaning that a single snippet may exhibit none, one, or multiple smell types.

Final labels in the released DUIS Dataset were determined using **majority voting** over the five independent annotations.

### 7.5 Annotation Quality Assessment

Annotation reliability was evaluated through inter-rater agreement analysis. Pairwise agreement was assessed using **Cohen’s Kappa**, and overall agreement across annotators was assessed using **Fleiss’s Kappa**.

The overall agreement results are summarized below.

| Smell Type         | Fleiss’s Kappa | 95% Confidence Interval | Interpretation      |
| ------------------ | -------------: | ----------------------- | ------------------- |
| Nested View (NV)   |           0.85 | 0.84–0.87               | Excellent agreement |
| Mutable View (MV)  |           0.63 | 0.61–0.65               | Good agreement      |
| Blocking View (BV) |           0.50 | 0.47–0.53               | Good agreement      |

More detailed annotation materials and agreement summaries are planned to be documented in the repository documentation.

---

## 8. Repository Structure

The repository is organized as follows:

```text
.
├── README.md
├── LICENSE
├── CITATION.cff
│
├── data/
│   ├── per_smell/
│       ├── rawdata_nv.csv
│       ├── rawdata_mv.csv
│       ├── rawdata_bv.csv
│   ├── raw_annotations/
│       ├── raw_annotations.csv
│   ├── duis_dataset.csv
│   └── duis_dataset.jsonl
│
├── docs/
│   ├── annotation_guideline.md
│   ├── dataset_construction.md
│   └── data_schema.md
│
└── metadata/
    ├── selected_projects.csv
    └── candidate_projects.csv
```

---

## 9. Quick Start

### 9.1 Loading the CSV Version with Python

```python
import pandas as pd

dataset = pd.read_csv("data/duis_dataset.csv")
print(dataset.head())
```

### 9.2 Loading the JSONL Version with Python

```python
import pandas as pd

dataset = pd.read_json("data/duis_dataset.jsonl", lines=True)
print(dataset.head())
```

### 9.3 Example Label Selection

```python
labels = dataset[["nested_view", "mutable_view", "blocking_view"]]
print(labels.head())
```

---

## 10. Intended Use

The DUIS Dataset is intended to support academic and empirical research on declarative UI software quality, particularly in the context of Flutter applications.

Potential use cases include:

* Training code smell detection models
* Benchmarking machine learning or deep learning classifiers
* Investigating the structural and semantic characteristics of declarative UI smells
* Evaluating code representation strategies for smell detection
* Supporting future studies on software maintainability in declarative UI frameworks

---

## 11. Citation

If you use the DUIS Dataset in your research, please cite the associated paper using the following IEEE-style reference:

> [1] F. F. Hardiansyah, D. Siahaan, and M. Aritsugi, “Evaluating Deep Learning Models for Declarative UI Code Smell Detection,” in *2026 International Conference on Smart Computing, IoT, and Machine Learning (SIML)*, 2026.

A machine-readable citation file will be provided in `CITATION.cff`.

---

## 12. License

This repository is released under the **MIT License**, unless otherwise stated in specific files.

---

## 13. Contact

For questions, corrections, or collaboration related to the DUIS Dataset, please contact:

**Fadilah Fahrul Hardiansyah**
Department of Informatics
Institut Teknologi Sepuluh Nopember, Indonesia

GitHub: `fahrulpens`
