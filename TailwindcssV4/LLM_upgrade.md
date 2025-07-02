 # @tailwindcss/upgrade

 ## CLI

 ### Installation
 ```bash
 npm install -D @tailwindcss/upgrade
 # or
 npx @tailwindcss/upgrade [options] [files...]
 ```

 ### Usage
 ```bash
 npx @tailwindcss/upgrade
 npx @tailwindcss/upgrade src/**/*.css --config tailwind.config.js
 npx @tailwindcss/upgrade --force
 npx @tailwindcss/upgrade --help
 npx @tailwindcss/upgrade --version
 ```

 ### Options

 - `-c, --config <path>`: Path to the configuration file.
 - `-f, --force`: Force migration even if the Git directory is dirty.
 - `-h, --help`: Display usage information.
 - `-v, --version`: Display the version number.

 ## Public API

 ### Commands

 - `help({ usage, options, invalid? })`: Print CLI help.

 ### Codemod Functions

 #### Config

 - `migrateJsConfig(designSystem: object, configFilePath: string, base: string): Promise<MigrationResult | null>`: Migrate JS config from v3 to v4.
 - `migratePostCSSConfig(base: string): Promise<void>`: Migrate PostCSS config.

 #### CSS

 - `analyze(stylesheets: Stylesheet[]): Promise<void>`: Analyze CSS files for migration.
 - `linkConfigsToStylesheets(stylesheets: Stylesheet[], opts: { configPath?: string; base: string }): Promise<void>`: Link Tailwind configs to stylesheets.
 - `migrateStylesheet(sheet: Stylesheet, opts: { newPrefix?: string | null; designSystem: object; userConfig?: object | null; configFilePath?: string | null; jsConfigMigration?: MigrationResult | null }): Promise<void>`: Apply CSS migrations.
 - `sortBuckets()`: PostCSS plugin to sort Tailwind buckets.
 - `splitStylesheets(stylesheets: Stylesheet[]): Promise<void>`: Split stylesheets per V4 layer strategy.
 - `formatNodes()`: PostCSS plugin to format AST nodes after migration.

 #### Template

 - `prepareConfig(configPath: string, opts: { base: string }): Promise<{ designSystem: object; userConfig: object | null; configFilePath: string }>`: Prepare design system and config for template migrations.
 - `migrateTemplate(designSystem: object, userConfig: object | null, file: string): Promise<void>`: Apply template migrations to source files.

 ## V3 → V4 Change Notes

 - Enforces a clean Git working directory unless `--force` is used.
 - Auto-discovers CSS and template files when none are provided.
 - Splits `@tailwind base/components/utilities` layers into separate files.
 - Migrates JS and PostCSS configs to V4 defaults.
 - Updates at-rules (`@apply`, `@variants`, `@import`) to V4 syntax.
 - Converts theme values to CSS variables for the new default theme.
 - Updates dependencies to the latest Tailwind CSS and related packages.
 - Cleans up injected `layer()` in import rules when no utilities are present.