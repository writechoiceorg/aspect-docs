---
title: Extension Stages Reference
description: A reference guide to the stages that extensions can hook into when the Aspect CLI generates BUILD files.
slug: extension-stages
---

# Aspect CLI Extension Stages Reference

This document is a reference for the stages extensions can hook into when the Aspect CLI generates BUILD files.

---

## Stages

### Prepare

The `Prepare` stage declares which files an extension processes and which queries it runs on those files.

**Function signature:** `Prepare(ctx PrepareContext) PrepareResult`

#### `PrepareContext`

The context for a `Prepare` invocation.

| Property | Type | Description |
| :--- | :--- | :--- |
| `repo_name` | `string` | The name of the Bazel repository. |
| `rel` | `string` | The directory being prepared relative to the repository root. |
| `properties` | `map` | A map of extension property values configured in `BUILD` files via `# aspect:{name} {value}`. |

#### `aspect.PrepareResult`

The factory method for a `Prepare` result.

**Arguments:**

| Field | Description |
| :--- | :--- |
| `sources` | An object or a list of objects that can be `SourceFiles`, `SourceExtensions`, or `SourceGlobs`. |
| `queries` | A map of queries to run on matching files, formatted as a `name:aspect.*Query`. |

---

### Source matchers

These objects are used within the `Prepare` stage to match files.

| Source Matcher | Description | Arguments |
| :--- | :--- | :--- |
| `aspect.SourceFiles` | Matches specific file paths. | `files...` (string) |
| `aspect.SourceExtensions` | Matches files with trailing extensions. The extension string must include the leading dot. | `exts...` (string) |
| `aspect.SourceGlobs` | Matches files that fit glob patterns. Note that these are significantly slower than exact paths or extension-based matchers. | `patterns...` (string) |

:::warning
Glob patterns are significantly slower than exact paths or extension-based matchers.
:::

---

### Analyze

The `Analyze` stage analyzes source code query results and can declare symbols that rules can import.

The **Analyze** stage lets an extension analyze source code query results and declare symbols that rules can import. The function signature is `Analyze(ctx AnalyzeContext) error`.

| Property | Type | Description |
| :--- | :--- | :--- |
| `source` | `aspect.TargetSource` | Metadata about the source file being analyzed. |

#### `add_symbol` Method

The `add_symbol(id, provider_type, label)` method adds a symbol to the symbol database.

| Field | Description |
| :--- | :--- |
| `id` | The symbol identifier. |
| `provider_type` | The provider's type (e.g., `java_info` for Java packages). |
| `label` | The Bazel label that produces the symbol. |
---

### Declare targets

The **`DeclareTargets`** stage is responsible for declaring the targets that will be generated in the `BUILD` file. Its function signature is `DeclareTargets(ctx DeclareTargetsContext) DeclareTargetsResult`.

The stage operates using the **`DeclareTargetsContext`**, which provides the context for each invocation.

| Property | Type | Description |
| :--- | :--- | :--- |
| `repo_name` | `string` | The name of the Bazel repository. |
| `rel` | `string` | The directory being prepared relative to the repository root. |
| `properties` | `map` | A map containing extension property values configured within the `BUILD` files. |
| `sources` | `list` | A list of `aspect.TargetSources` that are ready for processing, based on the results from the `Prepare` stage. |
| `targets` | `aspect.DeclareTargetActions` | A set of actions available to modify targets in the `BUILD` file. |

---

### DeclareTarget actions

These actions are used to add or remove targets for a `BUILD` file during the `DeclareTargets` stage.

#### Methods

`DeclareTargetActions` provides two methods for modifying a `BUILD` file: `add` and `remove`.

**The `add` Method**

The `add` method adds a new rule to the `BUILD` file.

| Field | Description |
| :--- | :--- |
| `name` | The name of the rule. |
| `kind` | The rule kind (a native/builtin rule or one registered with `aspect.register_rule_kind`). |
| `attrs` | A map of name-value pairs for the rule's attributes. Values of type `aspect.Import` are resolved to Bazel labels. |
| `symbols` | A list of symbols exported by the rule. |

**The `remove` Method**

The `remove` method removes an existing rule from the `BUILD` file.

| Field | Description |
| :--- | :--- |
| `name` | The name of the rule to remove. |

---

### Types

This section defines the data types used in Aspect CLI extension stages.

#### `aspect.TargetSource`

Metadata about a source file being analyzed.

| Property | Type | Description |
| :--- | :--- | :--- |
| `path` | `string` | The path to the source file relative to the `BUILD` file. |
| `query_results` | `map` | A `name:result` map for each query run on this source file. |

#### `aspect.Label`

Constructs a Bazel label.

**Arguments:**

| Field | Description |
| :--- | :--- |
| `repo` | The name of the repository (optional). |
| `pkg` | The label package (optional). |
| `name` | The name of the label. |

#### `aspect.Import()`

A placeholder for a Bazel label that is resolved after the `DeclareTargets` stage.

When an attribute value is an `aspect.Import`, it is resolved after the `DeclareTargets` stage and potentially replaced with a Bazel label. If the import resolves to the same target (a self-reference), it is removed. If it does not resolve, an error is thrown unless the import is declared as optional.

**Arguments:**

| Field | Description |
| :--- | :--- |
| `id` | The symbol identifier. |
| `provider` | The symbol's type (e.g., `js` for a JS/TS extension). The imported symbol must have the same type as the rule that defines it. |
| `optional` | A boolean value. If `true`, the import is ignored if not found, preventing an error. |
| `src` | The source of the import (optional). It is used for debugging and in error messages. |
}