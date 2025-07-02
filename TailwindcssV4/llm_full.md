# LLM.md

 # Tailwind CSS API Documentation
 This repository contains API documentation for each public module of Tailwind CSS, generated for use in your project.

 - [Core Package](LLM_core.md)
 - [Plugin API](LLM_plugin.md)
 - [Config API](LLM_config.md)
 - [CLI API](LLM_cli.md)
 - [Browser API](LLM_browser.md)
 - [Node API](LLM_node.md)
 - [PostCSS API](LLM_postcss.md)
 - [Standalone API](LLM_standalone.md)
 - [Upgrade Tool API](LLM_upgrade.md)
 - [Vite Plugin API](LLM_vite.md)
# browser.md

 # @tailwindcss/browser

 A browser-only build of Tailwind CSS v4 for rapid prototyping. This package scans `<style type="text/tailwindcss">` tags, compiles utility classes in the browser, and injects dynamic CSS. Not intended for production.

 ## Installation

 Include via ESM in an HTML page:
 ```html
 <script type="module">
   import '@tailwindcss/browser';
 </script>
 <style type="text/tailwindcss">
   @tailwind base;
   @tailwind components;
   @tailwind utilities;
 </style>
 ```

 Or via bundler in JavaScript:
 ```js
 import '@tailwindcss/browser';
 // Your application code follows
 ```

 ## Behavior

 - Scans `<style type="text/tailwindcss">` tags on the page.
 - Emits a warning if used in production.
 - Performs a full build on initial load or when stylesheets change.
 - Tracks classes and runs incremental rebuilds on DOM class changes.
 - Injects the compiled CSS into a `<style>` tag appended to `<head>`.

 ## Limitations

 - Does not support plugins or external config files; loading modules/plugins throws an error.
 - Designed for prototyping; not optimized for production use.

 ## Public API

 Importing the package registers mutation observers and triggers builds. There are no named exports.

 ## V3 → V4 Change Notes

 - Browser build split into standalone `@tailwindcss/browser` package.
 - Introduced explicit `<style>` tag type `text/tailwindcss` for detection.
 - Added `Instrumentation` performance tracking.
 - Switched to incremental rebuilds by tracking seen classes instead of full rebuilds.
 - Removed runtime plugin/module loading support.
# cli.md

 # Tailwind CSS CLI (@tailwindcss/cli)

 The `tailwindcss` command-line interface provides a simple way to compile your CSS using Tailwind CSS v4.

 ## Usage

 ```bash
 tailwindcss [options]
 ```

 ### Commands

 This package supports a single implicit build command. Running `tailwindcss` with no subcommand invokes the build.

 ## Options

 | Flag | Type | Default | Alias | Description |
 |------|------|---------|-------|-------------|
 | --input | string | (built-in CSS with @import) | -i | Input file path (`-` for stdin) |
 | --output | string | `-` (stdout) | -o | Output file path |
 | --watch | boolean \| 'always' | `false` | -w | Watch for changes (`always` to keep watching after stdin closes) |
 | --minify | boolean | `false` | -m | Optimize and minify the output |
 | --optimize | boolean | `false` |  | Optimize without minifying |
 | --cwd | string | `.` |  | Working directory for resolving files |
 | --map | boolean \| string | `false` |  | Generate source map inline (`true`) or to file (path) |
 | --help | boolean | `false` | -h | Display usage information |

 ## TypeScript Signatures

 ```ts
 // src/commands/build/index.ts
 export function options(): ArgDefinition<{
   '--input': string;
   '--output': string;
   '--watch': boolean | 'always';
   '--minify': boolean;
   '--optimize': boolean;
   '--cwd': string;
   '--map': boolean | string;
 }>;

 export async function handle(
   args: Result<ReturnType<typeof options>>
 ): Promise<void>;
 ```

 ## Examples

 ```bash
 # Compile CSS from input.css to output.css
 tailwindcss -i src/input.css -o dist/output.css

 # Watch for changes
 tailwindcss --watch -i styles.css -o styles.css

 # Minify output
 tailwindcss -m -o style.min.css

 # Generate inline source map
 tailwindcss --map -i app.css -o app.output.css

 # Generate external source map file
 tailwindcss --map=app.css.map -i app.css -o app.output.css
 ```

 ## v3 → v4 Changes

 - Moved CLI implementation to `@tailwindcss/cli` package.
 - Added separate `--optimize` flag alongside `--minify`.
 - `--map` now accepts true/false inline or string path for file.
 - Default `--output` changed to `-` (stdout) instead of requiring explicit output.
