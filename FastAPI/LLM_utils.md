```markdown
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
```