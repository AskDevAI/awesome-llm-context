```markdown
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