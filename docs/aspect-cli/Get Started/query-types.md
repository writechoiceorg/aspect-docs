---
title: File Query Types
description: A reference guide to the different methods for querying source files and extracting information for analysis.
slug: file-query-types
---

# File query types

This document is a reference that describes the different methods for querying source files and extracting information for analysis.

---

## AstQuery

| Field | Type | Required | Description |
|---|---|---|---|
| `filter` | `glob pattern` | Yes | A glob pattern to match file names to be queried. |
| `grammar` | [tree-sitter grammar](https://tree-sitter.github.io/tree-sitter/syntax-highlighting.html#installation-and-usage) | Optional | The `tree-sitter` grammar to parse source code (the default is based on the file extension). |
| `query` | [tree-sitter query](https://tree-sitter.github.io/tree-sitter/using-parsers.html#pattern-matching-with-queries) | Yes | A `tree-sitter` query to run on the source code AST. |
| **Returns** | List of `QueryMatch` | — | A list of [QueryMatch](#querymatch-details) objects for each corresponding AST node. |

---

## RegexQuery

| Field | Type | Required | Description |
|---|---|---|---|
| `filter` | `glob pattern` | Yes | A glob pattern to match file names to be queried. |
| `expression` | `regular expression` | Yes | A regular expression to run on the file. |
| **Returns** | List of `QueryMatch` | — | A list of [QueryMatch](#querymatch-details) objects for each match in the file. |

---

## RawQuery

| Field | Type | Required | Description |
|---|---|---|---|
| `filter` | `glob pattern` | Yes | A glob pattern to match file names to be returned. |
| **Returns** | `file content` | — | The file content as-is, with no parsing or filtering. |

---

## JsonQuery

| Field | Type | Required | Description |
|---|---|---|---|
| `filter` | `glob pattern` | Yes | A glob pattern to match file names to be queried. |
| `query` | [JQ filter expression](https://stedolan.github.io/jq/) | Yes | A JQ filter expression to run on the JSON document. |
| **Returns** | List of `JSON nodes` | — | A list of each corresponding JSON node in the document. |

---

## YamlQuery

| Field | Type | Required | Description |
|---|---|---|---|
| `filter` | `glob pattern` | Yes | A glob pattern to match file names to be queried. |
| `query` | [YQ filter expression](https://mikefarah.gitbook.io/yq/) | Yes | A YQ filter expression to run on the YAML document. |
| **Returns** | List of `YAML nodes` | — | A list of each corresponding YAML node in the document. |

---

## QueryMatch details

| Field | Type | Required | Description |
|---|---|---|---|
| `result` | string | No | The matched content from the source file, such as raw text. |
| `captures`| `name:value map` | No | A map of the name and value of the query captures. |