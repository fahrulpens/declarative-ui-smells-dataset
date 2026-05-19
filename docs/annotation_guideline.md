# DUIS Dataset Annotation Guideline

This document provides the annotation guideline for the **DUIS Dataset** (**Declarative UI Smell Dataset**). It defines the operational criteria used to identify three declarative UI code smells in Flutter source-code snippets:

* **Nested View (NV)**
* **Mutable View (MV)**
* **Blocking View (BV)**

The guideline is intended to help readers understand the meaning of each released label, support consistent interpretation of the dataset, and provide a reusable reference for future annotation or extension studies.

> **Terminology note:** Earlier working materials may use the term **Expensive View**. In this repository and in the associated SIML 2026 paper, the official term is **Blocking View (BV)**.

---

## 1. Purpose and Scope

### 1.1 Purpose

The DUIS Dataset was created to support empirical research on code smells that arise in **declarative UI programming**, particularly in Flutter applications. Unlike classical object-oriented code smells, the smells covered here are tied to issues that emerge from:

* Declarative widget-tree construction
* State-driven rendering
* Rendering-time side effects
* Blocking or expensive operations placed in UI-related code

This document serves as the **canonical interpretation guide** for the three smell labels released in the dataset.

### 1.2 Target Programming Context

The annotation guideline focuses on:

* **Language:** Dart
* **Framework:** Flutter
* **Programming paradigm:** Declarative UI

The smells are described using Flutter terminology because the DUIS Dataset is built from Flutter projects. However, the conceptual ideas may also be relevant to other declarative UI frameworks.

---

## 2. Annotation Unit and Labeling Scheme

### 2.1 Annotation Unit

Each annotation unit is a **Flutter source-code snippet** extracted from a selected open-source project. Depending on the extraction context, a snippet may represent:

* A class
* A method or function
* A widget declaration
* A UI-related component or code unit relevant to smell assessment

A snippet is not necessarily a complete standalone Dart file. It is an extracted unit of source code used for smell annotation and dataset construction.

### 2.2 Multi-Label Setting

Each snippet is assessed **independently for all three smell types**. The smells are not mutually exclusive.

A single snippet may contain:

* None of the smells
* Exactly one smell
* Two smells
* All three smells

### 2.3 Public Label Representation

The released DUIS Dataset uses **binary labels**.

| Smell         | Dataset Field   | Label Values |
| ------------- | --------------- | ------------ |
| Nested View   | `nested_view`   | `0` or `1`   |
| Mutable View  | `mutable_view`  | `0` or `1`   |
| Blocking View | `blocking_view` | `0` or `1`   |

| Value | Meaning                                                                                                                   |
| ----- | ------------------------------------------------------------------------------------------------------------------------- |
| `0`   | The snippet does not provide sufficient observable evidence that the corresponding smell is present under this guideline. |
| `1`   | The snippet provides sufficient observable evidence that the corresponding smell is present under this guideline.         |

---

## 3. General Annotation Principles

The following principles apply to all three smell types.

### 3.1 Assess Each Smell Independently

Annotators should evaluate **Nested View**, **Mutable View**, and **Blocking View** as separate classification decisions. The presence of one smell does not automatically imply the presence of another.

### 3.2 Use Observable Evidence in the Snippet

Labels should be based on evidence visible in the extracted code snippet. When contextual information is missing, the decision should rely on what can reasonably be inferred from the available code unit.

### 3.3 Interpret Metrics and Patterns as Supporting Cues

Indicators such as nesting depth, widget count, mutation operations, or expensive API calls can help identify likely smells. However, these indicators are **supporting cues**, not automatic labeling rules by themselves.

A snippet should not be labeled positive solely because a metric is high or a certain token appears. The observed code should still fit the operational smell definition.

### 3.4 Use Positive Labels Only When the Smell Definition Is Met

A label of `1` should be assigned only when the snippet exhibits a pattern that clearly matches the corresponding smell definition. Borderline or weak cases should be judged conservatively.

### 3.5 Use Negative Labels When Evidence Is Insufficient

A label of `0` does not necessarily mean the broader project is free from the smell. It means that the **specific snippet** does not provide enough evidence to classify the smell as present under this guideline.

