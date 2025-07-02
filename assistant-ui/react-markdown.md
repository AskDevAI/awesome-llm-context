 # react-markdown

 **Import**
 ```ts
 import {
   MarkdownTextPrimitive,
   useIsMarkdownCodeBlock,
   unstable_memoizeMarkdownComponents
 } from 'assistant-ui/packages/react-markdown';
 ```

 ## MarkdownTextPrimitive

 **Signature**  
 `const MarkdownTextPrimitive: React.ForwardRefExoticComponent<
   MarkdownTextPrimitiveProps & React.RefAttributes<HTMLDivElement>
 >`

 **Description**  
 Renders markdown text with customizable components and syntax highlighting.

 **Props**
 - `className?: string` — CSS class for root container.
 - `containerComponent?: React.ElementType` — Container element type (default `'div'`).
 - `containerProps?: React.HTMLAttributes<HTMLDivElement>` — Props forwarded to container.
 - `components?` — Custom overrides for markdown elements.
 - `componentsByLanguage?` — Language-specific component overrides.
 - `smooth?: boolean` — Enable smooth rendering (default `true`).
 - Other props from `react-markdown` `Options` (e.g., `children`, `remarkPlugins`).

 **Usage**
 ```tsx
 <MarkdownTextPrimitive
   children="## Heading"
   className="markdown"
 />
 ```

 ## useIsMarkdownCodeBlock

 **Signature**  
 `function useIsMarkdownCodeBlock(): boolean`

 **Description**  
 Hook that returns `true` when rendering inside a markdown code block.

 **Usage**
 ```ts
 const isCode = useIsMarkdownCodeBlock();
 ```

 ## unstable_memoizeMarkdownComponents

 **Signature**  
 `function unstable_memoizeMarkdownComponents(
   components?: Record<string, React.ComponentType<any>>
 ): Record<string, React.ComponentType<any>>`

 **Description**  
 Wraps component map entries in `React.memo` to optimize rendering.

 **Usage**
 ```ts
 const memoized = unstable_memoizeMarkdownComponents({ pre: CustomPre });
 ```

 ## Types

 ### MarkdownTextPrimitiveProps

 Extend `react-markdown` `Options` (excluding `components` and `children`) with:
 - `className?`, `containerComponent?`, `containerProps?`, `components?`, `componentsByLanguage?`, `smooth?`.

 ### CodeHeaderProps
 Defined in `src/overrides/types.ts`:
 ```ts
 export type CodeHeaderProps = {
   node: Element;
   className?: string;
   children?: string;
 };
 ```

 ### SyntaxHighlighterProps
 Defined in `src/overrides/types.ts`:
 ```ts
 export type SyntaxHighlighterProps = {
   node?: Element;
   children?: string;
   style?: object;
 };
 ```