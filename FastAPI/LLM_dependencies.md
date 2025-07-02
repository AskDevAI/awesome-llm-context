```markdown
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
```