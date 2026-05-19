# DUIS Dataset Construction

This document describes how the **DUIS Dataset** (**Declarative UI Smell Dataset**) was constructed. It explains the sequence of decisions and processing stages used to develop the dataset released in this repository, from project collection to final label generation.

The construction process was designed to support:

* Clear operational definitions of target smells
* Diversity in source-project size
* Professional manual annotation
* Annotation reliability assessment
* Transparent consensus-label generation
* A reusable public dataset format

---

## 1. Construction Pipeline Overview

The DUIS Dataset was developed through five main stages:

1. **Target smell definition**
2. **Project collection and stratified sampling**
3. **Code snippet extraction**
4. **Independent multi-rater annotation and reliability assessment**
5. **Consensus labeling and final dataset assembly**

```text
Target smell definition
        ↓
Candidate Flutter project collection
        ↓
Stratified random sampling of source projects
        ↓
Flutter code snippet extraction
        ↓
Independent annotation by five mobile developers
        ↓
Inter-rater reliability assessment
        ↓
Majority-vote consensus labels
        ↓
Final DUIS Dataset release
```

---

## 2. Target Smell Definition

The dataset focuses on three declarative UI code smells:

* **Nested View (NV)**
* **Mutable View (MV)**
* **Blocking View (BV)**

These smells were selected because they capture recurring quality concerns in declarative UI code:

| Smell         | Main Concern                                                    |
| ------------- | --------------------------------------------------------------- |
| Nested View   | Excessive structural complexity in widget-tree construction     |
| Mutable View  | State mutation during UI declaration or rendering-related logic |
| Blocking View | Blocking or expensive work embedded in UI-related code          |

The detailed operational definitions, positive-label criteria, negative-label criteria, borderline cases, and examples are documented in:

```text
docs/annotation_guideline.md
```

---

## 3. Candidate Project Collection

### 3.1 Source Domain

The dataset was constructed from **open-source Flutter projects** obtained from GitHub. Flutter was selected as the target environment because it is a widely used declarative UI framework and provides a practical setting for observing state-driven and widget-structure-related code smells.

### 3.2 Candidate Pool

A total of **57 candidate Flutter projects** were initially collected. These candidate projects formed the source pool for later sampling.

The full candidate-project metadata is intended to be documented in:

```text
metadata/candidate_projects.csv
```

### 3.3 Project-Size Characterization

To capture variation across small, medium, and large Flutter codebases, project size was characterized using **Lines of Code (LoC)**. LoC was used as the stratification basis during project sampling.

---

## 4. Stratified Random Sampling of Source Projects

### 4.1 Sampling Rationale

Rather than selecting source projects arbitrarily, the DUIS Dataset uses a **stratified random sampling strategy based on Lines of Code (LoC)**. The purpose was to represent a broad range of project scales and reduce the risk that the dataset would be dominated by only very small or very large applications.

### 4.2 Percentile-Based Selection

Five projects were selected from the candidate pool at representative LoC positions:

* 10th percentile
* 30th percentile
* 50th percentile
* 70th percentile
* 90th percentile

This approach provides coverage across the project-size distribution while keeping manual annotation feasible.

### 4.3 Selected Projects

The five selected Flutter projects are shown below.

| Project                   | Lines of Code (LoC) | Number of Samples | Percentile |
| ------------------------- | ------------------: | ----------------: | ---------: |
| `flutter-task-manager`    |               1,132 |                43 |         10 |
| `instagram-flutter-clone` |               2,444 |                95 |         30 |
| `mobile-iot-device`       |               5,240 |               275 |         50 |
| `Timecop`                 |              10,120 |               264 |         70 |
| `air-controller-desktop`  |              31,268 |             1,220 |         90 |

The selected-project summary is intended to be documented in:

```text
metadata/selected_projects.csv
```

---

## 5. Code Snippet Extraction

### 5.1 Extraction Goal

The goal of snippet extraction was to convert selected Flutter source-code repositories into **annotation-ready code units** suitable for declarative UI smell assessment.

### 5.2 Snippet Granularity

The final dataset consists of **1,896 code snippets**. Depending on the original source structure, an extracted snippet may correspond to:

* A class
* A method or function
* A widget declaration
* Another UI-related component relevant to the annotation process

The snippet-level representation was used because the smell assessment focuses on localized code patterns rather than entire repositories.

### 5.3 Extracted Content

Each extracted sample retains:

* A unique `sample_id`
* The original `code_snippet`
* Associated metric values generated during the dataset preparation process

After annotation and consensus labeling, each sample also receives:

* `nested_view`
* `mutable_view`
* `blocking_view`

The released field structure is documented in:

```text
docs/data_schema.md
```

---

## 6. Annotation Setup

### 6.1 Annotator Profile

The annotation process was conducted by **five professional mobile application developers** working in technology companies and startups in Indonesia.

The annotator group included:

* Two junior mobile programmers
* Three senior mobile developers

Each annotator had at least **three years of relevant mobile software development experience**.

### 6.2 Annotation Design

All **1,896 snippets** were independently assessed by all five annotators for the presence or absence of:

* Nested View
* Mutable View
* Blocking View

This means that every snippet received five independent judgments for each target smell.

### 6.3 Multi-Label Annotation

The annotation task followed a **multi-label setting**. A snippet could be labeled as:

* Clean for all three smell types
* Positive for one smell type
* Positive for two smell types
* Positive for all three smell types

The smell categories were evaluated independently and were not treated as mutually exclusive classes.

### 6.4 Annotation Guideline

To support consistent interpretation, annotators were provided with an annotation guideline covering:

* Definitions of NV, MV, and BV
* Typical symptoms
* Positive and negative decision criteria
* Borderline cases
* Examples and interpretation cues

