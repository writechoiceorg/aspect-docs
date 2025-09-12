# Aspect CLI Commands

Aspect CLI is an enhanced interface for running Bazel.

## Usage

```bash
aspect [command]
```

## Command Categories

### Common Bazel Commands

| Command     | Description                                                        |
|-------------|--------------------------------------------------------------------|
| `build`     | Compiles the specified targets                                     |
| `cquery`    | Queries the dependency graph, respecting configuration flags       |
| `run`       | Compiles a single target and runs it with the provided arguments   |
| `test`      | Compiles the specified targets and runs all tests                  |
| `version`   | Shows the Aspect CLI and Bazel versions                            |

### Aspect CLI Exclusive Commands

| Command      | Description                                               |
|--------------|----------------------------------------------------------|
| `configure`  | Autoconfigures Bazel by updating BUILD files             |
| `docs`       | Opens documentation in the browser                       |
| `init`       | Creates a new Bazel workspace                            |
| `lint`       | Runs configured linters on the dependency graph          |
| `outputs`    | Shows paths to declared output files                     |
| `print`      | Displays syntax elements from BUILD files                |

### Other Commands

| Command               | Description                                                        |
|-----------------------|--------------------------------------------------------------------|
| `analyze-profile`     | Analyzes build profile data                                        |
| `aquery`              | Queries the action graph                                           |
| `canonicalize-flags`  | Presents Bazel options in canonical format                         |
| `clean`               | Removes the output tree                                            |
| `config`              | Shows configuration details                                        |
| `coverage`            | Same as `test`, but also generates code coverage reports           |
| `fetch`               | Fetches external repositories required for the targets             |
| `info`                | Shows Bazel server runtime information                             |
| `license`             | Displays this software's license                                   |
| `mod`                 | Tools for working with the bzlmod dependency graph                 |
| `query`               | Queries the dependency graph, ignoring configuration flags         |
| `shutdown`            | Shuts down the Bazel server                                        |

### Additional Commands

| Command       | Description                                                         |
|---------------|---------------------------------------------------------------------|
| `completion`  | Generates shell autocomplete script                                 |
| `explain`     | Compares two Bazel execution logs to detect hermeticity issues      |
| `help`        | Shows Aspect CLI help                                               |

## Flags

| Flag                     | Description                                                                                   |
|--------------------------|----------------------------------------------------------------------------------------------|
| `--aspect:config string` | Aspect CLI config file specified by the user. `/dev/null` ignores subsequent flags.          |
| `--aspect:hints`         | Enables hints if set (default: true)                                                         |
| `--aspect:interactive`   | Interactive mode (e.g., prompts for user input) (default: true)                              |
| `-h`, `--help`           | Shows Aspect help                                                                             |
| `-v`, `--version`        | Shows Aspect version                                                                          |

---

Type `aspect [command] --help` for more information about the commands.