---
title: "How to create a 'Hello World' web application"
description: "Create a simple web application using Aspect, Bazel, and pnpm."
slug: /guides/create-hello-world-web-app
audience: "JavaScript developer with basic knowledge of command-line tools."
---

# Build an example app

This guide explains how to create a "Hello, World" web application. You'll use the **Aspect CLI**, **Bazel**, and `pnpm`. Follow these steps to set up a new project, configure its build process, and run a local web server.

## Prerequisites

Before you begin, ensure you have the following tools installed:

* **Aspect CLI**: A command-line tool for managing projects with Bazel and Aspect.
* **Bazel**: A build and test tool for software.
* **`pnpm`**: A fast, disk space-efficient package manager.
* **`direnv`**: A tool to manage your project's environment variables.
* **Git**: A version control system.

## Steps

### 1. Initiate the project

Use the `aspect init` command to start a new project.

1.  Open your terminal and run `aspect init`.
2.  Enter a name for your project.
3.  Select `JavaScript & TypeScript` and press `ENTER`.
4.  Answer `Yes` to all prompts: `Setup code generation?`, `Setup format and linting?`, `Setup version stamping?`, and `Setup OCI Containers?`.
5.  Review the project summary and choose `run`.

Follow the on-screen instructions to set up your development environment. To do this, run `bazel run tools:bazel_env`.

### 2. Configure the file structure

Create the necessary files and directories for your web application.

1.  Create a `hello-world` directory inside the `packages` folder.
2.  Inside `packages/hello-world`, create an `index.html` file with the following content:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>Hello World</title>
    </head>
    <body>
        <h1>Hello, World!</h1>
    </body>
    </html>
    ```

3.  Create a `devserver.js` file inside `packages/hello-world` with this code:

    ```js
    const express = require('express');
    const path = require('path');

    const app = express();
    const port = 3000;

    app.get('/', (req, res) => {
      res.sendFile(path.join(__dirname, 'index.html'));
    });

    app.listen(port, () => {
      console.log(`Server listening on port ${port}`);
    });
    ```

4.  Create a `package.json` file inside `packages/hello-world`:

    ```json
    {
        "name": "hello-world"
    }
    ```

### 3. Add dependencies and configure Bazel

Add the `express` dependency and configure Bazel to build the project.

1.  In the root directory of your project, run `pnpm add express -w` to add `express` as a workspace dependency.
2.  Run `pnpm install` to ensure the `express` dependency is available in `node_modules`.
3.  Create a `BUILD.bazel` file in `packages/hello-world` with the following content:

```js
load("@aspect_rules_js//js:defs.bzl", "js_binary")

filegroup(
    name = "data",
    srcs = ["index.html"],
)

js_binary(
    name = "devserver",
    data = [
        ":data",
        "//:node_modules/express",
    ],
    entry_point = "devserver.js",
)
```

### 4. Run the server

Start your web server using the Aspect CLI.

1.  Run the command `aspect run //packages/hello-world:devserver` in your terminal.
2.  Open your browser and navigate to `localhost:3000` to see your "Hello, World" page.

---

## Troubleshooting

* **Express dependency error:** This error usually occurs if the `js_binary` rule cannot find the `express` package. Make sure you have referenced `//:node_modules/express` in the `data` attribute of your `BUILD.bazel` file. The `data` attribute specifies files to be made available at runtime to any executable that depends on a target.
* **Workspace error:** The application directory must be included in your `pnpm-workspace.yaml` file and must contain a `package.json` file.

---

## See also

* **Aspect CLI**: [https://github.com/aspect-build/aspect-cli](https://github.com/aspect-build/aspect-cli)
* **Bazel**: [https://bazel.build/](https://bazel.build/)
* **`pnpm`**: [https://pnpm.io/](https://pnpm.io/)