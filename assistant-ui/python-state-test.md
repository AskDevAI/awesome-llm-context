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