```markdown
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
```