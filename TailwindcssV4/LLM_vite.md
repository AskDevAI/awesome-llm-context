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