```markdown
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