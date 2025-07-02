```markdown
# FastAPI Responses & Exception Handlers

## fastapi.responses

```python
from fastapi.responses import (
    Response, JSONResponse, UJSONResponse, ORJSONResponse,
    HTMLResponse, PlainTextResponse, RedirectResponse,
    StreamingResponse, FileResponse,
)
```

### JSONResponse
Serializes content to JSON.
```python
JSONResponse(content: Any, status_code: int = 200, headers: dict = None)
```

### UJSONResponse
Uses `ujson` for serialization.
```python
class UJSONResponse(JSONResponse):
    def render(self, content: Any) -> bytes
```

### ORJSONResponse
Uses `orjson` with performance optimizations.

### HTMLResponse / PlainTextResponse / RedirectResponse
Aliases of Starlette’s classes, same signatures.

### StreamingResponse
Streams iterables (sync or async).

### FileResponse
Serves disk files.

---

## fastapi.exception_handlers

```python
from fastapi.exception_handlers import (
    http_exception_handler,
    request_validation_exception_handler,
    websocket_request_validation_exception_handler,
)
```

### http_exception_handler
```python
async def http_exception_handler(
    request: Request, exc: HTTPException
) -> Response
```

### request_validation_exception_handler
```python
async def request_validation_exception_handler(
    request: Request, exc: RequestValidationError
) -> JSONResponse
```

### websocket_request_validation_exception_handler
```python
async def websocket_request_validation_exception_handler(
    websocket: WebSocket, exc: WebSocketRequestValidationError
) -> None
```

### Example: Custom Handler
```python
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse
from fastapi.exceptions import HTTPException

app = FastAPI()

@app.exception_handler(HTTPException)
async def custom_http_exception_handler(request: Request, exc: HTTPException):
    return JSONResponse({"error": exc.detail}, status_code=exc.status_code)
```
```