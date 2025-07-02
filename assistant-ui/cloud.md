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