# config.md

 # Tailwind CSS Configuration API Documentation
 This document outlines the configuration system for the core `tailwindcss` package (v4.x).

 ## `Config` Type
 **Import Path**
 ```ts
 import type { Config } from 'tailwindcss'
 ```
 **Signature**
 ```ts
 type Config = UserConfig
 ```
 **Description**
 Alias for the full user configuration schema, including theme, plugins, presets, content, darkMode, prefix, important, blocklist, and future flags.
 **Example**
 ```ts
 import type { Config } from 'tailwindcss'
 const config: Config = {
   content: ['./src/**/*.html'],
   theme: {
     extend: { colors: { teal: '#4dc0b5' } }
   },
   plugins: []
 }
 ```
 **Notice of API Change (V3 → V4)**
 Schema expanded to support new `future` key and refined `darkMode` strategies; core `Config` alias remains.

 ## `defaultTheme`
 **Import Path**
 ```ts
 import defaultTheme from 'tailwindcss/defaultTheme'
 ```
 **Signature**
 ```ts
 type DefaultTheme = Record<string, Record<string, unknown>>
 ```
 **Description**
 The built-in default theme values (colors, spacing, fonts, etc.) before any user overrides.
 **Example**
 ```ts
 import defaultTheme from 'tailwindcss/defaultTheme'
 console.log(defaultTheme.spacing['4']) // '1rem'
 ```
 **Notice of API Change (V3 → V4)**
 Default theme structure largely unchanged; now exported under `defaultTheme` module.

 ## `resolveConfig`
 **Import Path**
 ```ts
 import { resolveConfig } from 'tailwindcss/resolveConfig'
 ```
 **Signature**
 ```ts
 function resolveConfig(
   designSystem: import('tailwindcss').DesignSystem,
   files: Array<{ base: string; config: UserConfig; path?: string; reference?: boolean }>
 ): {
   resolvedConfig: ResolvedConfig
   replacedThemeKeys: Set<string>
 }
 ```
 **Description**
 Merges multiple user/preset/plugin config files with the default design system to produce a final resolved configuration and track theme keys replaced by `extend`.
 **Example**
 ```ts
 import { resolveConfig } from 'tailwindcss/resolveConfig'
 import defaultTheme from 'tailwindcss/defaultTheme'

 const { resolvedConfig } = resolveConfig(
   designSystem, // new DesignSystem()
   [{ base: '.', config: { theme: { colors: { primary: '#123456' } } } }]
 )
 console.log(resolvedConfig.theme.colors.primary)
 ```
 **Notice of API Change (V3 → V4)**
 Config resolution now tracks `future` flags and preserves plugin ordering; replaces legacy preset merge.

 ## Config Schema Types
 Types representing config shape under `compat/config/types.ts`.

 ### `UserConfig`
 **Import Path**
 ```ts
 import type { UserConfig } from 'tailwindcss/compat/config/types'
 ```
 **Description**
 Base interface for user-supplied config, including `presets?`, `theme?`, `plugins?`, `content?`, `darkMode?`, `prefix?`, `important?`, `blocklist?`, and `future?`.
 **Example**
 ```ts
 const userConfig: UserConfig = {
   theme: { extend: { spacing: ['2px', '4px'] } },
   darkMode: 'class',
   future: { hoverOnlyWhenSupported: true }
 }
 ```

 ### `ThemeConfig` & `ThemeValue`
 **Import Path**
 ```ts
 import type { ThemeConfig, ThemeValue } from 'tailwindcss/compat/config/types'
 ```
 **Description**
 `ThemeConfig` maps theme keys to values or functions (`ThemeValue`), with optional `extend` overrides.
 **Example**
 ```ts
 const themeConfig: ThemeConfig = {
   colors: { red: '#f00' },
   extend: { spacing: { '72': '18rem' } }
 }
 ```

 ### `ResolvedConfig`
 **Import Path**
 ```ts
 import type { ResolvedConfig } from 'tailwindcss/compat/config/types'
 ```
 **Description**
 Fully expanded configuration after merging defaults, user config, presets, and plugins. Contains normalized `theme`, `content`, `plugins`, and top-level keys (`darkMode`, `prefix`, `important`, `blocklist`, `future`).
 **Example**
 ```ts
 const finalConfig: ResolvedConfig = resolvedConfig
 console.log(Object.keys(finalConfig.theme))
 ```
 **Notice of API Change (V3 → V4)**
 Resolved config now includes `future` and `blocklist`; key normalization improved for plugin presets.