### 3.6 Avoid Treating Ordinary Implementation Details as Smells

Not every instance of nesting, mutation, or computation is problematic. The annotation should focus on patterns that indicate a likely design or quality concern in declarative UI code.

---

## 4. Smell Overview

| Smell                  | Core Concern                                           | Typical High-Level Evidence                                                                       |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------- |
| **Nested View (NV)**   | Excessive structural complexity in the widget tree     | Deep nesting, long wrapper chains, structurally burdensome UI composition                         |
| **Mutable View (MV)**  | State mutation during rendering or UI declaration      | `setState`, shared-state writes, render-time side effects, collection mutation                    |
| **Blocking View (BV)** | Blocking or expensive work embedded in UI-related code | Sync I/O, heavy computation, database/network work, expensive transforms during view construction |

---

## 5. Nested View (NV)

### 5.1 Definition

**Nested View** refers to **excessive structural nesting in declarative UI code**, especially in widget-tree construction, such that the hierarchy becomes difficult to read, inspect, modify, or maintain.

The key issue is not simply that a widget contains child widgets. Nesting is fundamental to Flutter. The smell appears when nesting becomes **structurally burdensome** and the UI declaration accumulates excessive hierarchy or wrapper depth without sufficient decomposition.

### 5.2 Conceptual Intent

Declarative UI encourages composition, but composition should remain understandable. Nested View captures cases where the widget structure becomes so deep or intertwined that:

* Readability declines
* Layout intent becomes difficult to follow
* Future changes become fragile
* Widget subtrees should likely have been decomposed into smaller units

### 5.3 Positive Decision Criteria

Label a snippet as `nested_view = 1` when it shows **clear evidence** of excessive widget-tree complexity, such as one or more of the following:

1. **Deep widget nesting** that materially increases reading difficulty.
2. **Long chains of wrapper widgets**, especially when several single-child wrappers are stacked.
3. **Large monolithic UI declarations** containing many nested widgets in one code unit.
4. **Conditional or branching UI logic mixed into an already deeply nested structure**, making the hierarchy difficult to trace.
5. A structure that would reasonably benefit from decomposition into smaller widgets or helper methods.

### 5.4 Negative or Non-Smelly Cases

Do **not** label a snippet as Nested View merely because:

* It contains several widgets.
* It uses common Flutter composition patterns.
* It has moderate nesting that remains easy to follow.
* It contains a `Column`, `Row`, or `Container` with a small and readable subtree.
* It appears visually nested but remains structurally straightforward.

A snippet should be labeled `0` when the hierarchy does not provide sufficient evidence of excessive structural burden.

### 5.5 Typical Symptoms

Common observable patterns include:

* Deeply indented widget trees
* Repeated nesting of `Padding`, `Align`, `SizedBox`, `Container`, `Expanded`, `Center`, or similar wrapper widgets
* A long `child:` chain that keeps extending downward
* A single `build()` or UI-related method that constructs a very large layout subtree
* UI branches or ternary expressions embedded deep within nested layout structures

### 5.6 Potential Quality Impact

Nested View may negatively affect:

* **Maintainability:** UI logic becomes harder to inspect and modify
* **Readability:** The actual layout intention is obscured by structural depth
* **Testability:** Subtrees are harder to isolate and validate
* **Change safety:** Small modifications can become error-prone in large nested structures
* **Performance risk:** Highly complex view construction may increase rendering or rebuild burden

### 5.7 Supporting Indicators

The following indicators can support assessment, but they should not be used mechanically as standalone rules.

| Indicator               | Meaning                                                                   |
| ----------------------- | ------------------------------------------------------------------------- |
| Nesting depth           | The maximum hierarchical depth in code or widget structure                |
| Wrapper-chain length    | The number of sequential nested wrapper widgets                           |
| Widget count            | The amount of widget construction concentrated in one snippet             |
| Render-logic complexity | The amount of conditional or branching logic interwoven with UI structure |

These ideas conceptually align with dataset metrics such as maximum nesting depth, widget-related counts, and wrapper-chain indicators.

### 5.8 Borderline Cases and Cautions

Use additional care in cases such as:

