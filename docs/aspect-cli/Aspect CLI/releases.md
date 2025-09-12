---
title: Aspect CLI Releases
description: Aspect CLI last releases.
slug: releases
---

# Aspect CLI Releases | Aspect Docs

## 2025.19.5 (latest)
[​](#2025195-latest "Direct link to 2025.19.5 (latest)")

_May 6, 2025_

### Installation[​](#installation "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2025.19.5

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes "Direct link to Changes")

Features:

*   Generate ts\_project(isolated\_typecheck) when isolatedDeclarations enabled (#8590)
    
*   Disable configure gitignore support by default (#8490)
    
    The `configure` gitignore support is now disabled by default. It is recommended to `.bazelignore` and the Bazel 8 [ignore\_directories](https://bazel.build/rules/lib/globals/repo#ignore_directories) feature to align BUILD generation and Bazel ignore logic while also increasing `configure` performance.
    
    To maintain gitignore functionality add the following to the root BUILD:
    
    ```
# aspect:gitignore enabled

```

    
*   `ts_config` target generation is now enabled by default
    
    Generation can be enabled|disabled with `# aspect:js_tsconfig enabled|disabled`
    
*   Support `.netrc` files for authentication when downloading cli plugins.
    

Fixes:

*   Forward bes to user provided backend including .bazelrc (#8666, [https://github.com/aspect-build/aspect-cli/issues/838](https://github.com/aspect-build/aspect-cli/issues/838))
    
*   Do not convert non-array orion imports to array attribute values (#8532)
    
    Fix orion generation of attributes containing a single imported label:
    
    ```
ctx.targets.add(
    ....
    attrs = {
            "config": aspect.Import(...)
    }
)

```

    
    should generate a `config` attribute with a single label referencing the imported symbol.
    
*   tsconfig references to tsconfig .json files (#8509)
    
*   Fix `configure` generation of `ts_config(deps)` when referencing npm packages. (#8512)
    

## 2025.15.20[​](#20251520 "Direct link to 2025.15.20")

_April 9, 2025_

### Installation[​](#installation-1 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-1 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2025.15.20

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-1 "Direct link to Changes")

Features:

*   (OSS) Aspect CLI configure command now supports hints (#8113)
*   Add CLI lint `lint --quiet` to reduce stdout on success (#8238)
*   Support the latest bazel-gazelle features including:
    *   [REPO support](https://github.com/bazel-contrib/bazel-gazelle/pull/2039)
    *   the Aspect `generation_mode` is now [builtin to gazelle](https://github.com/bazel-contrib/bazel-gazelle/pull/1921) (#8351)

Fixes:

*   Ensure that the CLI handles more than one linter per label (#8355)
*   Fix `configure` rules\_python BUILD-generation with `generation_mode: update_only`. See [https://github.com/bazel-contrib/rules\_python/issues/2707](https://github.com/bazel-contrib/rules_python/issues/2707) (#8365)
*   (OSS) Fix formatting of long lines in Aspect CLI hints output (#8116)
*   Support running queries via `--query_file` (#8166)
*   CLI outputs verb: Handle runfile characters [introduced in bazel 7.4.0](https://github.com/bazelbuild/bazel/pull/23912) and attempt to determine an absolute path if a relative one is provided (#8280)

## 2025.08.18[​](#20250818 "Direct link to 2025.08.18")

_February 19, 2025_

### Installation[​](#installation-2 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-2 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2025.08.18

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-2 "Direct link to Changes")

Features:

*   (OSS) Support orion plugins importing and depending on js language files such as those outputted by generated `ts_project` targets

Performance:

*   (OSS) Aspect CLI lint command now handle multi-threaded BES events for faster BES processing

Fixes:

*   (OSS) Do not output `aspect configure` warning for package.json 'null exports'
*   (OSS) Fix formatting of long lines in Aspect CLI hints output

## 2025.06.52[​](#20250652 "Direct link to 2025.06.52")

_February 8, 2025_

### Installation[​](#installation-3 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-3 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2025.06.52

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-3 "Direct link to Changes")

Fixes:

*   (OSS) Fixes stdout & stderr flushing when hints are enabled. Also adds a way to disable hints with the flag `--aspect:hints=false` or `--aspect:hints=0`
*   (OSS) Fix issue detecting dependencies in dynamic import/require calls containing a nested comment such as `/* webpackChunkName: ... */`. Example repro: [https://github.com/walkerburgin/aspect-cli-regression-repro](https://github.com/walkerburgin/aspect-cli-regression-repro)

## 2025.05.46[​](#20250546 "Direct link to 2025.05.46")

_February 2, 2025_

### Installation[​](#installation-4 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-4 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2025.05.46

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-4 "Direct link to Changes")

Features:

*   (OSS) Upgrade configure gazelle version to v0.41.0. See the latest new features in [gazelle 0.41](https://github.com/bazel-contrib/bazel-gazelle/releases/tag/v0.41.0)
*   (OSS) Add aspect `configure` `ts_project(declaration_dir)` support
*   (OSS) Add `# aspect:js_tsconfig_ignore {attribute}` to disable generation of tsconfig related `ts_project` attributes such as `tsconfig`, `declaration`, `incremental` etc. Use cases include scenarios where such attributes are automatically set by a macro or when such config is unnecessary within bazel but the underlying tsconfig.json can not be changed

Performance:

*   (OSS) `aspect configure` AST query performance improvements

Fixes:

*   (OSS) Fix `# keep` directives on tsconfig related attributes with `aspect configure`
*   (OSS) Support `js_ignore_imports` of .proto imports for `aspect configure`
*   (OSS) Support aspect `configure` dependencies on generated `ts_proto_library`, `_pb`, `_connect` and `_connectquery` files
*   (OSS) Fix `aspect configure` to allow generated package targets to include source files not included in `ts_project(srcs)`
*   (OSS) Aspect CLI hints feature now only parses the stdout & stderr from Bazel out in the following commands: build, coverage, fetch, info, lint, run, test. This fixes interactive mode in the CLI when hints are enabled

## 2025.03.28[​](#20250328 "Direct link to 2025.03.28")

_January 17, 2025_

### Installation[​](#installation-5 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-5 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2025.03.28

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-5 "Direct link to Changes")

Features:

*   (OSS) Aspect CLI now supports adding "hints" in the Aspect CLI config file. This is a map of regex to hint/suggestion that will be displayed when the regex matches in stdout or stderr of the command
*   Support BUILD-generation dependencies on npm packages from other gazelle languages such as orion
*   Orion CLI `configure` plugins can generate rules\_js/ts rules such as `ts_project`, `js_library`, `js_binary` etc

Performance:

Fixes:

*   (OSS) Support BUILD generation of `ts_project(ts_build_info_file)`
*   (OSS) Fix finding dependencies in esm dynamic type-only imports in tsx files

## 2024.51.11[​](#20245111 "Direct link to 2024.51.11")

_December 17, 2024_

### Installation[​](#installation-6 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-6 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2024.51.11

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-6 "Direct link to Changes")

Features:

*   (OSS) Add support for salting on the `outputs` verb. To add a salt value to hashes generated from the `outputs` verb, specify the `--hash_salt` flag when calling `outputs`
*   (OSS) Add support for `# gazelle:js_tsconfig_file _tsconfig_json_` directory to specify the path `tsconfig.json` file, in case your TSConfig file has a different file name
*   Aspect CLI can now use the output from Stylelint
*   Orion target declaration attribute values can be a source file, automatically converting to the relative file path (the same as `.path`)
*   Orion extension property values are now inherited. Properties are almost always intended to be inherited by child BUILD files. Opt-out of this may be added in the future if/when necessary
*   If the folder given via `lint_diagnostics_file` does not exist, create it.

Performance:

*   Parse and cache orion queries on declaration to reduce locking during concurrent orion query execution

Fixes:

*   Add documentation for `aspect explain`
*   (OSS) Handle non `@types` imports from tsconfig `compilerOptions.types`
*   (OSS) Reflect the tsconfig "composite" and "incremental" setting into ts\_project() rules
*   (OSS) Add generated ts sources to ts\_project(srcs)
*   Orion plugin property declarations no longer require a default
*   Orion `PrepareResult.sources` can now be a single filter in addition to an array of filters
*   (OSS) Support `aspect configure` detecting dependencies from `package.json` `types|typings` fields
*   (OSS) Workflows now supports faster build event transmission to Aspect CLI plugins by sending up to 10 build events in parallel via 10 concurrent BEPEventCallback calls. Plugins can opt-in to faster build event transmission by setting `multi_threaded_build_events: true` in their configuration in the `.aspect/cli/config.yaml` configuration file. Plugins that opt-in must handle multi-threaded BEPEventCallback. If plugins require build events to be processed in order they should buffering out-of-order build events and processing the build events in order of sequence number. The sequence number of a build event can be obtained via a new 2nd parameter of the BEPEventCallback function: `BEPEventCallback(event *buildeventstream.BuildEvent, sequenceNumber int64)`
*   `aspect configure` handles `/// <reference lib="..." />` comments correctly

BREAKING CHANGES:

*   (OSS) Aspect CLI no longer supported building and running a Workflows plugin from a target. Instead it is recommended to run it from a built binary during plugin development. For example, [https://github.com/aspect-build/plugin-fix-visibility/blob/a8acbbd2bce5a6e5cf6990050e438acd505dafbc/.aspect/cli/config.yaml#L8](https://github.com/aspect-build/plugin-fix-visibility/blob/a8acbbd2bce5a6e5cf6990050e438acd505dafbc/.aspect/cli/config.yaml#L8).

## 2024.46.33[​](#20244633 "Direct link to 2024.46.33")

_November 13, 2024_

### Installation[​](#installation-7 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-7 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2024.46.33

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-7 "Direct link to Changes")

Performance:

*   (OSS) `aspect configure` glob patterns are now validated once up front instead of per evaluation
*   (OSS) Minor reduction in memory allocation during `aspect configure`, especially parsing of source code
*   (OSS) Minor reduction in memory allocation during `aspect configure`
*   (OSS) Cache parsing of regex statements for `aspect configure` source code parsing
*   (OSS) Prevent extra fs lookup for `aspect configure` tsconfig `extends`
*   (OSS) Caching of `aspect configure` tree-sitter query metadata across query executions
*   (OSS) Reduce fs io of `aspect configure` during fs traversal (#7173)
*   Performance improvements to orion BUILD generator
*   (OSS) Memory improvements to js `aspect configure`
*   (OSS) `aspect configure` performance improvements
*   (OSS) Add experimental caching of source code analysis for `aspect configure` via `ASPECT_CONFIGURE_CACHE` environment variable pointing to a cache file path, normally in a temp or ignored file location

Fixes:

*   (OSS) Fix to flag handling when empty argument passed to CLI
*   (OSS) Comments similar to typescript triple-comment references should not output errors during `aspect configure`
*   (OSS) The go language in `aspect configure` will now work when rules\_go is under bzlmod
*   (OSS) The `ts_project` `preserve_jsx` attribute is now generated by `aspect configure`
*   (OSS) `aspect configure` orion generated targets of rule kinds known to the configure js language should work across languages
*   (OSS) Increase linting BES completion timeout to 60 seconds
*   (OSS) Use localExecRoot if possible when constructing path to results files ([https://github.com/aspect-build/aspect-cli/pull/768](https://github.com/aspect-build/aspect-cli/pull/768))
*   (OSS) Fix the use of cli empty-string arguments ([https://github.com/aspect-build/aspect-cli/issues/771](https://github.com/aspect-build/aspect-cli/issues/771))

Refactors:

*   (OSS) Update configure directive suggestions to use aspect directives
*   (OSS) Remove query -> cquery guidance

## 2024.42.3[​](#2024423 "Direct link to 2024.42.3")

_October 15, 2024_

### Installation[​](#installation-8 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-8 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2024.42.3

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-8 "Direct link to Changes")

Features:

*   (OSS) `configure` generated package targets (`npm_package` or `js_library`) visibility is now set to public by default.
*   Support starzelle rule removal restricted by kind

Fixes:

*   (OSS) Pnpm workspace project dependencies resolved with `aspect configure` are now higher priority then resolving dependencies via tsconfig paths
*   Show correct paths in starzelle error stack traces
*   Use gazelle result.Empty API for target removal
*   Read+query source files concurrently across all plugins

Refactors:

*   Improve starzelle missing-property error message

## 2024.41.17[​](#20244117 "Direct link to 2024.41.17")

_October 8, 2024_

### Installation[​](#installation-9 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-9 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2024.41.17

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-9 "Direct link to Changes")

Fixes:

*   Dependencies from package.json `main/types/exports` fields no longer cause errors when `aspect configure` can not resolve the referenced files.

## 2024.41.9[​](#2024419 "Direct link to 2024.41.9")

_October 7, 2024_

### Installation[​](#installation-10 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-10 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2024.41.9

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-10 "Direct link to Changes")

Features:

*   (OSS) New `--fixes` flag added to lint command in the the Aspect CLI which defaults to true. Users can now set `--nofixes` to turn off requesting the lint patch output group (`rules_lint_patch`). Requesting the report outputs groups (`rules_lint_human` and `rules_lint_machine`) is still controlled by the `--report` and `--machine` flags

Fixes:

*   (OSS) rules\_js gazelle plugin: support direct import of outputted dts files. Things like package.json might import both the .d.ts and .js version of a file. Those can come from source .d.ts or .ts.
*   (OSS) rules\_js gazelle plugin: set npm\_package(srcs) as mergeable gazelle attribute
*   (OSS) rules\_js gazelle plugin: tsconfig rootdir filtering of source files
*   (OSS) rules\_js gazelle plugin: generated npm package srcs vs deps attributes
*   (OSS) rules\_js gazelle plugin: support package.json exports/main references to outputted .js/.d.ts
*   (OSS) gazelle: minor enhancement to support the .gitignore trailing-/ so it only matches directories

Refactors:

*   (OSS) gazelle: upgrade tree-sitter json, kotlin, starlark, typescript grammars. Addresses [https://github.com/aspect-build/aspect-cli/issues/530](https://github.com/aspect-build/aspect-cli/issues/530).

## 2024.39.54[​](#20243954 "Direct link to 2024.39.54")

_September 28, 2024_

### Installation[​](#installation-11 "Direct link to Installation")

For full install instructions see [overview](about:/cli/#installation).

#### Bazelisk (macOS / Linux)
[​](#bazelisk-macos--linux-11 "Direct link to Bazelisk (macOS / Linux)")

Configure [bazelisk](https://github.com/bazelbuild/bazelisk) to use the Aspect CLI for all developers in a repository by adding the following to `.bazeliskrc` in the repository root:

```
BAZELISK_BASE_URL=https://static.aspect.build/aspect
USE_BAZEL_VERSION=aspect/2024.39.54

```


The underlying version of Bazel can be configured in your `.bazelversion` file or the `BAZEL_VERSION` environment variable.

### Changes[​](#changes-11 "Direct link to Changes")

Initial release of the Aspect CLI (standard).