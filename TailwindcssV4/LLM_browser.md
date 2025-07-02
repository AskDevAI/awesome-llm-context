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