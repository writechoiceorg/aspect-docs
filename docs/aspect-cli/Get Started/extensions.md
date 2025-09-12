---
title: Extension Registration API
description: A reference to the Extension Registration API, detailing the functions for registering rule kinds and configure extensions, along with the use of extension properties.
slug: extensions
---

# Extension Registration API

This document is a reference for the **Aspect CLI Extension Registration API**.

---

## `aspect.register_rule_kind`

Registers a new rule kind that a `configure` extension can generate.

**Args:**

| Field | Description |
|---|---|
| `name` | The name of the rule kind. |
| `from` | The target `.bzl` file that defines the rule. |
| `NonEmptyAttrs` | A set of attributes that, if present, disqualify a rule from being deleted after a merge. |
| `MergeableAttrs` | A set of attributes that should be merged before dependency resolution. |
| `ResolveAttrs` | A set of attributes that should be merged after dependency resolution. |
---

## `aspect.register_configure_extension`

Registers a `configure` extension for generating targets in `BUILD` files.

**Args:**

| Field | Description |
|---|---|
| `name` | A unique identifier for the extension. You can reference this in the Starlark API or use it in `# aspect:{name} enabled\|disabled` directives. |
| `properties` | An optional map of `name:property` definitions. See the Extension Properties section for more details. |
| `prepare` | The optional callback for the `prepare` stage. |
| `analyze` | The optional callback for the `analyze` stage. |
| `declare` | The optional callback for the `declare` stage. |

---

## Extension properties

You can set property values in `BUILD` files using `# aspect:{name} {value}` directives. Each stage has access to the extension properties via `ctx.properties`. Property values are inherited from parent packages.

## `aspect.Property(type, default)`

Constructs a property definition.

**Args:**

| Field | Description |
|---|---|
| `type` | The property type. It must be one of `string`, `[]string`, `number`, or `bool`. |
| `default` | The optional default value for the property. |