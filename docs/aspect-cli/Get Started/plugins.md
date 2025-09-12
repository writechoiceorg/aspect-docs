---
title: Plugins
description: Learn how to create, load, and enable Aspect CLI plugins.
slug: plugins
---

# Plugins

This document is a reference for **Aspect CLI plugins**.

---

## Writing plugins

To create a plugin, start with a **[Starlark](https://github.com/bazelbuild/starlark)** source file, preferably with a `.star` extension for syntax highlighting and formatting in tools like GitHub.

Common locations for these files include:

* `/tools/configure/my_extension.star`: near other tool configurations.
* `/bazel/rules_mylang.star`: near Bazel-specific support code.
* `/.aspect/cli/my_ruletype.star`: alongside the Aspect CLI configuration.

Your plugin will use the `aspect` symbol provided in the Starlark interpreter context. At a minimum, you must call `aspect.register_configure_extension`.

**Code Example**

This simple example generates `sh_library` targets for all shell scripts.

```python
aspect.register_configure_extension(
    id = "rules_sh",
    prepare = lambda cfg: aspect.PrepareResult(
        sources = aspect.SourceExtensions(".bash", ".sh"),
    ),
    declare = lambda ctx: ctx.targets.add(
        kind = "sh_library",
        name = "shell",
        attrs = {
            "srcs": [s.path for s in ctx.sources],
        },
    ),
)
```

-----

## Loading plugins

The **Starlark** interpreter runtime is included in the Aspect CLI. To load your plugin, add a section to the `.aspect/cli/config.yaml` file.

```yaml
configure:
    plugins:
        WORKSPACE_relative/path/to/my_plugin.star
```

-----

## Enabling plugins

Enable or disable individual plugins using **[BUILD directives]**:

```
# aspect:{plugin_id} enabled|disabled
```