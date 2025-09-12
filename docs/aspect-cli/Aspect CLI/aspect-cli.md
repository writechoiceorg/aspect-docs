---
title: Aspect CLI overview
description: The Aspect CLI is a Bazel wrapper designed to improve developer experience and add extensibility.
slug: aspect-cli
---

# Aspect CLI overview

The Aspect CLI is a Bazel wrapper built on top of Bazelisk. Created to improve Bazel’s functionality and add new features and extensibility.

## The problem it solves

Bazel was designed for Google's internal workflows. Outside of Google, many companies create custom wrappers and scripts. This adapts Bazel to their engineering culture and tech stack. These scripts often become difficult to maintain. Each team spends time creating and supporting its own solution. This leads to developer frustration and technical debt.

## The solution

The Aspect CLI provides a robust, open-source solution. Instead of using improvised bash scripts, organizations can use the Aspect CLI. It is a stable and extensible alternative. It improves the developer experience and reduces the need for custom scripts.

## What it does

The Aspect CLI runs all standard Bazel commands. It also adds new commands, such as `print` and `docs`. It offers extensibility features. These features allow developers to create plugins to automate common tasks.

## Installation and availability

The Aspect CLI is available for macOS and Linux. A Windows version is planned. The source code is on GitHub under an Apache 2 license.