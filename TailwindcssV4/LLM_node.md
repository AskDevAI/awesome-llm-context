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