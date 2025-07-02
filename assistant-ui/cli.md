 # `assistant-ui` CLI Documentation

 This document provides reference for the public commands exposed by the `assistant-ui` CLI package.

 **Import path:**
 ```ts
 import { add, create, init, update, codemodCommand, upgradeCommand } from 'assistant-ui/packages/cli';
 ```

 ## add

 **Signature**
 ```ts
 import { add } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Add one or more components to your project.

 **Arguments**
 - `<components...>`: string[] – The components to add (e.g., `Button`, `Input`).

 **Options**
 - `-y, --yes` (boolean, default: `true`): Skip confirmation prompt.
 - `-o, --overwrite` (boolean, default: `false`): Overwrite existing files.
 - `-c, --cwd <cwd>` (string, default: `process.cwd()`): Working directory.
 - `-p, --path <path>` (string): Path to add the component to.

 **Usage**
 ```bash
 assistant-ui add Button Input --yes --path src/components
 ```

 ## create

 **Signature**
 ```ts
 import { create } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Create a new project.

 **Arguments**
 - `[project-directory]` (string, optional): Name of the project directory.

 **Options**
 - `-t, --template <template>` (string): Template to use (`default`, `langgraph`, `mcp`).
 - `--use-npm`: Use npm to bootstrap.
 - `--use-pnpm`: Use pnpm to bootstrap.
 - `--use-yarn`: Use Yarn to bootstrap.
 - `--use-bun`: Use Bun to bootstrap.
 - `--skip-install`: Skip installing dependencies.

 **Usage**
 ```bash
 assistant-ui create my-app --template langgraph --use-pnpm
 ```

 ## init

 **Signature**
 ```ts
 import { init } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Initialize Assistant-UI in a new or existing project.

 **Usage**
 ```bash
 assistant-ui init
 ```

 ## update

 **Signature**
 ```ts
 import { update } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Update all `@assistant-ui/*` and `assistant-stream` packages to their latest versions.

 **Options**
 - `--dry` (boolean): Print the update command without executing.

 **Usage**
 ```bash
 assistant-ui update --dry
 ```

 ## codemod

 **Signature**
 ```ts
 import { codemodCommand } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Run codemods on source files.

 **Arguments**
 - `<codemod>` (string): The codemod to run (e.g., `rewrite-framework-imports`).
 - `<source>` (string): Path to files or directory to transform.

 **Options**
 - `-d, --dry`: Dry run (no file changes).
 - `-p, --print`: Print transformed output to stdout.
 - `--verbose`: Show detailed transform logs.
 - `-j, --jscodeshift <options>`: Options passed to jscodeshift.

 **Usage**
 ```bash
 assistant-ui codemod rewrite-framework-imports src/
 ```

 ## upgrade

 **Signature**
 ```ts
 import { upgradeCommand } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Upgrade AI package dependencies and apply codemods.

 **Options**
 - `-d, --dry`: Dry run (no file changes).
 - `-p, --print`: Print transformed output to stdout.
 - `--verbose`: Show detailed transform logs.
 - `-j, --jscodeshift <options>`: Options passed to jscodeshift.

 **Usage**
 ```bash
 assistant-ui upgrade --dry
 ```