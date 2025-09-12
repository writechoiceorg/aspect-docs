---
title: Starlark BUILD file generators overview
description: Learn about the Starlark extensions for the Aspect CLI, which allow you to generate BUILD files.
slug: starlark-build-generators
---

# Starlark BUILD file generators overview

The Aspect CLI can write BUILD file generation extensions in Starlark. These extensions run when you use the `aspect configure` command.

:::warning
This feature is experimental. The Starlark API may change without notice.
:::

## Starlark-based Gazelle extensions

The `aspect` CLI provides a new way to write Gazelle extensions using Starlark. This approach has many benefits over traditional Go extensions, including a better developer experience, improved portability, and an easier-to-use API.

## Why use a Starlark extension?

Using Starlark for Gazelle extensions offers several benefits:

* **Faster development**: Starlark is an interpreted language. You do not need to recompile a binary when you change your code. This removes a major point of friction for developers.
* **Improved portability**: Some Go extensions, such as `rules_python`, use `CGo` (C-Go interoperability). This requires a C++ toolchain on the user's machine, which can be hard to set up. Starlark extensions avoid this issue.
* **Built for Bazel**: Starlark is the native language for Bazel extensibility. This allows you to share logic between rule implementations and `BUILD` file generators. You can also easily turn a user-facing macro into a generator that writes targets directly to a `BUILD` file.
* **Wider familiarity**: Most Bazel developers understand Starlark. This makes the code more readable and accessible than Go.
* **Easier customization**: You can easily customize a Starlark extension for a repository's needs. This reduces the use of "directives" (load-bearing comments). Directives are often missed and lack syntax highlighting.
* **User-friendly API**: The API for Starlark extensions is easy for new users to learn. This is a big improvement over the low-level API of Go extensions, which can be complex.

---

## Technical design

The `aspect` CLI embeds a Starlark interpreter. This interpreter acts as a Gazelle extension. A new symbol, `aspect`, is exposed inside the interpreter, giving you access to the API.

This design allows existing Go extensions to work with Starlark extensions. Currently, other Go extensions must be compiled into the `aspect` binary. However, a future Gazelle update is expected to allow pre-compiled Go extensions to work with `aspect configure`.