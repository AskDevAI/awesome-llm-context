# FastAPI API Reference

A consolidated API reference for core, user-facing FastAPI modules.

## Table of Contents
1. [Core Modules](#core-modules)
2. [Routing](#routing)
3. [Dependencies](#dependencies)
4. [Security](#security)
5. [Parameters](#parameters)
6. [Responses & Exceptions](#responses--exceptions)
7. [OpenAPI & Docs](#openapi--docs)
8. [Background Tasks & TestClient](#background-tasks--testclient)

---

## Core Modules
# FastAPI Core Modules API Reference

This document covers the public API for three core FastAPI modules under the package root:

- `fastapi/applications.py`  
- `fastapi/cli.py`  
- `fastapi/__main__.py`  

---

## 1. fastapi.applications.FastAPI

```python
from fastapi.applications import FastAPI
```

### Class Definition

```python
class FastAPI(Starlette)
```

**Description**  
The main application class for FastAPI. Inherits from Starlette and adds OpenAPI/schema support, docs UIs, and convenient decorators.

### Constructor

```python
def __init__(
    self: AppType,
    *,
    debug: bool = False,
    routes: Optional[List[BaseRoute]] = None,
    title: str = "FastAPI",
    version: str = "0.1.0",
    openapi_url: Optional[str] = "/openapi.json",
    openapi_tags: Optional[List[Dict[str, Any]]] = None,
    servers: Optional[List[Dict[str, Any]]] = None,
    dependencies: Optional[Sequence[Depends]] = None,
    default_response_class: Type[Response] = JSONResponse,
    docs_url: Optional[str] = "/docs",
    redoc_url: Optional[str] = "/redoc",
    swagger_ui_oauth2_redirect_url: Optional[str] = "/docs/oauth2-redirect",
    swagger_ui_init_oauth: Optional[Dict[str, Any]] = None,
    openapi_prefix: str = "",
    on_startup: Optional[List[Callable[[], Any]]] = None,
    on_shutdown: Optional[List[Callable[[], Any]]] = None,
    middleware: Optional[List[Middleware]] = None,
    exception_handlers: Dict[Union[int, Type[Exception]], Callable] = None,
    root_path: str = "",
    root_path_in_servers: bool = True
) -> None
```

| Parameter                           | Type                                                          | Default            | Description                                                                       |
|-------------------------------------|---------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------------|
| `debug`                             | `bool`                                                        | `False`            | Enable debug mode (detailed errors, auto‐reload).                                 |
| `routes`                            | `List[BaseRoute]`                                             | `None`             | Custom list of routes instead of using decorators.                                |
| `title`                             | `str`                                                         | `"FastAPI"`        | OpenAPI schema title.                                                             |
| `version`                           | `str`                                                         | `"0.1.0"`          | OpenAPI schema version.                                                           |
| `openapi_url`                       | `Optional[str]`                                               | `"/openapi.json"`  | URL path for OpenAPI schema (set to `None` to disable).                          |
| `openapi_tags`                      | `Optional[List[Dict[str, Any]]]`                              | `None`             | List of OpenAPI tags (`name`, `description`, `externalDocs`).                     |
| `servers`                           | `Optional[List[Dict[str, Any]]]`                              | `None`             | List of server definitions for OpenAPI.                                           |
| `dependencies`                      | `Optional[Sequence[Depends]]`                                 | `None`             | Global dependencies applied to all routes.                                        |
| `default_response_class`            | `Type[Response]`                                              | `JSONResponse`     | Default response class for endpoints.                                             |
| `docs_url`                          | `Optional[str]`                                               | `"/docs"`          | URL path for Swagger UI.                                                          |
| `redoc_url`                         | `Optional[str]`                                               | `"/redoc"`         | URL path for ReDoc.                                                               |
| `swagger_ui_oauth2_redirect_url`    | `Optional[str]`                                               | `"/docs/oauth2-redirect"` | OAuth2 redirect URL for Swagger UI.                                      |
| `swagger_ui_init_oauth`             | `Optional[Dict[str, Any]]`                                    | `None`             | Init OAuth settings for Swagger UI.                                               |
| `openapi_prefix`                    | `str`                                                         | `""`               | Prefix for all OpenAPI URLs.                                                      |
| `on_startup`                        | `Optional[List[Callable[[], Any]]]`                           | `None`             | Callbacks to run on application startup.                                          |
| `on_shutdown`                       | `Optional[List[Callable[[], Any]]]`                           | `None`             | Callbacks to run on application shutdown.                                         |
| `middleware`                        | `Optional[List[Middleware]]`                                  | `None`             | List of ASGI middleware to apply.                                                 |
| `exception_handlers`                | `Dict[Union[int, Type[Exception]], Callable]`                 | `{}`               | Custom exception handlers by status code or exception class.                      |
| `root_path`                         | `str`                                                         | `""`               | Mount path prefix (e.g., behind a proxy).                                         |
| `root_path_in_servers`              | `bool`                                                        | `True`             | Include `root_path` in OpenAPI servers definitions.                               |

**Usage Example**

```python
from fastapi import FastAPI

app = FastAPI(
    debug=True,
    title="My API",
    version="1.2.3",
    openapi_tags=[{"name": "users", "description": "User operations"}]
)

@app.get("/ping")
def ping() -> dict:
    return {"ping": "pong"}
```

---

## 2. fastapi.cli.main

```python
from fastapi.cli import main
```

### Function

```python
def main() -> None
```

**Description**  
Entry point for the `fastapi` command-line tool. Checks that `fastapi[standard]` is installed; if not, prints an install hint and raises `RuntimeError`. Otherwise delegates to `fastapi_cli.cli.main`.

**Usage**

```bash
$ fastapi
# or, if installed as a module
$ python -m fastapi
```

---

## 3. fastapi.__main__.py

```python
# fastapi/__main__.py
from fastapi.cli import main

main()
```

**Description**  
Enables running FastAPI as a module:

```bash
python -m fastapi
```

This invokes `fastapi.cli.main()`, providing the same behavior as the `fastapi` console script.
```

## Routing
# fastapi.routing — Routing API Reference

This document describes the public routing API in `fastapi/routing.py`:
- `APIRoute`
- `APIRouter` (decorators and methods)

---

## `APIRoute`

```python
from fastapi.routing import APIRoute
from starlette.responses import Response, JSONResponse

class APIRoute(routing.Route):
    def __init__(
        self,
        path: str,
        endpoint: Callable[..., Any],
        *,
        response_model: Any = Default(None),
        status_code: Optional[int] = None,
        tags: Optional[List[Union[str, Enum]]] = None,
        dependencies: Optional[Sequence[Depends]] = None,
        summary: Optional[str] = None,
        description: Optional[str] = None,
        response_description: str = "Successful Response",
        responses: Optional[Dict[Union[int, str], Dict[str, Any]]] = None,
        deprecated: Optional[bool] = None,
        name: Optional[str] = None,
        methods: Optional[Union[Set[str], List[str]]] = None,
        response_class: Union[Type[Response], DefaultPlaceholder] = Default(JSONResponse),
        callbacks: Optional[List[BaseRoute]] = None,
        openapi_extra: Optional[Dict[str, Any]] = None,
        generate_unique_id_function: Callable[["APIRoute"], str] = Default(generate_unique_id),
    ) -> None
```

- **`path`**: URL template (e.g. `/items/{id}`).
- **`endpoint`**: handler function (sync or async).
- **`methods`**: HTTP methods (default `['GET']`).
- **`response_model`**, **`response_class`**, **tags**, **summary**, etc. for OpenAPI.

---

## `APIRouter`

```python
from fastapi.routing import APIRouter
from fastapi import Depends
from starlette.responses import Response, JSONResponse

class APIRouter(routing.Router):
    """
    Group path operations and include in FastAPI.
    """

    def __init__(
        self,
        *,
        prefix: str = "",
        tags: Optional[List[Union[str, Enum]]] = None,
        dependencies: Optional[Sequence[Depends]] = None,
        default_response_class: Type[Response] = JSONResponse,
        responses: Optional[Dict[Union[int, str], Dict[str, Any]]] = None,
        callbacks: Optional[List[BaseRoute]] = None,
        routes: Optional[List[BaseRoute]] = None,
        include_in_schema: bool = True,
    ) -> None
```

### Decorator Methods

```python
# HTTP
def get(self, path: str, *, ...) -> Callable[[Callable], Callable]
# Similarly post, put, delete, patch, options, head, trace
```

### Core Methods

```python
def add_api_route(
    self,
    path: str,
    endpoint: Callable[..., Any],
    *,
    methods: List[str],
    **kwargs_for_APIRoute
) -> None

def add_websocket_route(
    self,
    path: str,
    endpoint: Callable[..., Any],
    name: Optional[str] = None
) -> None

def include_router(
    self,
    router: "APIRouter",
    *,
    prefix: str = "",
    tags: List[str] = None,
    dependencies: Sequence[Depends] = None,
    responses: Dict[Union[int,str], Dict[str, Any]] = None,
    default_response_class: Type[Response] = JSONResponse,
    callbacks: List[BaseRoute] = None,
    route_class: Type[BaseRoute] = APIRoute,
    include_in_schema: bool = True
) -> None
```

---

## Usage Examples

### Simple Route

```python
from fastapi import FastAPI
from fastapi.routing import APIRouter

app = FastAPI()
router = APIRouter(prefix="/items", tags=["items"])

@router.get("/{item_id}", response_model=dict, status_code=200)
async def read_item(item_id: int):
    return {"item_id": item_id}

app.include_router(router)
```

### WebSocket Route

```python
from fastapi import FastAPI, WebSocket

app = FastAPI()

async def websocket_endpoint(ws: WebSocket):
    await ws.accept()
    await ws.send_text("Hello WebSocket")
    await ws.close()

app.add_websocket_route("/ws", websocket_endpoint)
```

---
```markdown
Nested routers, etc.
```

## Dependencies
# fastapi.dependencies — Dependency Injection API Reference

This document covers public classes and functions in the `fastapi.dependencies` package.

---

## 1. fastapi.dependencies.models

```python
from fastapi.dependencies.models import Dependant, SecurityRequirement
from fastapi.security.base import SecurityBase
```

### SecurityRequirement
```python
@dataclass
class SecurityRequirement:
    security_scheme: SecurityBase
    scopes: Optional[Sequence[str]] = None
```
- **`security_scheme`**: a `SecurityBase` (e.g. OAuth2).
- **`scopes`**: optional list of OAuth2 scopes.

### Dependant
```python
@dataclass
class Dependant:
    path_params: List[ModelField]
    query_params: List[ModelField]
    header_params: List[ModelField]
    cookie_params: List[ModelField]
    body_params: List[ModelField]
    dependencies: List[Dependant]
    security_requirements: List[SecurityRequirement]
    name: Optional[str]
    call: Optional[Callable[..., Any]]
    use_cache: bool
    path: Optional[str]
```
- Holds metadata for a dependency callable and its parameters.

---

## 2. fastapi.dependencies.utils

```python
from fastapi.dependencies.utils import (
    ensure_multipart_is_installed,
    get_param_sub_dependant,
    get_parameterless_sub_dependant,
    get_sub_dependant,
    get_dependant,
    get_flat_dependant,
    get_flat_params,
    solve_dependencies,
)
```

### ensure_multipart_is_installed
```python
def ensure_multipart_is_installed() -> None
```
Raises `RuntimeError` if `python-multipart` is not installed.

### get_param_sub_dependant
```python
def get_param_sub_dependant(
    *, param_name: str, depends: Depends, path: str,
    security_scopes: Optional[List[str]] = None
) -> Dependant
```
Wraps a parameterized `Depends` into a `Dependant`.

### get_parameterless_sub_dependant
```python
def get_parameterless_sub_dependant(
    *, depends: Depends, path: str
) -> Dependant
```

### get_sub_dependant
```python
def get_sub_dependant(
    *, depends: Depends, dependency: Callable[..., Any],
    path: str, name: Optional[str] = None,
    security_scopes: Optional[List[str]] = None
) -> Dependant
```

### get_dependant
```python
def get_dependant(
    *, path: str, call: Callable[..., Any], name: Optional[str] = None,
    security_scopes: Optional[List[str]] = None, use_cache: bool = True
) -> Dependant
```

### get_flat_dependant
```python
def get_flat_dependant(
    dependant: Dependant, *, skip_repeats: bool = False,
    visited: Optional[List[Any]] = None
) -> Dependant
```

### get_flat_params
```python
def get_flat_params(dependant: Dependant) -> List[ModelField]
```

### solve_dependencies
```python
async def solve_dependencies(
    *, request: Union[Request, WebSocket], dependant: Dependant,
    body: Optional[Any] = None, background_tasks: Optional[Any] = None,
    response: Optional[Response] = None,
    dependency_cache: Optional[Dict[Any, Any]] = None,
    async_exit_stack: Any, embed_body_fields: bool = False
) -> Any
```
- Resolves values for all dependencies in a request.

---

## Usage Example

```python
from fastapi import FastAPI, Depends

def common_params(q: str = None, limit: int = 10):
    return {"q": q, "limit": limit}

app = FastAPI()

@app.get("/items/")
async def read_items(params: dict = Depends(common_params)):
    return params
```

## Security
# fastapi.security — Security Schemes Reference

This document summarizes FastAPI’s built-in security schemes under `fastapi.security`.

---

## 1. Base

### SecurityBase
```python
from fastapi.security.base import SecurityBase
```
Base class for all security scheme dependencies.

---

## 2. API Key

### APIKeyBase
```python
from fastapi.security.api_key import APIKeyBase
```
Helper with:
```python
@staticmethod
def check_api_key(api_key: Optional[str], auto_error: bool) -> Optional[str]
```

### APIKeyQuery
```python
from fastapi.security.api_key import APIKeyQuery

class APIKeyQuery(APIKeyBase):
    def __init__(
        self,
        *,
        name: str,
        scheme_name: Optional[str] = None,
        description: Optional[str] = None,
        auto_error: bool = True,
    )
    async def __call__(self, request: Request) -> Optional[str]
```
- **`name`**: query param key.
- **`auto_error`**: if `False`, returns `None` instead of 403.

**Example**
```python
from fastapi import FastAPI, Depends
from fastapi.security import APIKeyQuery

app = FastAPI()
api_key_q = APIKeyQuery(name="api_key")

@app.get("/items")
async def read(api_key: str = Depends(api_key_q)):
    return {"key": api_key}
```

... (similar for APIKeyHeader, APIKeyCookie)

---

## 3. HTTP Schemes

### HTTPBase
```python
from fastapi.security.http import HTTPBase
```
Parses `Authorization` header, returns `HTTPAuthorizationCredentials`.

### HTTPBasic
```python
from fastapi.security.http import HTTPBasic
```
Subclass of `HTTPBase` for Basic auth, returns `HTTPBasicCredentials`.

**Example**
```python
from fastapi import FastAPI, Depends
from fastapi.security import HTTPBasic, HTTPBasicCredentials

app = FastAPI()
basic = HTTPBasic()

@app.get("/users/me")
def me(creds: HTTPBasicCredentials = Depends(basic)):
    return {"user": creds.username}
```

---

## 4. OAuth2

### OAuth2PasswordBearer
```python
from fastapi.security import OAuth2PasswordBearer
```
```python
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/token")
```
Extracts Bearer token from `Authorization` header.

**Example**
```python
from fastapi import FastAPI, Depends

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/token")
app = FastAPI()

@app.get("/users/me")
async def read_users(token: str = Depends(oauth2_scheme)):
    return {"token": token}
```

... (other OAuth2 classes)

---

## 5. OpenID Connect

### OpenIdConnect
```python
from fastapi.security.open_id_connect_url import OpenIdConnect
```
`openIdConnectUrl`: OpenID Connect discovery URL.

---

## 6. Utilities

### get_authorization_scheme_param
```python
from fastapi.security.utils import get_authorization_scheme_param

def get_authorization_scheme_param(
    authorization_header_value: Optional[str]
) -> Tuple[str, str]
```
Splits an `Authorization` header value into `(scheme, param)`.

_End of document._
```

## Parameters
# FastAPI Parameter Definitions

Import parameter functions:
```python
from fastapi import Path, Query, Header, Cookie, Body, Form, File, UploadFile
```

Defined in `fastapi.param_functions.py` and `fastapi.params`.

---

## Path
```python
Path(
    default: Any = ..., *, alias: str = None, title: str = None,
    description: str = None, gt: float = None, ge: float = None,
    lt: float = None, le: float = None,
    min_length: int = None, max_length: int = None,
    pattern: str = None, deprecated: bool = False,
    include_in_schema: bool = True, json_schema_extra: dict = None,
) -> Any
```
- Use for required path params. `default` must be `...`.
- Validators: `gt, ge, lt, le, min_length, max_length, pattern`.

**Example**
```python
@app.get("/items/{item_id}")
def read_item(item_id: int = Path(..., gt=1, description=">1")):
    return {"id": item_id}
```

## Query
```python
Query(
    default: Any = None, *, alias: str = None, title: str = None,
    description: str = None, gt: float = None, ge: float = None,
    lt: float = None, le: float = None,
    min_length: int = None, max_length: int = None,
    pattern: str = None, deprecated: bool = False,
    include_in_schema: bool = True, json_schema_extra: dict = None,
) -> Any
```
- Use for optional query params.

**Example**
```python
@app.get("/search/")
def search(q: str = Query(None, min_length=3)):
    return {"q": q}
```

## Header
```python
Header(
    default: Any = None, *, alias: str = None,
    convert_underscores: bool = True, deprecated: bool = False,
    include_in_schema: bool = True, json_schema_extra: dict = None,
) -> Any
```
- Reads HTTP header, `_`→`-` by default.

## Cookie
```python
Cookie(default: Any = None, *, alias: str = None,
       deprecated: bool = False, include_in_schema: bool = True) -> Any
```

## Body
```python
Body(
    default: Any = None, *, embed: bool = False,
    media_type: str = "application/json",
    example: Any = None, examples: dict = None,
    deprecated: bool = False, include_in_schema: bool = True,
    json_schema_extra: dict = None,
) -> Any
```
- Use for request body. `embed` wraps content under param name.

## Form
```python
Form(default: Any = ..., *, media_type: str = "application/x-www-form-urlencoded",
     deprecated: bool = False, include_in_schema: bool = True) -> Any
```

## File
```python
File(default: Any = ..., *, media_type: str = None,
     deprecated: bool = False, include_in_schema: bool = True) -> UploadFile
```

**Example**
```python
@app.post("/uploadfile/")
async def create_upload_file(file: UploadFile = File(...)):
    return {"filename": file.filename}
```

_End of Parameter Definitions._
```

## Responses & Exceptions
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

## OpenAPI & Docs
# fastapi.openapi — OpenAPI & Docs API Reference

Utilities under `fastapi.openapi.docs` and `fastapi.openapi.utils`.

---

## Swagger & ReDoc HTML

```python
from fastapi.openapi.docs import get_swagger_ui_html, get_redoc_html
```

### get_swagger_ui_html
Generates HTML response for Swagger UI.
```python
get_swagger_ui_html(
    *, openapi_url: str, title: str,
    swagger_js_url: str, swagger_css_url: str,
    oauth2_redirect_url: str = None,
    init_oauth: Dict[str,Any] = None,
    swagger_ui_parameters: Dict[str,Any] = None,
) -> HTMLResponse
```

### get_redoc_html
Generates HTML for ReDoc.
```python
get_redoc_html(
    *, openapi_url: str, title: str,
    redoc_js_url: str, with_google_fonts: bool = True
) -> HTMLResponse
```

---

## get_openapi

```python
from fastapi.openapi.utils import get_openapi
```
```python
get_openapi(
    *, title: str, version: str,
    routes: Sequence[BaseRoute],
    openapi_version: str = "3.1.0",
    summary: str = None, description: str = None,
    tags: List[Dict[str,Any]] = None,
    servers: List[Dict[str,Any]] = None,
    terms_of_service: str = None,
    contact: Dict[str,Any] = None,
    license_info: Dict[str,Any] = None,
    separate_input_output_schemas: bool = True,
) -> Dict[str,Any]
```
- Builds the OpenAPI schema dict (`info`, `paths`, `components`).

**Example**
```python
app.openapi = lambda: get_openapi(
    title="Custom API", version="1.0.0",
    routes=app.routes
)
```

---

## FastAPI Methods

### app.openapi()
Returns the generated schema dict.

### `/openapi.json` endpoint
```python
@app.get("/openapi.json", include_in_schema=False)
async def openapi(req: Request) -> JSONResponse:
    return JSONResponse(app.openapi())
```

_End of OpenAPI Reference._
```

## Background Tasks & TestClient
# FastAPI Background Tasks & TestClient

## BackgroundTasks
```python
from fastapi.background import BackgroundTasks
```
Collects callables to run after response.

### add_task
```python
def add_task(self, func: Callable[..., Any], *args, **kwargs) -> None
```

**Example**
```python
@app.post("/notify/")
async def notify(bg: BackgroundTasks):
    bg.add_task(write_log, "msg")
    return {"status": "queued"}
```

## run_in_threadpool
```python
from fastapi.concurrency import run_in_threadpool
```
Executes blocking call without blocking event loop.
```python
async def run_in_threadpool(func: Callable[..., T], *args, **kwargs) -> T
```

## TestClient
```python
from fastapi.testclient import TestClient
```
Client for sync tests (requests-based).

**Example**
```python
client = TestClient(app)
resp = client.get("/ping")
```
```


# datastructures
# fastapi.datastructures — Data Structures Reference

Import path:
```python
from fastapi.datastructures import (
    UploadFile,
)
from starlette.datastructures import (
    URL, Address, FormData, Headers, QueryParams, State
)
```

## UploadFile
A subclass of Starlette’s `UploadFile` for async file uploads.

```python
class UploadFile:
    file: BinaryIO  # standard file for non-async use
    filename: Optional[str]
    content_type: Optional[str]
    headers: Headers
    size: Optional[int]

    async def read(self, size: int = -1) -> bytes
    async def write(self, data: bytes) -> None
    async def seek(self, offset: int) -> None
    async def close(self) -> None

    @classmethod
    def validate(cls, v: Any) -> 'UploadFile'
```

**Example**
```python
from fastapi import File, UploadFile, FastAPI

@app.post("/uploadfile/")
async def create_upload_file(file: UploadFile = File(...)):
    data = await file.read()
    return {"filename": file.filename, "size": len(data)}
```

## Starlette Data Structures (re-exported)
- `URL`: URL builder & parser.
- `Address`: client address tuple.
- `FormData`: form fields container.
- `Headers`: immutable HTTP headers mapping.
- `QueryParams`: query string parameters.
- `State`: request state container.

_End of Data Structures Reference._


# utils
# fastapi.encoders & fastapi.concurrency — Utilities Reference

## fastapi.encoders

```python
from fastapi.encoders import jsonable_encoder, ENCODERS_BY_TYPE, encoders_by_class_tuples
```

### ENCODERS_BY_TYPE
A dict mapping types → encoder functions (e.g. `bytes: lambda o: o.decode()`).

### encoders_by_class_tuples
Reverses ENCODERS_BY_TYPE: encoder fn → tuple of handled types.

### jsonable_encoder
```python
def jsonable_encoder(
    obj: Any,
    include: IncEx = None,
    exclude: IncEx = None,
    by_alias: bool = True,
    exclude_unset: bool = False,
    exclude_defaults: bool = False,
    exclude_none: bool = False,
    custom_encoder: Dict[Any,Callable] = None,
    sqlalchemy_safe: bool = True,
) -> Any
```
- Converts Pydantic models, dataclasses, lists, dicts, date, Decimal, Enum, etc., into JSON-serializable types.

**Example**
```python
from fastapi.encoders import jsonable_encoder
from datetime import datetime
from decimal import Decimal
json_data = jsonable_encoder({"time": datetime.utcnow(), "value": Decimal("1.23")})
```

## fastapi.concurrency

```python
from fastapi.concurrency import (
    run_in_threadpool,
    iterate_in_threadpool,
    run_until_first_complete,
    contextmanager_in_threadpool,
)
```

### run_in_threadpool
```python
async def run_in_threadpool(func: Callable[..., T], *args, **kwargs) -> T
```
- Runs blocking `func` in threadpool.

### iterate_in_threadpool
```python
async def iterate_in_threadpool(iterator: Iterator[T]) -> AsyncIterator[T]
```
- Iterates over blocking iterator in threadpool.

### run_until_first_complete
```python
async def run_until_first_complete(
    tasks: Sequence[Tuple[str, Callable, Tuple[Any,...]]],
    timeout: float = None,
) -> Dict[str, Any]
```
- Runs tasks concurrently; returns first-completed result.

### contextmanager_in_threadpool
```python
@asynccontextmanager
async def contextmanager_in_threadpool(cm: ContextManager[T]) -> AsyncGenerator[T,None]
```
- Runs blocking context manager in threadpool without blocking the loop.

_End of Utilities Reference._
