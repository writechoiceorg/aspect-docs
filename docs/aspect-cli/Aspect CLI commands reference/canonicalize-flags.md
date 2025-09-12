---
title: The `canonicalize-flags` command
description: The `canonicalize-flags` command presents Bazel options in a canonical, standardized format.
slug: canonicalize-flags
---

# The `canonicalize-flags` command

This command takes a list of Bazel options and presents them in a **canonical** form. This is especially useful for creating a **unique key** to group Bazel invocations by their flags, making automation and scripting much easier.

## Usage

```sh
aspect canonicalize-flags -- <bazel flags>
```

## Examples

This example shows how the command translates shorthand flags (`-k`, `-c opt`) into their full, canonical form (`--keep_going=1`, `--compilation_mode=opt`).

```bash
% aspect canonicalize-flags -- -k -c opt
--keep_going=1
--compilation_mode=opt
```

## Options

### Command-specific options

| Flag | Description |
| :--- | :--- |
| `-h`, `--help` | Displays help for the `canonicalize-flags` command. |

### Inherited options

These options are inherited from parent commands and work with `aspect canonicalize-flags`.

| Flag | Description |
| :--- | :--- |
| `--aspect:config string` | Specifies an Aspect CLI configuration file. Using `/dev/null` will cause all subsequent `--aspect:config` flags to be ignored. |
| `--aspect:hints` | Enables hints if they are configured (default: `true`). |
| `--aspect:interactive` | Enables interactive mode, which includes prompts for user input. |

-----

## See Also

  * [Aspect CLI Commands](https://www.google.com/search?q=/reference/cli-commands)