# TinyAgent Hooks Submodule API Documentation

## rich_code_ui_callback (rich_code_ui_callback.py)
Callback to display code execution with Rich.

```python
def rich_code_ui_callback(result: CodeResult) → None
```

## gradio_callback (gradio_callback.py)
Integrate TinyAgent with a Gradio UI.

### Class GradioCallback
Handles UI updates in Gradio interfaces.

#### Methods
- __init__(self, interface) → None
- on_event(self, event: str, data: Any) → None

## rich_ui_callback (rich_ui_callback.py)
Callback to render chat and code with Rich UI.

```python
def rich_ui_callback(message: Message) → None
```

## logging_manager (logging_manager.py)
Manage and route logs for TinyAgent.

### Class LoggingManager
Centralized logger configuration.

#### Methods
- __init__(self, level: str = "INFO") → None
- log(self, message: str, level: Optional[str] = None) → None
- set_level(self, level: str) → None