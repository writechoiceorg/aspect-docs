---
title: Aspect CLI overview
description: The Aspect CLI is a Bazel wrapper designed to improve developer experience and add extensibility.
slug: aspect-cli
---

# Aspect CLI overview

Aspect CLI is a Bazel wrapper developed atop Bazelisk. A development created to enhance the functionality of Bazel and introduce additional features and extensibility.

## The problem it solves

Bazel started as a tool for Google's internal workflow. Most other companies have bespoke wrappers and scripts. This makes Bazel a good fit within their tech stack and engineering culture. These scripts then become unmaintainable. Each team spends the time building and maintaining its own solution. This is a source of developer frustration and technical debt. 

## The solution

The Aspect CLI provides a robust, open-source option. Instead of patching together bash scripts, businesses can use the Aspect CLI. It is a reliable and adaptable replacement. It improves developer experience and reduces the usage of tailored scripts.

## What it does

The Aspect CLI runs all standard Bazel commands. It also adds new commands, such as `print` and `docs`. It offers extensibility mechanisms. These mechanisms allow developers to create plugins that can automate routine tasks.

## Installation and availability

Aspect CLI is available on macOS and Linux. A Windows distribution will be released. The source code resides on GitHub with an Apache 2 license.