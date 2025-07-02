```markdown
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