* **Builder patterns:** A builder may visually appear nested but remain appropriate.
* **Generated code:** Generated code may be structurally large but should not automatically be treated as a manually introduced design smell.
* **Framework-required wrapping:** Some nesting may be justified by Flutter layout constraints.
* **Domain-rich layout:** A complex screen may legitimately contain many widgets, but the smell should depend on the burden of the structure, not only on size.

### 5.9 Example of a Smelly Pattern

```dart
@override
Widget build(BuildContext context) {
  return Padding(
    padding: const EdgeInsets.all(16),
    child: Align(
      alignment: Alignment.topCenter,
      child: SizedBox(
        width: 400,
        child: Container(
          child: Column(
            children: [
              Row(
                children: [
                  Expanded(
                    child: Container(
                      child: Text(title),
                    ),
                  ),
                ],
              ),
            ],
          ),
        ),
      ),
    ),
  );
}
```

This pattern illustrates a deeply nested wrapper chain that can reduce readability and suggests a need for decomposition.

### 5.10 Refactoring Direction

Typical improvement strategies include:

* Extracting subtrees into dedicated widgets
* Flattening unnecessary wrapper chains
* Separating layout composition from nested conditional logic
* Replacing oversized UI methods with smaller, composable units
* Using more expressive layout abstractions where possible

---

## 6. Mutable View (MV)

### 6.1 Definition

**Mutable View** refers to **state mutation performed inside UI-declaration logic or rendering-related code**, thereby violating the declarative principle that the view should be derived from state rather than mutating state during view construction.

The smell appears when rendering-related code introduces side effects that can destabilize UI behavior, create hidden dependencies, or cause rebuild-related problems.

### 6.2 Conceptual Intent

Declarative UI should ideally be:

* Predictable
* Idempotent with respect to rendering
* Driven by existing state
* Free from unnecessary side effects during view construction

Mutable View identifies snippets where this principle is weakened because the UI-producing code also changes state.

### 6.3 Positive Decision Criteria

Label a snippet as `mutable_view = 1` when it shows **clear evidence** of state-changing behavior tied to UI declaration or rendering-related code, such as:

1. Calling `setState(...)` inside a rendering-related method.
2. Writing to shared state, providers, blocs, notifiers, or similar state holders during view construction.
3. Mutating collections that are directly involved in rendering logic.
4. Assigning to persistent object fields in a way that introduces render-time side effects.
5. Creating or mutating controllers, timers, or streams as part of view construction logic.

### 6.4 Negative or Non-Smelly Cases

Do **not** label a snippet as Mutable View merely because:

* It contains local variable assignment.
* It performs ordinary temporary computation before returning a widget.
* It initializes a local constant or derived value.
* It reads state without modifying it.
* It contains an event callback that changes state, when the mutation is clearly executed only in response to a user action rather than during rendering itself.

A snippet should be labeled `0` when there is insufficient evidence of render-time or UI-declaration-time state mutation.

### 6.5 Typical Symptoms

Common observable patterns include:

* `setState(() { ... })` inside `build()` or closely related UI-declaration logic
* `provider.value = ...` or analogous state writes during view construction
* `bloc.add(...)`, notifier writes, or similar update operations embedded in UI rendering logic
* Mutating lists, maps, or other collections used by the view
* Assigning to `this.field`, `widget.field`, or shared-state objects while building the UI

### 6.6 Potential Quality Impact

Mutable View may negatively affect:

* **Correctness:** Rendering may become non-idempotent or unstable
* **Maintainability:** State change is hidden inside view-construction code
* **Predictability:** Rebuild behavior becomes harder to reason about
* **Performance:** Side effects may trigger unnecessary state updates or rebuild loops
* **Debuggability:** Errors become difficult to trace because rendering and mutation are interwoven

### 6.7 Supporting Indicators

The following indicators can support assessment:

| Indicator                     | Meaning                                                   |
| ----------------------------- | --------------------------------------------------------- |
| `setState` calls              | Direct evidence of Flutter state mutation                 |
| Provider/Bloc/Riverpod writes | Evidence of mutation of shared or reactive state          |
| Field assignments             | Persistent object-level state modifications               |
| Collection mutation           | Structural changes to mutable data consumed by the UI     |
| Timer/controller/stream setup | Side-effectful initialization placed in rendering context |