# core.md

 # Tailwind CSS Core Package API Documentation
 This document outlines the public API exported by the `tailwindcss` core package (v4.x).

 ## Config
 **Import Path**
 ```ts
 import type { Config } from 'tailwindcss'
 ```
 **Signature**
 ```ts
 type Config = UserConfig
 ```
 **Description**
 Alias for the user configuration schema (`UserConfig`) used by Tailwind’s compatibility layer.
 **Example**
 ```ts
 import { compile } from 'tailwindcss'
 const config: Config = { theme: { extend: {} } }
 ```
 **Notice of API Change (V3 → V4)**
 No changes; remains an alias for the same compatibility type.

 ## Polyfills
 **Import Path**
 ```ts
 import { Polyfills } from 'tailwindcss'
 ```
 **Signature**
 ```ts
 const enum Polyfills {
   None = 0,
   AtProperty = 1 << 0,
   ColorMix = 1 << 1,
   All = AtProperty | ColorMix,
 }
 ```
 **Description**
 Bitmask flags to control emission of CSS polyfills for `@property` and `color-mix()` fallbacks.
 **Example**
 ```ts
 import { compile } from 'tailwindcss'
 const result = await compile(css, { polyfills: Polyfills.All })
 ```
 **Notice of API Change (V3 → V4)**
 Introduced in v4 to enable optional CSS polyfills.

 ## Features
 **Import Path**
 ```ts
 import { Features } from 'tailwindcss'
 ```
 **Signature**
 ```ts
 const enum Features {
   None = 0,
   AtApply = 1 << 0,
   AtImport = 1 << 1,
   JsPluginCompat = 1 << 2,
   ThemeFunction = 1 << 3,
   Utilities = 1 << 4,
   Variants = 1 << 5,
 }
 ```
 **Description**
 Bitmask flags indicating which Tailwind features were used during compilation (e.g., `@apply`, `theme()`).
 **Example**
 ```ts
 const { features } = await compile(css)
 if (features & Features.Utilities) { /* generated utilities */ }
 ```
 **Notice of API Change (V3 → V4)**
 Added new flags for plugin compatibility and feature tracking.

 ## compileAst
 **Import Path**
 ```ts
 import { compileAst } from 'tailwindcss'
 ```
 **Signature**
 ```ts
 async function compileAst(
   input: AstNode[],
   opts?: CompileOptions
 ): Promise<{
   sources: { base: string; pattern: string; negated: boolean }[]
   root: Root
   features: Features
   build(candidates: string[]): AstNode[]
 }>
 ```
 **Description**
 Parses and processes a PostCSS AST, returning intermediate results and a `build` method to regenerate transformed AST nodes.
 **Example**
 ```ts
 import { parse } from 'tailwindcss/css-parser'
 const ast = parse('@tailwind utilities')
 const api = await compileAst(ast)
 const newAst = api.build(['bg-red-500'])
 ```
 **Notice of API Change (V3 → V4)**
 New in v4; provides low-level AST pipeline for plugins and advanced use.

 ## compile
 **Import Path**
 ```ts
 import { compile } from 'tailwindcss'
 ```
 **Signature**
 ```ts
 async function compile(
   css: string,
   opts?: CompileOptions
 ): Promise<{
   sources: { base: string; pattern: string; negated: boolean }[]
   root: Root
   features: Features
   build(candidates: string[]): string
   buildSourceMap(): DecodedSourceMap
 }>
 ```
 **Description**
 Primary API to compile Tailwind CSS directives and classes into final CSS string and source map builder.
 **Example**
 ```ts
 import { compile } from 'tailwindcss'
 const result = await compile('@tailwind utilities; .btn { @apply px-4 }')
 console.log(result.build([]))
 ```
 **Notice of API Change (V3 → V4)**
 Signature unchanged; V4 preserves promise-based output with added `buildSourceMap`.

 ## DecodedSourceMap
 **Import Path**
 ```ts
 import type { DecodedSourceMap } from 'tailwindcss'
 ```
 **Signature**
 ```ts
 type DecodedSourceMap = { version: number; mappings: any; sources: string[]; names: string[] }
 ```
 **Description**
 Structure of the generated source map for the compiled CSS.
 **Example**
 ```ts
 const map: DecodedSourceMap = result.buildSourceMap()
 ```
 **Notice of API Change (V3 → V4)**
 No breaking changes; returned by `buildSourceMap` in both v3 and v4.

 ## __unstable__loadDesignSystem
 **Import Path**
 ```ts
 import { __unstable__loadDesignSystem } from 'tailwindcss'
 ```
 **Signature**
 ```ts
 async function __unstable__loadDesignSystem(
   css: string,
   opts?: CompileOptions
 ): Promise<DesignSystem>
 ```
 **Description**
 Extracts Tailwind’s internal design system (theme, variants, utilities) from CSS without full compilation.
 **Example**
 ```ts
 const ds = await __unstable__loadDesignSystem('@tailwind utilities')
 console.log(ds.theme)
 ```
 **Notice of API Change (V3 → V4)**
 Introduced in v4 as experimental API for tooling and introspection.

 ## postcssPluginWarning (default)
 **Import Path**
 ```ts
 import postcssPluginWarning from 'tailwindcss'
 ```
 **Signature**
 ```ts
 function postcssPluginWarning(): never
 ```
 **Description**
 Default export that throws an error when using `tailwindcss` directly as a PostCSS plugin. Informs users to install `@tailwindcss/postcss` instead.
 **Example**
 ```js
 import postcssPluginWarning from 'tailwindcss'
 // will throw at runtime:
 postcssPluginWarning()
 ```
 **Notice of API Change (V3 → V4)**
 Changed in v4: default plugin logic moved to `@tailwindcss/postcss`; this helper now always throws.
