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