These cues conceptually align with state-mutation-related dataset metrics.

### 6.8 Borderline Cases and Cautions

Use additional care in cases such as:

* **Local temporary mutation:** A local list transformation may not represent a smell unless it mutates shared render state.
* **Callbacks:** State mutation inside `onPressed`, `onTap`, or similar event handlers is usually not Mutable View by itself.
* **Initialization patterns:** Some snippets may resemble mutation but execute outside render-time flow; judgment should follow observable code context.
* **Dependency injection or service access:** Apparent writes should be evaluated by whether they constitute actual UI-declaration-time state mutation.

### 6.9 Example of a Smelly Pattern

```dart
@override
Widget build(BuildContext context) {
  appState.counter++;
  setState(() {});
  provider.user = provider.user.copyWith(isActive: true);

  return Text('${appState.counter}');
}
```

This pattern mutates state during UI construction and can produce unstable or repeated rebuild effects.

### 6.10 Refactoring Direction

Typical improvement strategies include:

* Moving state writes into user-event handlers
* Performing initialization in lifecycle-aware methods such as `initState()` when appropriate
* Using effects, controllers, or state-management layers outside the rendering method
* Ensuring that view construction derives UI from state rather than mutating state
* Keeping render-time code as side-effect free as possible

---

## 7. Blocking View (BV)

### 7.1 Definition

**Blocking View** refers to **blocking, expensive, or computationally heavy operations embedded in UI-declaration or rendering-related code**, where lightweight and responsive rendering would normally be expected.

The smell covers operations that may degrade UI responsiveness, delay view construction, or introduce avoidable work during rendering.

### 7.2 Conceptual Intent

Declarative UI rendering should remain lightweight. Heavy work is better placed in:

* Initialization stages
* Asynchronous loading flows
* View models or controllers
* Background tasks or isolates when appropriate
* Cached or precomputed execution paths

Blocking View identifies code that violates this principle by placing costly work where view rendering should remain efficient.

### 7.3 Positive Decision Criteria

Label a snippet as `blocking_view = 1` when it shows **clear evidence** of blocking, expensive, or inappropriate heavy work in UI-related code, such as:

1. Synchronous I/O or process-like blocking operations inside view construction logic.
2. Heavy loops, sorting, parsing, decoding, or transformations directly embedded in rendering-related code.
3. Database, storage, or network operations initiated from UI-declaration logic.
4. Expensive image-processing, cryptographic, or serialization work performed in a view-construction context.
5. Timers or streams created during rendering in a way that introduces repeated overhead or misplaced execution burden.

### 7.4 Negative or Non-Smelly Cases

Do **not** label a snippet as Blocking View merely because:

* It contains an `async` keyword.
* It contains an API call outside the relevant UI-construction context.
* It performs simple arithmetic or lightweight formatting.
* It creates a simple local value needed for rendering.
* It references a potentially expensive function without enough evidence that the work is actually executed as part of the relevant rendering logic.

A snippet should be labeled `0` when there is not enough observable evidence of expensive or blocking work in the UI-related execution context.

### 7.5 Typical Symptoms

Common observable patterns include:

* `readAsBytesSync`, `readAsStringSync`, or other synchronous file operations
* `sleep(...)` or similarly blocking operations
* Database/storage access initiated while building UI
* Network requests triggered directly from rendering-related code
* Sorting large collections or running loops to prepare display data during view construction
* Image decoding, encoding, or other heavy transformations in a render-oriented method
* Timer or stream initialization embedded in UI-building logic

### 7.6 Potential Quality Impact

Blocking View may negatively affect:

* **Performance:** Rendering becomes slower or more computationally expensive
* **Responsiveness:** Input delay, frame drops, or visible jank may appear
* **User experience:** UI transitions and interactions may feel sluggish
* **Energy efficiency:** Redundant heavy work in render paths can increase CPU or I/O cost
* **Maintainability:** Performance-relevant work becomes scattered into presentation logic

### 7.7 Supporting Indicators

The following categories can support assessment:

