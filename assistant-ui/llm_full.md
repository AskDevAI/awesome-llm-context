# assistant-stream.md

# assistant-stream

**Description**: Stream processing utilities for assistant responses.

## Installation
```bash
npm install @assistant-ui/assistant-stream
```

## API
- `streamAssistant(options: StreamOptions): AsyncIterable<string>` — stream assistant response chunks.

**Example**:
```js
import { streamAssistant } from '@assistant-ui/assistant-stream';
for await (const chunk of streamAssistant({ prompt: 'Hello' })) console.log(chunk);
```
# cli.md

 # `assistant-ui` CLI Documentation

 This document provides reference for the public commands exposed by the `assistant-ui` CLI package.

 **Import path:**
 ```ts
 import { add, create, init, update, codemodCommand, upgradeCommand } from 'assistant-ui/packages/cli';
 ```

 ## add

 **Signature**
 ```ts
 import { add } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Add one or more components to your project.

 **Arguments**
 - `<components...>`: string[] – The components to add (e.g., `Button`, `Input`).

 **Options**
 - `-y, --yes` (boolean, default: `true`): Skip confirmation prompt.
 - `-o, --overwrite` (boolean, default: `false`): Overwrite existing files.
 - `-c, --cwd <cwd>` (string, default: `process.cwd()`): Working directory.
 - `-p, --path <path>` (string): Path to add the component to.

 **Usage**
 ```bash
 assistant-ui add Button Input --yes --path src/components
 ```

 ## create

 **Signature**
 ```ts
 import { create } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Create a new project.

 **Arguments**
 - `[project-directory]` (string, optional): Name of the project directory.

 **Options**
 - `-t, --template <template>` (string): Template to use (`default`, `langgraph`, `mcp`).
 - `--use-npm`: Use npm to bootstrap.
 - `--use-pnpm`: Use pnpm to bootstrap.
 - `--use-yarn`: Use Yarn to bootstrap.
 - `--use-bun`: Use Bun to bootstrap.
 - `--skip-install`: Skip installing dependencies.

 **Usage**
 ```bash
 assistant-ui create my-app --template langgraph --use-pnpm
 ```

 ## init

 **Signature**
 ```ts
 import { init } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Initialize Assistant-UI in a new or existing project.

 **Usage**
 ```bash
 assistant-ui init
 ```

 ## update

 **Signature**
 ```ts
 import { update } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Update all `@assistant-ui/*` and `assistant-stream` packages to their latest versions.

 **Options**
 - `--dry` (boolean): Print the update command without executing.

 **Usage**
 ```bash
 assistant-ui update --dry
 ```

 ## codemod

 **Signature**
 ```ts
 import { codemodCommand } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Run codemods on source files.

 **Arguments**
 - `<codemod>` (string): The codemod to run (e.g., `rewrite-framework-imports`).
 - `<source>` (string): Path to files or directory to transform.

 **Options**
 - `-d, --dry`: Dry run (no file changes).
 - `-p, --print`: Print transformed output to stdout.
 - `--verbose`: Show detailed transform logs.
 - `-j, --jscodeshift <options>`: Options passed to jscodeshift.

 **Usage**
 ```bash
 assistant-ui codemod rewrite-framework-imports src/
 ```

 ## upgrade

 **Signature**
 ```ts
 import { upgradeCommand } from 'assistant-ui/packages/cli';
 ```

 **Description**
 Upgrade AI package dependencies and apply codemods.

 **Options**
 - `-d, --dry`: Dry run (no file changes).
 - `-p, --print`: Print transformed output to stdout.
 - `--verbose`: Show detailed transform logs.
 - `-j, --jscodeshift <options>`: Options passed to jscodeshift.

 **Usage**
 ```bash
 assistant-ui upgrade --dry
 ```
# cloud.md

 # Cloud Package API

 Import the client and types:
 ```ts
 import { AssistantCloud, CloudMessage } from 'assistant-ui/packages/cloud';
 ```

 ## AssistantCloud
 Primary entry point to interact with Assistant Cloud service.

 ### Constructor
 ```ts
 new AssistantCloud(config: AssistantCloudConfig)
 ```
 - **config**: Authentication settings:
   - `{ baseUrl: string; authToken: () => Promise<string | null> }`
   - `{ apiKey: string; userId: string; workspaceId: string }`
   - `{ baseUrl: string; anonymous: true }`

 ### Properties
 - `threads`: AssistantCloudThreads — manage conversation threads
 - `auth.tokens`: AssistantCloudAuthTokens — handle JWT tokens
 - `runs`: AssistantCloudRuns — start and stream runs
 - `files`: AssistantCloudFiles — file operations

 ## AssistantCloudThreads
 Manage thread lifecycle and nested messages.

 ```ts
 threads.list(query?: { is_archived?: boolean; limit?: number; after?: string })
   : Promise<{ threads: CloudThread[] }>;
 threads.create(body: { title?: string; last_message_at: Date; metadata?: unknown; external_id?: string })
   : Promise<{ thread_id: string }>;
 threads.update(id: string, body: { title?: string; last_message_at?: Date; metadata?: unknown; is_archived?: boolean })
   : Promise<void>;
 threads.delete(id: string): Promise<void>;
 threads.messages: AssistantCloudThreadMessages
 ```

 ### Types
 ```ts
 type CloudThread = {
   id: string;
   title: string;
   last_message_at: Date;
   is_archived: boolean;
   external_id: string | null;
   project_id: string;
   workspace_id: string;
   metadata: unknown;
   created_at: Date;
   updated_at: Date;
 };
 ```

 ## AssistantCloudThreadMessages
 Handle messages within a thread.

 ```ts
 messages.list(threadId: string)
   : Promise<{ messages: CloudMessage[] }>;
 messages.create(
   threadId: string,
   body: { parent_id: string | null; format: string; content: any }
 ) : Promise<{ message_id: string }>;
 ```

 ## CloudMessage
 Represents a message in a thread.

 ```ts
 type CloudMessage = {
   id: string;
   parent_id: string | null;
   height: number;
   created_at: Date;
   updated_at: Date;
   format: string;
   content: Record<string, any>;
 };
 ```

 ## AssistantCloudFiles
 File-related operations.

 ```ts
 files.pdfToImages(body: { file_blob?: string; file_url?: string })
   : Promise<{ success: boolean; urls: string[]; message: string }>;
 files.generatePresignedUploadUrl(
   body: { filename: string }
 ) : Promise<{ success: boolean; signedUrl: string; expiresAt: string; publicUrl: string }>;
 ```

 ## AssistantCloudRuns
 Start and stream AI runs.

 ```ts
 runs.stream(
   body: { thread_id: string; assistant_id: string; messages: readonly any[] }
 ) : Promise<import('assistant-stream').AssistantStream>;
 ```

 ## Auth Types
 ```ts
 type AssistantCloudConfig =
   | { baseUrl: string; authToken: () => Promise<string | null> }
   | { apiKey: string; userId: string; workspaceId: string }
   | { baseUrl: string; anonymous: true };
 ```
# create-assistant-ui.md

 # create-assistant-ui

 Command-line tool to scaffold a new assistant-ui project.

 ## Installation

 Install via npx (no global install required):
 ```sh
 npx create-assistant-ui [project-directory] [options]
 ```
 Or install globally:
 ```sh
 npm install -g create-assistant-ui
 ```

 ## Usage

 ```sh
 create-assistant-ui [project-directory] [options]
 ```

 **Arguments**

 - `project-directory` (optional): Name of the folder for the new project.

 **Options**

 - `-t, --template <template>`: Template to use (default, langgraph).
 - `--use-npm`     : Bootstrap with npm.
 - `--use-pnpm`    : Bootstrap with pnpm.
 - `--use-yarn`    : Bootstrap with Yarn.
 - `--use-bun`     : Bootstrap with Bun.
 - `--skip-install`: Skip installing dependencies.

 ## Examples

 - Create a default project:
   ```sh
   npx create-assistant-ui my-app
   ```

 - Create a project with the 'langgraph' template using pnpm:
   ```sh
   npx create-assistant-ui my-app -t langgraph --use-pnpm
   ```

 ## Notes

 This package exposes a CLI only; no programmatic API is exported.
# python-assistant-stream-hello-world.md

 # assistant-stream-hello-world Python API

 This document describes the public API exposed by the `assistant-stream-hello-world` Python package.

 ## Module
 **assistant_stream_hello_world.api.chat.completions**

 ### chat_completions()

 **Import**
 ```python
 from assistant_stream_hello_world.api.chat.completions import chat_completions
 ```

 **Signature**
 ```python
 async def chat_completions() -> DataStreamResponse
 ```

 **Description**
 Streams chat completions by sending partial text in a server-sent events style. In this implementation, it emits "Hello " then "world." with a short delay.

 **Usage Example**
 ```bash
 # Start the FastAPI app
 uvicorn assistant_stream_hello_world.api.chat.completions:app --reload

 # In another terminal, request the streaming endpoint
 curl -N -X POST http://localhost:8000/api/chat/completions
 ```

 **Python client example**
 ```python
 import httpx

 async def fetch_stream():
     async with httpx.AsyncClient() as client:
         async with client.stream("POST", "http://localhost:8000/api/chat/completions") as response:
             async for chunk in response.aiter_text():
                 print(chunk, end="")

 # Run with: import asyncio; asyncio.run(fetch_stream())
 ```
# python-state-test.md

 # state-test Python API
 
 This document provides an overview of the `state-test` FastAPI endpoints for testing state streaming behavior.
 
 ## simple_test
 **Endpoint:** POST `/simple-test`
 
 **Signature:**
 ```python
 async def simple_test() -> DataStreamResponse
 ```
 
 **Description:**
 Starts a simple state test run, streaming incremental state updates and appended text.
 
 **Usage Example:**
 ```python
 from state_test.server import app
 from fastapi.testclient import TestClient
 
 client = TestClient(app)
 response = client.post("/simple-test")
 print(response.text)
 ```
 
 ## complex_test
 **Endpoint:** POST `/complex-test`
 
 **Signature:**
 ```python
 async def complex_test() -> DataStreamResponse
 ```
 
 **Description:**
 Runs a complex state test with nested dictionaries, lists, timed updates, and toggling themes, streaming state changes.
 
 **Usage Example:**
 ```python
 from state_test.server import app
 from fastapi.testclient import TestClient
 
 client = TestClient(app)
 response = client.post("/complex-test")
 print(response.text)
 ```
 
 ## string_test
 **Endpoint:** POST `/string-test`
 
 **Signature:**
 ```python
 async def string_test() -> DataStreamResponse
 ```
 
 **Description:**
 Performs string manipulations over time (append, uppercase, contains, length) and streams updates.
 
 **Usage Example:**
 ```python
 from state_test.server import app
 from fastapi.testclient import TestClient
 
 client = TestClient(app)
 response = client.post("/string-test")
 print(response.text)
 ```
 
 ## list_test
 **Endpoint:** POST `/list-test`
 
 **Signature:**
 ```python
 async def list_test() -> DataStreamResponse
 ```
 
 **Description:**
 Streams updates as a list is built, extended, accessed, and cleared over time.
 
 **Usage Example:**
 ```python
 from state_test.server import app
 from fastapi.testclient import TestClient
 
 client = TestClient(app)
 response = client.post("/list-test")
 print(response.text)
 ```
 
 ## dict_test
 **Endpoint:** POST `/dict-test`
 
 **Signature:**
 ```python
 async def dict_test() -> DataStreamResponse
 ```
 
 **Description:**
 Streams dictionary operations: setting keys, defaults, getting values, listing keys/values, and clearing.
 
 **Usage Example:**
 ```python
 from state_test.server import app
 from fastapi.testclient import TestClient
 
 client = TestClient(app)
 response = client.post("/dict-test")
 print(response.text)
 ```
# react-hook-form.md

 # react-hook-form

 ## useAssistantForm
 
 **Signature**  
 ```ts
 function useAssistantForm<TFieldValues = FieldValues, TContext = any, TTransformedValues = TFieldValues>(
   props?: UseAssistantFormProps<TFieldValues, TContext, TTransformedValues>
 ): UseFormReturn<TFieldValues, TContext, TTransformedValues>;
 ```

 **Description**  
 Custom hook integrating react-hook-form with assistant runtime. It registers form context providers for the assistant to interact with form fields and submission.

 **Import**  
 ```ts
 import { useAssistantForm } from 'assistant-ui/packages/react-hook-form';
 ```

 **Example**  
 ```tsx
 import React from 'react';
 import { useAssistantForm } from 'assistant-ui/packages/react-hook-form';

 type FormData = { firstName: string; lastName: string; };

 const MyForm = () => {
   const { register, handleSubmit } = useAssistantForm<FormData>();
   const onSubmit = data => console.log(data);

   return (
     <form onSubmit={handleSubmit(onSubmit)}>
       <input {...register('firstName')} placeholder="First Name" />
       <input {...register('lastName')} placeholder="Last Name" />
       <button type="submit">Submit</button>
     </form>
   );
 };
 ```

 ## formTools

 **Description**  
 Tools for assistant to interact with form fields and submission.

 **Import**  
 ```ts
 import { formTools } from 'assistant-ui/packages/react-hook-form';
 ```

 ### set_form_field

 **Signature**  
 ```ts
 set_form_field: {
   description: string;
   parameters: { name: string; value: string };
 }
 ```

 **Description**  
 Sets a form field. Call this function as soon as the user provides the data for each field.

 **Example**  
 ```ts
 import { formTools } from 'assistant-ui/packages/react-hook-form';

 // Within assistant tool execution context:
 await formTools.set_form_field.execute({ name: 'email', value: 'user@example.com' });
 ```

 ### submit_form

 **Signature**  
 ```ts
 submit_form: {
   description: string;
   parameters: {};
 }
 ```

 **Description**  
 Submits the form. Confirm with user before submitting.

 **Example**  
 ```ts
 import { formTools } from 'assistant-ui/packages/react-hook-form';

 // Within assistant tool execution context:
 await formTools.submit_form.execute({});
 ```
# react-langgraph.md

 # @assistant-ui/react-langgraph
 Integration package for LangGraph features in React.

 **Import Path**
 ```ts
 import {
   useLangGraphRuntime,
   useLangGraphSend,
   useLangGraphSendCommand,
   useLangGraphInterruptState,
   useLangGraphMessages,
   convertLangChainMessages,
   LangGraphMessageAccumulator,
   appendLangChainChunk
 } from 'assistant-ui/packages/react-langgraph';
 ```

 ## Hooks

 ### useLangGraphRuntime
 ```ts
 function useLangGraphRuntime(options: {
   stream: LangGraphStreamCallback<LangChainMessage>;
   threadId?: string;
   autoCancelPendingToolCalls?: boolean;
   unstable_allowCancellation?: boolean;
   adapters?: {
     attachments?: AttachmentAdapter;
     feedback?: FeedbackAdapter;
     speech?: SpeechSynthesisAdapter;
   };
   eventHandlers?: {
     onMetadata?: OnMetadataEventCallback;
     onInfo?: OnInfoEventCallback;
     onError?: OnErrorEventCallback;
     onCustomEvent?: OnCustomEventCallback;
   };
 }): {
   interrupt: LangGraphInterruptState | undefined;
   messages: LangChainMessage[];
   sendMessage: (msgs: LangChainMessage[], config: LangGraphSendMessageConfig) => Promise<void>;
   cancel: () => void;
   isRunning: boolean;
 };
 ```
 Manages streaming lifecycle, message state, and interrupts.

 **Example**
 ```tsx
 const { messages, sendMessage } = useLangGraphRuntime({
   stream: async (msgs, config) => { /* implement generator */ },
 });
 ```

 ### useLangGraphSend
 ```ts
 function useLangGraphSend(): (msgs: LangChainMessage[], config: LangGraphSendMessageConfig) => Promise<void>;
 ```
 Returns the `sendMessage` function from `useLangGraphRuntime`.

 ### useLangGraphSendCommand
 ```ts
 function useLangGraphSendCommand(): (command: LangGraphCommand) => Promise<void>;
 ```
 Sends a single command without prior messages.

 ### useLangGraphInterruptState
 ```ts
 function useLangGraphInterruptState(): LangGraphInterruptState | undefined;
 ```
 Retrieves the current interrupt state from runtime.

 ### useLangGraphMessages
 ```ts
 function useLangGraphMessages<TMessage extends { id?: string }>(options: {
   stream: LangGraphStreamCallback<TMessage>;
   appendMessage?: (prev?: TMessage, curr: TMessage) => TMessage;
   eventHandlers?: { /* callbacks */ };
 }): {
   interrupt: LangGraphInterruptState | undefined;
   messages: TMessage[];
   sendMessage: (msgs: TMessage[], config: LangGraphSendMessageConfig) => Promise<void>;
   cancel: () => void;
   setInterrupt: (state: LangGraphInterruptState) => void;
   setMessages: (msgs: TMessage[]) => void;
 };
 ```
 Core hook for low-level stream handling.

 ## Utilities

 ### convertLangChainMessages
 ```ts
 function convertLangChainMessages(message: LangChainMessage): ExternalMessage;
 ```
 Converts LangChain messages to external format.

 ### LangGraphMessageAccumulator
 Class to accumulate and merge streaming message chunks.

 ### appendLangChainChunk
 ```ts
 function appendLangChainChunk(prev?: LangChainMessage, curr: LangChainMessage): LangChainMessage;
 ```
 Appends partial message chunks into full messages.

 ## Types

 - **LangGraphCommand**: `{ resume: string }`
 - **LangGraphInterruptState**: `{ value?: any; resumable?: boolean; when: string; ns?: string[] }`
 - **LangGraphSendMessageConfig**: `{ command?: LangGraphCommand; runConfig?: unknown }`
 - **LangGraphStreamCallback<TMessage>**: `(msgs: TMessage[], config: LangGraphSendMessageConfig & { abortSignal: AbortSignal }) => AsyncGenerator<LangGraphMessagesEvent<TMessage>> | Promise<...>`
 - **LangGraphMessagesEvent<TMessage>**: `{ event: EventType; data: TMessage[] | any }`
 - **LangChainMessage**: union of system/human/tool/ai message shapes.
 - **LangChainEvent**: `{ event: 'messages/partial'|'messages/complete'; data: LangChainMessage[] }`
 - **LangChainToolCall** and **LangChainToolCallChunk**: types for tool call metadata.
# react-markdown.md

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