# node.md

 # @tailwindcss/node: Node.js API (v4.x)

 **Package:** `@tailwindcss/node`  
 **Version:** 4.x  
 **Repository:** https://github.com/tailwindlabs/tailwindcss/tree/main/packages/@tailwindcss-node

 ## Installation
 ```bash
 npm install --save @tailwindcss/node tailwindcss
 ```

 ## Import Paths
 ```js
 // ESM
 import { compile, compileAst, __unstable__loadDesignSystem,
          Features, Polyfills } from '@tailwindcss/node'
 import { optimize, Instrumentation, normalizePath } from '@tailwindcss/node'
 import { toSourceMap } from '@tailwindcss/node/source-maps'

 // CJS
 const { compile } = require('@tailwindcss/node')
 ```

 ## API Reference

 ### compile(css: string, options: CompileOptions): Promise<Compiler>
 Compile a CSS string to a Tailwind Compiler instance.
 **Options** (CompileOptions):
 - `base` (string, required): project root
 - `from?` (string): entry file path
 - `onDependency(path: string)`: callback for each imported file
 - `shouldRewriteUrls?` (boolean): rewrite CSS URL() paths
 - `polyfills?` (Polyfills): custom polyfill features
 - `customCssResolver?`/`customJsResolver?`: async resolver functions
 
 **Example**:
 ```js
 const compiler = await compile('@tailwindcss/base', {
   base: process.cwd(),
   onDependency: (file) => console.log('dep:', file)
 })
 const output = await compiler.process();
 ```

 ### compileAst(ast: AstNode[], options: CompileOptions): Promise<Compiler>
 Same as `compile` but accepts a PostCSS AST array.

 ### __unstable__loadDesignSystem(css: string, opts: { base: string }): Promise<DesignSystem>
 Load design tokens without compilation. _Experimental._

 ### optimize(input: string, opts?: OptimizeOptions): TransformResult
 Optimize PostCSS output via LightningCSS.
 **OptimizeOptions**:
 - `file?` (string): filename for source map
 - `minify?` (boolean): produce minified CSS
 - `map?` (string): input source map
 **TransformResult**: `{ code: string; map?: string }`

 ```js
 const { code, map } = optimize(cssString, { minify: true });
 ```

 ### Instrumentation
 ```js
 import { Instrumentation } from '@tailwindcss/node'
 const inst = new Instrumentation();
 inst.start('compile')
 await compile(css, { base, onDependency: () => {} });
 inst.end('compile');
 inst.report();
 ```

 ### normalizePath(path: string): string
 Normalize file paths to posix-style.

 ### toSourceMap(map: DecodedSourceMap | string): SourceMap
 Convert internal map to `{ raw, inline }` formats.

 ## V3 → V4 Breaking Changes
 - Package split: Node API moved to `@tailwindcss/node` (was in core)
 - `compileAst` exposed separately
 - `optimize` uses LightningCSS v1 API
 - `onDependency` callback now required for file watchers
 - ESM loaders improved: `require-cache` & `esm-cache-loader` hooks
 - Experimental `__unstable__loadDesignSystem`

 ## Examples
 ```js
 import { compile, optimize } from '@tailwindcss/node'
 let compiler = await compile('@tailwind base;', {
   base: __dirname,
   onDependency: console.log
 });
 let result = await compiler.process();
 let { code } = optimize(result.css, { minify: false });
 console.log(code);
 ```
