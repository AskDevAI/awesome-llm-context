```markdown
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