The repository version of that guideline is provided in:

```text
docs/annotation_guideline.md
```

---

## 7. Annotation Reliability Assessment

### 7.1 Purpose

Because code smell labeling can involve human judgment, the dataset construction process included an explicit **inter-rater reliability assessment** to evaluate annotation consistency.

### 7.2 Agreement Measures

Two agreement statistics were used:

| Measure            | Purpose                                      |
| ------------------ | -------------------------------------------- |
| **Cohen’s Kappa**  | Pairwise agreement between annotators        |
| **Fleiss’s Kappa** | Overall agreement across all five annotators |

Cohen’s Kappa was used first to inspect pairwise annotator consistency and identify unusual agreement patterns. Fleiss’s Kappa was then used to summarize overall agreement for each smell type.

### 7.3 Overall Fleiss’s Kappa Results

| Smell Type         | Fleiss’s Kappa | 95% Confidence Interval | Interpretation      |
| ------------------ | -------------: | ----------------------- | ------------------- |
| Nested View (NV)   |           0.85 | 0.84–0.87               | Excellent agreement |
| Mutable View (MV)  |           0.63 | 0.61–0.65               | Good agreement      |
| Blocking View (BV) |           0.50 | 0.47–0.53               | Good agreement      |

These results indicate that the annotators reached the strongest agreement for **Nested View**, while **Mutable View** and **Blocking View** also achieved acceptable agreement levels for dataset construction.

### 7.4 Pairwise Cohen’s Kappa Materials

Pairwise Cohen’s Kappa results were also examined during dataset preparation. Detailed reliability materials may be documented or released separately as supporting repository artifacts.

---

## 8. Consensus Label Generation

### 8.1 Majority Voting

The final smell labels released in the DUIS Dataset were determined using **majority voting** over the five independent annotator judgments.

For each sample and each smell type:

* The five individual annotations were collected.
* The class receiving the majority of annotator votes became the final released label.

This procedure was applied separately to:

* `nested_view`
* `mutable_view`
* `blocking_view`

### 8.2 Final Binary Labels

The released DUIS Dataset uses binary labels:

| Value | Meaning                                        |
| ----- | ---------------------------------------------- |
| `0`   | Smell absent according to the consensus label  |
| `1`   | Smell present according to the consensus label |

---

## 9. Final Dataset Composition

### 9.1 Number of Samples

The final DUIS Dataset contains:

* **1,896 Flutter code snippets**
* **Three binary smell labels per snippet**
* **Twenty extracted metric columns per snippet**

### 9.2 Label Distribution

| Smell Type         | Smelly Samples | Clean Samples | Smelly (%) | Clean (%) | Imbalance Ratio |
| ------------------ | -------------: | ------------: | ---------: | --------: | --------------: |
| Nested View (NV)   |          1,048 |           848 |       55.3 |      44.7 |        1.24 : 1 |
| Mutable View (MV)  |          1,179 |           717 |       62.2 |      37.8 |        1.61 : 1 |
| Blocking View (BV) |            391 |         1,505 |       20.6 |      79.4 |        3.85 : 1 |

The three smell-specific label distributions range from relatively balanced to moderately imbalanced, with Blocking View showing the most skewed distribution.

---

## 10. Final Release Assembly

### 10.1 Working Consensus Files

During dataset preparation, smell-specific consensus-labeled files were maintained separately:

```text
rawdata_nv.csv
rawdata_mv.csv
rawdata_bv.csv
```

Each file contains:

* `sample_id`
* `code_snippet`
* one binary label column for the corresponding smell
* the same 20 metric columns

### 10.2 Public Unified Release

For the repository release, the smell-specific consensus files are merged into one **multi-label dataset** with three label fields:

| Source Working File | Original Label Meaning | Released Field  |
| ------------------- | ---------------------- | --------------- |
| `rawdata_nv.csv`   | Nested View label      | `nested_view`   |
| `rawdata_mv.csv`   | Mutable View label     | `mutable_view`  |
| `rawdata_bv.csv`   | Blocking View label    | `blocking_view` |

The public dataset is planned to be distributed as:

```text
data/
├── duis_dataset.csv
└── duis_dataset.jsonl
```

### 10.3 Relationship to Repository Documentation

| Document                       | Purpose                                                     |
| ------------------------------ | ----------------------------------------------------------- |
| `README.md`                    | High-level overview of the dataset and repository           |
| `docs/annotation_guideline.md` | Operational smell definitions and annotation interpretation |
| `docs/data_schema.md`          | Final dataset field schema and format details               |
| `docs/dataset_construction.md` | End-to-end construction process and dataset provenance      |

---

## 11. Reproducibility and Transparency Notes

The repository documentation is designed to support transparency in the following areas:

* Why the three target smells were selected
* How source projects were chosen
* How code snippets were extracted and represented
* Who performed the annotation
* How annotation reliability was assessed
* How final binary labels were generated
* How the public release schema was assembled

The construction artifacts and metadata files associated with these stages are intended to make the dataset easier to inspect, reuse, and extend in future work.

---

## 12. Summary

The DUIS Dataset construction process can be summarized as follows:

1. Define three declarative UI smell types: NV, MV, and BV.
2. Collect 57 candidate open-source Flutter projects.
3. Select five representative source projects using LoC-based stratified random sampling.
4. Extract 1,896 annotation-ready Flutter code snippets.
5. Obtain independent labels from five professional mobile developers.
6. Evaluate annotation consistency using Cohen’s Kappa and Fleiss’s Kappa.
7. Derive final binary labels through majority voting.
8. Merge smell-specific consensus data into one reusable multi-label dataset released as CSV and JSONL.
