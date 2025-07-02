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