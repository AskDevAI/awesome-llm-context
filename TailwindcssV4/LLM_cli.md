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