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