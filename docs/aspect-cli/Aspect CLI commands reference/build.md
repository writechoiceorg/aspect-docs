---
title: The `build` command
description: The `build` command executes a build on specified targets.
slug: build
---

# The `build` command

This command executes a build on specified targets and produces standard outputs. For detailed information on how to specify targets, refer to the [Bazel target syntax documentation](https://bazel.build/docs/command-line-reference#target-patterns).

## Usage

```bash
aspect build <target patterns> [flags]
```

## Options

### Command-specific options

| Option | Description |
| :--- | :--- |
| `--fetch=false` | Prevents Bazel from fetching any missing or outdated external dependencies before the build. |
| `--nobuild` | Loads and analyzes `BUILD` files without starting any build actions. |
| `-k`, `--keep_going` | Continues the build even after an error is encountered. |
| `--output_groups` | Creates non-default outputs. |
| `--build_tag_filters` | Filters the target pattern. |

### Inherited options

These options can be used with any `aspect` command.

| Option | Description |
| :--- | :--- |
| `--aspect:config string` | Specifies an Aspect CLI configuration file. Using `/dev/null` will ignore any further `--aspect:config` flags. |
| `--aspect:hints` | Enables hints if they are configured. This is enabled by default (`true`). |
| `--aspect:interactive` | Enables interactive mode for user input prompts. |

-----

## Related Resources

  * [Aspect CLI Commands](https://www.google.com/search?q=/reference/cli-commands)