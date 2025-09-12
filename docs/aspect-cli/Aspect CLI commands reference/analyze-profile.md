---
title: The `analyze-profile` command
description: The `analyze-profile` command analyzes build profile data from one or more files.
slug: analyze-profile
---

# The `analyze-profile` command

This command analyzes and prints build profile data from one or more specified files. You can typically find profile data in `$(bazel info output_base)/command.profile.gz` after a build. To specify a different location, use the `--profile=<file>` flag.

## Usage

```sh
aspect analyze-profile <command.profile.gz> [flags]
```

-----

## Options

### Command-specific options

| Flag | Description |
| :--- | :--- |
| `-h`, `--help` | Displays help for the `analyze-profile` command. |
| `--dump=raw` | Use this for post-processing with scripts. It dumps profile data in an easily-parsed format. |

### Inherited options

These options are inherited from parent commands and work with `aspect analyze-profile`.

| Flag | Description |
| :--- | :--- |
| `--aspect:config string` | Specifies a user-defined Aspect CLI configuration file. Using `/dev/null` will ignore all subsequent `--aspect:config` flags. |
| `--aspect:hints` | Enables hints if they are configured. This is enabled by default (`true`). |
| `--aspect:interactive` | Enables interactive mode, including prompts for user input. |

-----

## Visualizing profile data

While this command prints profile data to standard output (`stdout`), we recommend using a graphical user interface (GUI) to inspect the data. Recommended tools include:

  * The **`chrome://tracing`** interface, which is built into Chromium and Google Chrome.
  * The web-based **`ui.perfetto.dev`** tool.

-----

## See Also

  * [Aspect CLI Commands](https://www.google.com/search?q=/reference/cli-commands)