# plugin.md

 # Tailwind CSS Plugin API Documentation
 This document outlines the public plugin and utility APIs exported by the core package (v4.x).

 ## `plugin` (default export)
 **Import Path**
 ```ts
 import plugin from 'tailwindcss/plugin'
 ```
 **Signature**
 ```ts
 function plugin(handler: PluginFn, config?: Partial<Config>): PluginWithConfig
 plugin.withOptions<T>(
   fn: (options?: T) => PluginFn,
   configFn?: (options?: T) => Partial<Config>
 ): PluginWithOptions<T>
 ```
 **Description**
 Factory to define a Tailwind plugin. Supports simple plugins via a handler or optionized plugins via `withOptions`.
 **Example**
 ```js
 // simple plugin
 module.exports = plugin(function ({ addUtilities }) {
   addUtilities({ '.text-shadow': { textShadow: '2px 2px #000' } })
 })
 ```
 **Notice of API Change (V3 → V4)**
 Unchanged; signature preserved since v3.

 ## Plugin Structure Types
 These types support plugin definitions.

 ### `PluginFn`
 **Import Path**
 ```ts
 import type { PluginFn } from 'tailwindcss/plugin'
 ```
 **Signature**
 ```ts
 type PluginFn = (api: PluginAPI) => void
 ```
 **Description**
 Function that receives a `PluginAPI` instance to register utilities, variants, components, etc.

 ### `PluginWithConfig` / `PluginWithOptions`
 **Import Path**
 ```ts
 import type { PluginWithConfig, PluginWithOptions } from 'tailwindcss/plugin'
 ```
 **Description**
 - `PluginWithConfig`: plugin object with optional default config.
 - `PluginWithOptions<T>`: factory producing a `PluginWithConfig` based on user options.

 ### `Plugin`
 **Import Path**
 ```ts
 import type { Plugin } from 'tailwindcss/plugin'
 ```
 **Description**
 Union type of `PluginFn | PluginWithConfig | PluginWithOptions<any>`.

 ## `PluginAPI` Methods
 Access these methods within your plugin handler.

 ### `addBase`
 **Signature**
 ```ts
 addBase(css: CssInJs): void
 ```
 **Description**
 Inject base styles under the `base` layer.
 **Example**
 ```js
 addBase({
   'h1': { fontSize: '2rem' }
 })
 ```
 **Notice of API Change**
 Unchanged.

 ### `addUtilities`
 **Signature**
 ```ts
 addUtilities(
   utilities: Record<string, CssInJs> | Record<string, CssInJs>[],
   options?: { variants?: string[] }
 ): void
 ```
 **Description**
 Register custom utility classes.
 **Example**
 ```js
 addUtilities({
   '.skew-10deg': { transform: 'skewY(-10deg)' }
 }, ['responsive', 'hover'])
 ```
 **Notice of API Change**
 Unchanged.

 ### `matchUtilities`
 **Signature**
 ```ts
 matchUtilities(
   map: Record<string, (value: string) => CssInJs | CssInJs[]>,
   options?: { values: Record<string, string>; type?: string }
 ): void
 ```
 **Description**
 Generate utilities based on a value map.
 **Example**
 ```js
 matchUtilities({
   'rotate': (value) => ({ transform: `rotate(${value})` })
 }, { values: { '45': '45deg' } })
 ```
 **Notice of API Change**
 Unchanged.

 ### `addComponents`
 **Signature**
 ```ts
 addComponents(
   components: Record<string, CssInJs> | Record<string, CssInJs>[],
   options?: { variants?: string[] }
 ): void
 ```
 **Description**
 Register component classes under the `components` layer.
 **Example**
 ```js
 addComponents({
   '.btn': { padding: '.5rem 1rem', borderRadius: '.25rem' }
 })
 ```
 **Notice of API Change**
 Unchanged.

 ### `matchComponents`
 **Signature**
 ```ts
 matchComponents(
   map: Record<string, (value: string) => CssInJs>,
   options?: { values: Record<string, string> }
 ): void
 ```
 **Description**
 Generate component classes from dynamic values.
 **Example**
 ```js
 matchComponents({
   'btn': (value) => ({ padding: value })
 }, { values: { 'sm': '.25rem', 'md': '.5rem' } })
 ```
 **Notice of API Change**
 Unchanged.

 ### `addVariant`
 **Signature**
 ```ts
 addVariant(
   name: string,
   variant: string | string[] | CssInJs
 ): void
 ```
 **Description**
 Define a custom variant (e.g., pseudo-class or at-rule).
 **Notice of API Change**
 V4 ignores v3 `:merge()` syntax and auto-compounds `group-*`/`peer-*` selectors.

 ### `matchVariant`
 **Signature**
 ```ts
 matchVariant<T = string>(
   name: string,
   cb: (value: T | string, extra: { modifier: string | null }) => string | string[],
   options?: { values?: Record<string, T>; sort?: (a, b) => number }
 ): void
 ```
 **Description**
 Create functional variants based on matcher functions.
 **Notice of API Change**
 V4 deprecates manual `:merge()`; uses compound API for grouping.

 ### `theme` & `config`
 **Signature**
 ```ts
 theme(path: string, defaultValue?: any): any
 config(path?: string, defaultValue?: any): any
 ```
 **Description**
 Access theme values or full resolved config within a plugin.
 **Notice of API Change**
 Unchanged.

 ### `prefix`
 **Signature**
 ```ts
 prefix(className: string): string
 ```
 **Description**
 Apply user-configured prefix to class names.
 **Notice of API Change**
 Unchanged.
