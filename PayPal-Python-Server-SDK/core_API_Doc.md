# PayPal Python Server SDK – Core Helpers Documentation

## 1. High-Level Architecture

```
┌───────────────────────┐
│  Configuration        │  ←–  Configure environment, auth & HTTP
└──────────┬────────────┘
           │ injects
           ▼
┌───────────────────────┐
│  PayPalServerSdkClient│  ←–  Central façade created by user
└──────────┬────────────┘
           │ owns
           ▼
┌───────────────────────┐
│  Controllers (Orders, │  ←–  Each maps to a PayPal API group
│  Payments, Vault …)   │
└──────────┬────────────┘
           │ returns/accepts
           ▼
┌───────────────────────┐
│  Models               │  ←–  Typed request / response bodies
└───────────────────────┘

The helpers documented here live **outside** the generated controllers/models and power cross-cutting behavior such as HTTP transport, serialization, logging and error handling.

---

## 2. Public Helper APIs

### 2.1 `Configuration`

```python
from paypalserversdk.configuration import Configuration, Environment

config = Configuration(
    environment=Environment.SANDBOX,                 # or PRODUCTION
    client_credentials_auth_credentials=(
        "CLIENT_ID", "CLIENT_SECRET"               # tuple understood by OAuth2 helper
    ),
    timeout=60, max_retries=3, backoff_factor=2,     # basic retry knobs
    logging_configuration=logging_cfg               # see §2.4
)
```

| Property | Purpose |
|----------|---------|
| `environment` | Choose PayPal **Sandbox** or **Production**; controls base-URI (`https://api-m.[sandbox.]paypal.com`). |
| `client_credentials_auth_credentials` | Tuple used by the OAuth2 auth manager to fetch & cache access tokens automatically. |
| HTTP behaviour | `timeout`, `max_retries`, `backoff_factor`, `retry_statuses`, `retry_methods` mirror the standard `requests` retry strategy. |
| `http_call_back` | Optional hook invoked **before** & **after** every request for custom tracing. |
| `logging_configuration` | Structured controls for request / response logging (see §2.4). |

`Configuration.clone_with(**overrides)` returns a shallow-copy with selected fields overridden – useful for per-request tweaks.

### 2.2 `ApiHelper` (`api_helper.APIHelper`)

`ApiHelper` subclasses the APIMatic-core `ApiHelper` and mainly re-exports static utilities:

* **`json_serialize(obj)` / `json_deserialize(str)`** – thin wrappers over `json` that respect model `to_dictionary` helpers.
* **`encode_parameter(value)`** – percent-encodes path/query pieces.
* **`append_url_with_query_parameters(url, params)`** – builds query-strings while skipping `None` values.
* **Enum handling** – when passed an `Enum`, helper returns its **value** rather than the `Enum` instance, keeping payloads clean.

> The class is never instantiated; treat it as a namespace of pure functions.

### 2.3 `PayPalServerSdkClient`

```python
from paypalserversdk.paypal_serversdk_client import PaypalServersdkClient

client = PaypalServersdkClient(config=config)   # or pass low-level kwargs directly

order = client.orders.create_order({...})       # controllers lazily constructed
```

Highlights:

* **Lazily instantiates** controllers using the `LazyProperty` decorator – no cost until used.
* Internally builds a `GlobalConfiguration` object that wires the chosen `Configuration`, shared **auth managers** and **user-agent** metadata.
* Default auth manager is **`OAuth2`** which exchanges `client_id/secret` for bearer tokens and injects the `Authorization` header automatically.
* Exposes managers via `client.oauth_2` allowing manual token refresh or credential swap.

### 2.4 HTTP Layer (`http` package)

| Class | Role |
|-------|------|
| `HttpRequest` | Immutable data-class describing the outbound call – method, URL, headers, query & body. |
| `HttpResponse`| Raw reply – status, headers, body, originating request. |
| `ApiResponse` | Decorates `HttpResponse` with **deserialized body** & typed **errors**; returned by all controllers so callers can access both raw & parsed info. |
| `RequestsClient` *(from dependency)* | Real transport implementation selected by `Configuration.create_http_client()`. |

The request flow is: Controller → build `HttpRequest` → `HttpClient.execute(request)` → receive `HttpResponse` → deserialize → wrap as `ApiResponse`.