| Category              | Example Evidence                                                     |
| --------------------- | -------------------------------------------------------------------- |
| Synchronous I/O       | Blocking file or process operations                                  |
| Network/API work      | HTTP, Dio, WebSocket, or similar calls initiated in UI logic         |
| Database/storage work | Local storage or database operations in rendering-related code       |
| Heavy transformation  | Parsing, decoding, sorting, data transformation, or expensive loops  |
| Timer/stream setup    | Creation of recurring or asynchronous flows during view construction |

These categories align conceptually with the dataset metrics related to API calls, database operations, synchronous I/O, image codec activity, asynchronous UI-related operations, and timer/stream creation.

### 7.8 Borderline Cases and Cautions

Use additional care in cases such as:

* **Asynchronous operations:** An async call may be scheduled rather than completed during rendering, so context matters.
* **Cached operations:** A function that looks expensive may be cheap in a particular implementation.
* **Small data transformations:** A small sort or lightweight mapping operation may not be sufficient evidence of smell by itself.
* **Names alone are not enough:** Do not rely only on function or variable names; use the surrounding code to determine likely runtime burden.

### 7.9 Example of a Smelly Pattern

```dart
@override
Widget build(BuildContext context) {
  final bytes = File('assets/banner.png').readAsBytesSync();
  final sortedItems = List.of(items)
    ..sort((a, b) => a.score.compareTo(b.score));
  final decoded = decodeImage(bytes);

  return PreviewPanel(
    items: sortedItems,
    image: decoded,
  );
}
```

This pattern places blocking I/O and potentially expensive processing directly in UI construction logic.

### 7.10 Refactoring Direction

Typical improvement strategies include:

* Moving expensive work outside rendering methods
* Performing asynchronous loading through state-managed workflows
* Precomputing values in controllers, blocs, view models, or equivalent layers
* Caching or memoizing costly computations
* Offloading heavy work when appropriate
* Keeping UI declaration focused on presentation rather than data acquisition or expensive processing

---

## 8. Multi-Label Interpretation and Overlap

The three smells are distinct but can co-occur in the same snippet.

### 8.1 Possible Co-Occurrence Examples

* A deeply nested widget tree that also mutates state during rendering may be labeled with both **Nested View** and **Mutable View**.
* A large UI method that performs a synchronous file operation while constructing widgets may be labeled with both **Nested View** and **Blocking View**.
* A snippet that mutates state and performs expensive work during rendering may be labeled with both **Mutable View** and **Blocking View**.

### 8.2 No Forced Dependency

The presence of one smell should not force a positive label for another smell. Every label must be justified independently by the corresponding definition.

---

## 9. Annotation Decision Summary

| Smell         | Label as `1` when...                                                             | Do not label as `1` merely because...                                                           |
| ------------- | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| Nested View   | The widget structure is excessively deep, overloaded, or structurally burdensome | The snippet contains several widgets or ordinary layout nesting                                 |
| Mutable View  | State-changing work is performed in rendering or UI-declaration logic            | A local temporary variable is assigned or state is only read                                    |
| Blocking View | Blocking, heavy, or performance-risky work is embedded in UI-related code        | An async keyword, API name, or computation token appears without sufficient contextual evidence |

---

## 10. Severity Cues and Public Dataset Labels

Earlier working guidelines may discuss qualitative severity cues such as:

* Minor
* Major
* Critical

These severity ideas can be useful for understanding the relative seriousness of a smell occurrence. However, the **public DUIS Dataset release documented in this repository uses binary labels only**:

* `0` = absent
* `1` = present

Severity categories are therefore **not part of the released dataset fields** unless explicitly introduced in a future version.

---

## 11. Relationship to Dataset Fields

The released DUIS Dataset uses the following label fields:

| Smell Name    | Dataset Field   |
| ------------- | --------------- |
| Nested View   | `nested_view`   |
| Mutable View  | `mutable_view`  |
| Blocking View | `blocking_view` |

For the complete dataset schema, including metric fields and file formats, see:

```text
docs/data_schema.md
```

---

## 12. Recommended Use of This Guideline

This guideline should be used as:

* The primary reference for interpreting DUIS Dataset smell labels
* A public explanation of the operational smell definitions used in the repository
* A basis for reproducing or extending the annotation process
* A reference for readers who need to understand why a snippet may be considered positive or negative for a given smell

For the broader dataset construction process, see:

```text
docs/dataset_construction.md
```