# standalone.md

 # Tailwind CSS Standalone Bundle
 
 A zero-dependency, precompiled Tailwind CSS engine for Node, browser, and CLI use.
 
 ## Installation
 
 npm:
 ```bash
 npm install @tailwindcss-standalone
 ```
 
 Yarn:
 ```bash
 yarn add @tailwindcss-standalone
 ```
 
 ## Usage
 
 ### CLI
 ```bash
 npx tailwindcss-standalone -i input.css -o output.css --minify --config tailwind.config.js
 ```
 Options:
 - `-i, --input <file>`: Input CSS with `@tailwind` directives.
 - `-o, --output <file>`: Output CSS file.
 - `--minify`: Minify output.
 - `--config <file>`: Tailwind config path.
 
 ### Browser (Script Tag)
 ```html
 <script src="https://cdn.jsdelivr.net/npm/@tailwindcss-standalone@latest/dist/tailwind-standalone.min.js"></script>
 <script>
   tailwind({
     content: ['./index.html'],
     css: ['@tailwindcss/base','@tailwindcss/components','@tailwindcss/utilities'],
   }).then(css => {
     const style = document.createElement('style');
     style.textContent = css;
     document.head.appendChild(style);
   });
 </script>
 ```
 Global function: `tailwind(config): Promise<string>`.
 
 ### Node.js Import
 ```js
 import tailwind from '@tailwindcss-standalone';
 
 tailwind({
   content: ['./src/**/*.html'],
   css: ['@tailwindcss/base','@tailwindcss/components','@tailwindcss/utilities'],
   minify: true,
 }).then(result => console.log(result.css));
 ```
 CommonJS:
 ```js
 const tailwind = require('@tailwindcss-standalone');
 ```
 
 ## API Reference
 
 ### tailwind(options): Promise<{ css: string, map?: SourceMap }>
 | Option  | Type     | Description                             |
 | ------- | -------- | --------------------------------------- |
 | content | string[] | File paths or globs for content files.  |
 | css     | string[] | Entry CSS layers or file imports.       |
 | config  | object   | Tailwind configuration object.          |
 | input   | string   | Input CSS file path (CLI only).         |
 | output  | string   | Output file path (CLI only).            |
 | minify  | boolean  | Minify generated CSS.                   |
 | map     | boolean  | Generate source map.                    |
 
 ## Change Notes (V3 → V4)
 - **Package Renamed**: Now `@tailwindcss-standalone`.
 - **API Updated**: Use `tailwind(config)` instead of `interpretCSS/build`.
 - **CLI Flag Change**: `--minify` replaces `--optimize`.
 - **Global Export**: Browser global is `tailwind`.
 - **Engine Upgrade**: Bundled Tailwind engine upgraded to v4.
