```markdown
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