### 2.5 Logging Helpers (`logging.configuration`)

The SDK ships a structured, opt-in logging system built on **APIMatic LoggingConfiguration**:

```python
from paypalserversdk.logging.configuration import (
    LoggingConfiguration, RequestLoggingConfiguration, ResponseLoggingConfiguration)

logging_cfg = LoggingConfiguration(
    log_level="DEBUG",
    mask_sensitive_headers=True,
    request_logging_config=RequestLoggingConfiguration(log_body=True, log_headers=True),
    response_logging_config=ResponseLoggingConfiguration(log_body=True)
)

config = Configuration(logging_configuration=logging_cfg, ...)
```

You may plug any custom `logger` implementing `log(level, message, params)` via `LoggingConfiguration(logger=MyLogger())`.

### 2.6 Exceptions (`exceptions` package)

| Exception | Thrown when | Extra fields |
|-----------|------------|--------------|
| `ApiException` | Non-2xx HTTP response not mapped to a more specific error. | `response`, `response_code` |
| `ErrorException` | PayPal error JSON containing `name`, `message`, `details`, `links`. | Parsed fields listed. |
| `OAuthProviderException` | Errors while fetching OAuth2 tokens (e.g., invalid credentials). | `error`, `error_description`, `error_uri` |

All exceptions inherit `ApiException` and therefore carry the low-level `HttpResponse` for diagnostics.

---

## 3. Common Usage Recipes

### 3.1 Instantiate Client

```python
from paypalserversdk import configuration, paypal_serversdk_client

cfg = configuration.Configuration(
    environment=configuration.Environment.SANDBOX,
    client_credentials_auth_credentials=("CLIENT", "SECRET")
)

client = paypal_serversdk_client.PaypalServersdkClient(config=cfg)
```

### 3.2 Add Custom Headers (e.g., idempotency key)

```python
headers = {"PayPal-Request-Id": "my-uuid"}

resp = client.orders.create_order(
    body={...},
    request_headers=headers      # all controllers accept per-call header overrides
)
```

### 3.3 Robust Error Handling

```python
try:
    order = client.orders.get_order("1AB23456CD7890123")
except paypalserversdk.exceptions.ErrorException as e:
    # PayPal-specific error with structured fields
    print(e.name, e.message, e.details)
except paypalserversdk.exceptions.ApiException as e:
    # Fallback for unexpected status codes
    print(e.response.status_code, e.response.text)
```

### 3.4 Enable Wire Logging

```python
import logging, sys
from paypalserversdk.logging.configuration import LoggingConfiguration

py_logger = logging.getLogger("paypal-sdk")
py_logger.setLevel(logging.DEBUG)
py_logger.addHandler(logging.StreamHandler(sys.stdout))

cfg = configuration.Configuration(
    logging_configuration=LoggingConfiguration(logger=py_logger, log_level="DEBUG", mask_sensitive_headers=False),
    ...
)
client = paypal_serversdk_client.PaypalServersdkClient(config=cfg)
```

---

## 4. Notice of API Change (vs `paypal-checkout-serversdk`)

| Area | Old SDK | **New Server SDK** |
|------|---------|--------------------|
| Entry point | `PayPalHttpClient` (manual) | **`PaypalServersdkClient` auto-generated with injected controllers** |
| Config | Manual environment switch & `SandboxEnvironment` class | Unified `Configuration` enum (`Environment.SANDBOX/PRODUCTION`) |
| Serialization | Manual JSON fiddling | Handled by `ApiHelper` + generated models |
| OAuth | Explicit `OAuthTokenCredential` | Transparent `OAuth2` auth manager created from credentials tuple |
| Response type | Raw `HttpResponse` | Rich `ApiResponse` with deserialized body & helper fields |

Migration mainly involves **instantiating the new client** and **removing custom auth / JSON code**.

---

## 5. Known Limitations / Missing Functionality

* **Retry logic is basic** – only status-code / idempotent-method based with exponential back-off. No jitter, no adaptive retries.
* **Circuit breaker** utilities are **not included**; integrate external libraries (e.g., `pybreaker`) around your service layer if required.
* Only **client-credentials OAuth2** flow is supported out-of-the-box.

---

### Feedback & Contributions

Generated portions are maintained by PayPal; feel free to open issues for helper improvements.
