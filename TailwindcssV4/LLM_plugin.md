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