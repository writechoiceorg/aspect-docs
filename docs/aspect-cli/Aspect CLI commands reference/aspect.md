---
title: Aspect CLI Commands
description: A reference guide to the commands and flags available in the Aspect CLI.
slug: /reference/cli-commands
---

# Aspect CLI commands

The Aspect CLI is an enhanced interface for running Bazel, providing additional commands and features.

## Usage

You can run commands using the following syntax:

```bash
aspect [command]
```

## Command categories

The Aspect CLI organizes commands into categories. For detailed information, see the individual reference pages for each command.

### Common Bazel commands

| Command     | Description                                                        |
|-------------|--------------------------------------------------------------------|
| `build`     | Compiles the specified targets.                                    |
| `cquery`    | Queries the dependency graph, respecting configuration flags.       |
| `run`       | Compiles a single target and runs it with provided arguments.      |
| `test`      | Compiles the specified targets and runs all tests.                 |
| `version`   | Displays the Aspect CLI and Bazel versions.                         |

### Aspect CLI exclusive commands

| Command      | Description                                               |
|--------------|----------------------------------------------------------|
| `configure`  | Automatically configures Bazel by updating your `BUILD` files.             |
| `docs`       | Opens documentation in your browser.                       |
| `init`       | Creates a new Bazel workspace.                            |
| `lint`       | Runs configured linters on the dependency graph.          |
| `outputs`    | Shows paths to declared output files.                     |
| `print`      | Displays syntax elements from `BUILD` files.                |

### Other commands

| Command               | Description                                                        |
|-----------------------|--------------------------------------------------------------------|
| `analyze-profile`     | Analyzes build profile data.                                        |
| `aquery`              | Queries the action graph.                                           |
| `canonicalize-flags`  | Presents Bazel options in a canonical format.                      |
| `clean`               | Removes the output tree.                                           |
| `config`              | Displays configuration details.                                       |
| `coverage`            | The same as `test`, but also generates code coverage reports.           |
| `fetch`               | Fetches external repositories required for the targets.            |
| `info`                | Shows Bazel server runtime information.                            |
| `license`             | Displays this software's license.                                  |
| `mod`                 | Provides tools for working with the bzlmod dependency graph.                 |
| `query`               | Queries the dependency graph, ignoring configuration flags.        |
| `shutdown`            | Shuts down the Bazel server.                                       |

### Additional commands

| Command       | Description                                                         |
|---------------|---------------------------------------------------------------------|
| `completion`  | Generates a shell autocomplete script.                                 |
| `explain`     | Compares two Bazel execution logs to detect hermeticity issues.      |
| `help`        | Displays help for the Aspect CLI.                                               |

-----

## Flags

These flags work with any `aspect` command.

| Flag                     | Description                                                                                   |
|--------------------------|----------------------------------------------------------------------------------------------|
| `--aspect:config string` | Specifies an Aspect CLI config file. Set to `/dev/null` to ignore subsequent flags.          |
| `--aspect:hints`         | Enables hints if configured (default: true).                                                         |
| `--aspect:interactive`   | Enables interactive mode, such as prompting for user input (default: true).                              |
| `-h`, `--help`           | Displays Aspect help.                                                                             |
| `-v`, `--version`        | Displays Aspect version.                                                                          |

-----

For more details on any command, use `aspect [command] --help` in your terminal.
