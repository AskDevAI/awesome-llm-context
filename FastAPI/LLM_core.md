```markdown
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