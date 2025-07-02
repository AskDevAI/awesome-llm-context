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