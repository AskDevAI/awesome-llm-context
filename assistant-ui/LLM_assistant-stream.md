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