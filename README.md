# DUIS Dataset

### A Declarative UI Smell Dataset for Flutter Code

The **DUIS Dataset** (**Declarative UI Smell Dataset**) is a labeled dataset of Flutter source-code snippets designed to support research on **declarative UI code smell detection**. The dataset contains **1,896 annotated code samples** collected from open-source Flutter projects and labeled for three declarative UI code smell types:

* **Nested View (NV)**
* **Mutable View (MV)**
* **Blocking View (BV)**

The dataset was introduced in the paper:

> [1] F. F. Hardiansyah, D. Siahaan, and M. Aritsugi, “Evaluating Deep Learning Models for Declarative UI Code Smell Detection,” in 2026 International Conference on Smart Computing, IoT, and Machine Learning (SIML), 2026.

---

## 1. Overview

Declarative UI frameworks, such as Flutter, structure user interfaces as a function of application state. While this paradigm improves composability and productivity, it can also introduce specific quality issues related to widget hierarchy complexity, state mutation during rendering, and expensive operations embedded in UI declarations.

The DUIS Dataset was constructed to provide a publicly reusable benchmark for studying these issues in Flutter code. It is intended for tasks such as:

* Declarative UI code smell detection
* Binary or multi-label classification
* Comparative evaluation of machine learning and deep learning methods
* Analysis of structural and semantic characteristics of declarative UI smells
* Dataset-driven studies on software quality in Flutter applications

---

## 2. Dataset at a Glance

| Item                 | Description                                                                                      |
| -------------------- | ------------------------------------------------------------------------------------------------ |
| Dataset name         | DUIS Dataset                                                                                     |
| Full name            | Declarative UI Smell Dataset                                                                     |
| Programming language | Dart                                                                                             |
| Framework            | Flutter                                                                                          |
| Number of samples    | 1,896 code snippets                                                                              |
| Labeling type        | Binary label per smell (`0` = absent, `1` = present)                                             |
| Annotation setting   | Five professional mobile developers                                                              |
| Smell types          | Nested View, Mutable View, Blocking View                                                         |
| Code units           | Flutter source-code snippets extracted from classes, methods, widgets, and related UI components |
| Data source          | Open-source Flutter projects from GitHub                                                         |
| Main release formats | CSV and JSONL                                                                                    |

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

1. A unique identifier
2. A Flutter source-code snippet
3. Three binary smell labels
4. A set of structural and behavior-related metrics extracted from the snippet

### 5.1 Core Fields

| Field           | Description                                 |
| --------------- | ------------------------------------------- |
| `sample_id`     | Unique identifier of the code sample        |
| `code_snippet`  | Extracted Flutter source-code snippet       |
| `nested_view`   | Binary label for Nested View (`0` or `1`)   |
| `mutable_view`  | Binary label for Mutable View (`0` or `1`)  |
| `blocking_view` | Binary label for Blocking View (`0` or `1`) |

### 5.2 Metric Fields

The released dataset also contains metric columns associated with the original annotation and analysis process. These metrics include general source-code measures and smell-relevant indicators.

| Field     | Description                                                                            |
| --------- | -------------------------------------------------------------------------------------- |
| `LoC`     | Lines of Code                                                                          |
| `NoM`     | Number of Methods                                                                      |
| `NoP`     | Number of Parameters                                                                   |
| `CC`      | Cyclomatic Complexity                                                                  |
| `MND`     | Maximum Nesting Depth                                                                  |
| `NoF`     | Number of Fields                                                                       |
| `CR`      | Code-related structural measure retained from the dataset construction process         |
| `NoW`     | Number of Widgets                                                                      |
| `MNW`     | Maximum Nested Widgets                                                                 |
| `SCCL`    | Smell-related code complexity indicator retained from the dataset construction process |
| `sStC`    | State-related structural code measure retained from the dataset construction process   |
| `PBM`     | Pattern/behavior measure retained from the dataset construction process                |
| `FAC`     | Function/API-related count retained from the dataset construction process              |
| `MC`      | Mutation-related count retained from the dataset construction process                  |
| `API`     | API-call-related indicator                                                             |
| `DbC`     | Database-call-related indicator                                                        |
| `SyncIO`  | Synchronous I/O-related indicator                                                      |
| `ImgC`    | Image/codec-related indicator                                                          |
| `AsyncUI` | Asynchronous UI-related indicator                                                      |
| `TmrStr`  | Timer/stream-related indicator                                                         |

> A more detailed description of each metric will be provided in `docs/data_schema.md`.

---

## 6. Dataset Construction Summary

The DUIS Dataset was developed through a multi-stage construction process:

### 6.1 Target Smell Definition

Three declarative UI code smells were selected and operationalized:

* Nested View (NV)
* Mutable View (MV)
* Blocking View (BV)

The smell definitions, typical symptoms, impacted quality aspects, and annotation considerations are documented in the annotation guideline.

### 6.2 Project Collection and Sampling

A set of **57 candidate open-source Flutter projects** was initially collected from GitHub. From these candidates, **five projects** were selected using **stratified random sampling based on Lines of Code (LoC)** to represent different project-size strata.

The selected projects were chosen at the following percentile positions:

* 10th percentile
* 30th percentile
* 50th percentile
* 70th percentile
* 90th percentile

### 6.3 Snippet Extraction

Code samples were extracted from Flutter source code as snippet-level units. These snippets represent classes, methods, widgets, or other UI-related components relevant to declarative UI code smell analysis.

The final dataset contains **1,896 code snippets**.

### 6.4 Annotation Process

All code snippets were independently annotated by **five professional mobile developers**. Each annotator labeled the presence or absence of each target smell using binary values.

For each sample and each smell:

* `0` indicates that the smell is absent
* `1` indicates that the smell is present

The dataset supports multi-label representation, meaning that a single snippet may exhibit none, one, or multiple smell types.

### 6.5 Annotation Quality Assessment

Annotation reliability was evaluated through inter-rater agreement analysis. Pairwise agreement was assessed using **Cohen’s Kappa**, and overall agreement across annotators was assessed using **Fleiss’s Kappa**.

Detailed agreement statistics will be documented separately in the dataset documentation.

---

## 7. Repository Structure

The repository is planned as follows:

```text
.
├── README.md
├── LICENSE
├── CITATION.cff
│
├── data/
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
    ├── candidate_projects.csv
    └── stratified_sampling_summary.csv
```

---

## 8. Intended Use

The DUIS Dataset is intended to support academic and empirical research on declarative UI software quality, particularly in the context of Flutter applications.

Potential use cases include:

* Training code smell detection models
* Benchmarking machine learning or deep learning classifiers
* Investigating the structural and semantic characteristics of declarative UI smells
* Evaluating code representation strategies for smell detection
* Supporting future studies on software maintainability in declarative UI frameworks

---

## 9. Citation

If you use the DUIS Dataset in your research, please cite the associated paper:

```bibtex
@inproceedings{hardiansyah2026duis,
  title={Evaluating Deep Learning Models for Declarative UI Code Smell Detection},
  author={Hardiansyah, Fadilah Fahrul and Siahaan, Daniel and Aritsugi, Masayoshi},
  booktitle={Proceedings of SIML 2026},
  year={2026}
}
```

A machine-readable citation file will be provided in `CITATION.cff`.

---

## 10. License

This repository is released under the **MIT License**, unless otherwise stated in specific files.

---

## 11. Contact

For questions, corrections, or collaboration related to the DUIS Dataset, please contact:

**Fadilah Fahrul Hardiansyah**
Department of Informatics
Institut Teknologi Sepuluh Nopember, Indonesia

GitHub: `fahrulpens`