# upgrade.md

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
# vite.md

 # @tailwindcss/vite — Vite Plugin Documentation

 ## Import Path

 ```js
 import plugin from '@tailwindcss/vite';
 ```

 ## Installation

 ```bash
 npm install -D @tailwindcss/vite tailwindcss
 ```

 ## Plugin Options & Types

 ```ts
 import type { Plugin } from 'vite';
 import type { Config as TailwindConfig } from 'tailwindcss';

 export interface TailwindVitePluginOptions {
   /** Path to a Tailwind config file or inline config object */
   config?: string | Partial<TailwindConfig>;
   /** Automatically inject Tailwind directives into your CSS */
   inject?: boolean;
 }

 export default function plugin(
   options?: TailwindVitePluginOptions
 ): Plugin;
 ```

 - **config**: `string` or partial Tailwind config. Defaults to `undefined` (uses default `tailwind.config.js`).
 - **inject**: `boolean`. Defaults to `true`; when `false`, you must import Tailwind directives manually.

 ## Usage in `vite.config.js`

 ```js
 import { defineConfig } from 'vite';
 import tailwind from '@tailwindcss/vite';

 export default defineConfig({
   plugins: [
     tailwind({
       config: 'tailwind.config.js',
       inject: true,
     }),
   ],
 });
 ```

 ## Examples

 **1. Inline config**
 ```js
 import tailwind from '@tailwindcss/vite';

 export default {
   plugins: [
     tailwind({
       config: { theme: { extend: { colors: { primary: '#1E40AF' } } } },
     }),
   ],
 };
 ```

 **2. Disable auto-injection**
 ```js
 tailwind({ inject: false });
 ```

 ## V3 → V4 Changes

 - Renamed package from `vite-plugin-tailwindcss` to `@tailwindcss/vite`.
 - Dropped `configPath` option; use `config` instead.
 - Removed legacy `mode` option; rely on Vite’s mode.
 - Enhanced inline config support and exported proper TypeScript types.
# api_change_v3_v4.md

 # API Changes: Tailwind CSS v3 → v4

 This document summarizes the key API additions, changes, and removals when upgrading from Tailwind CSS v3 to v4.

 ## Added in v4.0.0
 - **High-Performance Engine**: New compiler offering up to 5× faster full builds and 100× faster incremental builds.
 - **Modern CSS Features**: Built on cascade layers, registered CSS custom properties with `@property`, and `color-mix()` support.
 - **Zero-Config Installation**: Simplified setup with automatic content file detection and built-in CSS `@import` support.
 - **First-Party Vite Plugin**: Official `@tailwindcss/vite` plugin for seamless Vite integration.
 - **CSS-First Configuration**: Configure and extend Tailwind directly in CSS instead of a JS config file.
 - **CSS Theme Variables**: All design tokens exposed as native CSS variables.
 - **Dynamic Utilities & Variants**: Support for arbitrary values and the new `not-*` variant natively.
 - **Container Queries & 3D Transforms**: New container query utilities and 3D transform support.
 - **Expanded Gradients & Transitions**: Radial/conic gradients, interpolation modes, and `@starting-style` variant.

 ## Changed
 - **@variant renamed to @custom-variant**: The at-rule for custom variants changed name.
 - **Alpha function syntax**: `--alpha(color, 50%)` now uses `--alpha(color / 50%)`.
 - **outline-hidden behavior**: Now sets `outline-style: none` except in forced-colors mode.
 - **--container-prose removed**: Replaced by the deprecated `--max-width-prose` theme variable.
 - **@property fallbacks removed**: No longer emits fallback rules for Firefox.

 ## Removed
 - **IE 11 support**: Dropped the legacy `target` feature.
 - **Legacy shadow utilities**: `shadow-outline`, `shadow-solid`, and `shadow-xs` have been replaced by the Ring API.
 - **negativeMargin plugin**: Removed in favor of negative keys in the standard margin plugin.
