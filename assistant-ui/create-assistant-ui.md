 # create-assistant-ui

 Command-line tool to scaffold a new assistant-ui project.

 ## Installation

 Install via npx (no global install required):
 ```sh
 npx create-assistant-ui [project-directory] [options]
 ```
 Or install globally:
 ```sh
 npm install -g create-assistant-ui
 ```

 ## Usage

 ```sh
 create-assistant-ui [project-directory] [options]
 ```

 **Arguments**

 - `project-directory` (optional): Name of the folder for the new project.

 **Options**

 - `-t, --template <template>`: Template to use (default, langgraph).
 - `--use-npm`     : Bootstrap with npm.
 - `--use-pnpm`    : Bootstrap with pnpm.
 - `--use-yarn`    : Bootstrap with Yarn.
 - `--use-bun`     : Bootstrap with Bun.
 - `--skip-install`: Skip installing dependencies.

 ## Examples

 - Create a default project:
   ```sh
   npx create-assistant-ui my-app
   ```

 - Create a project with the 'langgraph' template using pnpm:
   ```sh
   npx create-assistant-ui my-app -t langgraph --use-pnpm
   ```

 ## Notes

 This package exposes a CLI only; no programmatic API is exported.