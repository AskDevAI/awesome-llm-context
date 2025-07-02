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