---
title: The `aquery` command
description: Queries the action graph to analyze a build.
slug: aquery
---

# The `aquery` command

The `aquery` command executes a query over a subgraph of the action graph to analyze a build. You can use a full query expression or a preset query name. Some preset queries accept parameters like target labels. If you don't provide these arguments in an interactive session, the system will prompt you for them.

## Usage

```sh
aspect aquery [expression | <preset name> [arg ...]] [flags]
```

## Examples

To get the action graph for a single target, use:

```sh
aspect aquery '//src/target_a'
```

To get the action graph for all dependencies of a target, use:

```sh
aspect aquery 'deps(//src/target_a)'
```

To get the action graph for dependencies with specific file inputs, use:

```sh
aspect aquery 'inputs(".*cpp", deps(//src/target_a))'
```

## Options

### Command-specific options

| Flag | Description |
| :--- | :--- |
| `-h`, `--help` | Displays help for the `aquery` command. |

### Inherited options

These options work with any `aspect` command, including `aquery`.

| Flag | Description |
| :--- | :--- |
| `--aspect:config string` | Specifies an Aspect CLI configuration file. Using `/dev/null` will ignore any subsequent `--aspect:config` flags. |
| `--aspect:hints` | Enables hints if configured (default: `true`). |
| `--aspect:interactive` | Enables interactive mode, such as prompting for user input. |

-----

## Related Resources

  * [Aspect CLI Commands](https://www.google.com/search?q=/reference/cli-commands)