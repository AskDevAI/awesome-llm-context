# CODE_AGENT_API.md

# TinyAgent Code Agent Submodule API Documentation

## example_tools (tools/example_tools.py)
Utility functions for sample code tools.

**Functions**
- `echo_tool(text: str) → str`: Returns the same text.

## TinyCodeAgent (tiny_code_agent.py)
High-level agent specialized for code tasks.

### Class TinyCodeAgent

#### Methods
- __init__(model: str, workspace: str, **kwargs) → None
- async run_code(user_prompt: str, code: str) → str: Execute code with the agent.

## example (example.py)
Demonstrates usage of TinyAgent and TinyCodeAgent.

```python
from tinyagent.code_agent import TinyCodeAgent
agent = TinyCodeAgent(model="gpt-4-code", workspace="./code_workspace")
result = await agent.run_code("Sort list in Python", "my_list = [3,1,2]")
```

## utils (utils.py)
Helper functions for code processing.

**Functions**
- `format_code(code: str) → str`: Normalize code indentation.
- `extract_imports(code: str) → List[str]`: Return import statements.

## helper (helper.py)
General utilities for the code agent.

**Functions**
- `load_file(path: str) → str`: Read file contents.
- `save_file(path: str, content: str) → None`: Write content to file.

## safety (safety.py)
Sandbox safety checks.

**Functions**
- `is_safe_code(code: str) → bool`: Static analysis for unsafe patterns.

## modal_sandbox (modal_sandbox.py)
Run code inside Modal sandbox.

### Class ModalSandbox

#### Methods
- __init__(token: str) → None
- async run(fn: Callable, *args, **kwargs) → Any: Invoke function in sandbox.
# CORE_API.md

# TinyAgent Core Package API Documentation

## tool (decorator)
Convert a Python function or class into a tool for TinyAgent.

**Signature**
```python
tool(name: Optional[str] = None,
     description: Optional[str] = None,
     schema: Optional[Dict[str, Any]] = None) → Callable
```

## Class TinyAgent
A minimal implementation of an agent powered by MCP and LiteLLM, with session/state persistence.

### Methods
- __init__(model: str = "gpt-4.1-mini", api_key: Optional[str] = None,
           system_prompt: Optional[str] = None, temperature: float = 0.0,
           logger: Optional[logging.Logger] = None,
           model_kwargs: Optional[Dict[str, Any]] = {},
           *, user_id: Optional[str] = None,
           session_id: Optional[str] = None,
           metadata: Optional[Dict[str, Any]] = None) → None
- _generate_session_id() → str
- count_tokens(text: str) → int
- async save_agent() → None
- async _on_llm_end(event_name: str, agent: "TinyAgent", **kwargs) → None
- to_dict() → Dict[str, Any]
- from_dict(data: Dict[str, Any]) → "TinyAgent"
- add_callback(callback: callable) → None
- async _run_callbacks(event_name: str, **kwargs) → None
- async connect_to_server(command: str, args: List[str], **kwargs) → None
- add_tool(tool_func_or_class: Any) → None
- add_tools(tools: List[Any]) → None
- async _execute_custom_tool(tool_name: str, tool_args: Dict[str, Any]) → str
- async run(user_input: str, max_turns: int = 10) → str
- async close() → None
- clear_conversation() → None
- as_tool(name: Optional[str] = None, description: Optional[str] = None) → Dict[str, Any]
- async init_async() → "TinyAgent"
- async create(**kwargs) → "TinyAgent"
- _apply_session_data(data: Dict[str, Any]) → None
- async run_example() → None

## Class MCPClient
Simplified client for interacting with an MCP server over stdio.

### Methods
- add_callback(callback: callable) → None
- async _run_callbacks(event_name: str, **kwargs) → None
- async connect(command: str, args: List[str]) → None
- async list_tools() → None
- async call_tool(name: str, arguments: dict) → Any
- async close() → None

## Memory Strategies
Abstract strategies to decide which messages to keep:

- class MemoryStrategy: base class with should_keep_message(...) and get_priority_score(...)
- class ConservativeStrategy: keeps more messages
- class AggressiveStrategy: removes more messages
- class BalancedStrategy: moderate approach

## Class MemoryManager
Advanced memory management for TinyAgent.

### Methods
- __init__(max_tokens: int = 8000, target_tokens: int = 6000,
           strategy: MemoryStrategy = None,
           enable_summarization: bool = True,
           logger: Optional[logging.Logger] = None,
           num_recent_pairs_high_importance: Optional[int] = None,
           num_initial_pairs_critical: Optional[int] = None) → None
# HOOKS_API.md

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
# LLM.md

# tinyagent API Documentation
This document provides a concise, up-to-date reference for the public APIs of the tinyagent library.

## Table of Contents
1. [Core Package API](#core-package-api)
2. [Storage Submodule API](#storage-submodule-api)
3. [Hooks Submodule API](#hooks-submodule-api)
4. [Code Agent Submodule API](#code-agent-submodule-api)

## Core Package API
<!-- Content from CORE_API.md -->

## tool (decorator)
Convert a Python function or class into a tool for TinyAgent.

**Signature**
```python
tool(name: Optional[str] = None,
     description: Optional[str] = None,
     schema: Optional[Dict[str, Any]] = None) → Callable
```

## Class TinyAgent
A minimal implementation of an agent powered by MCP and LiteLLM, with session/state persistence.

### Methods
- __init__(model: str = "gpt-4.1-mini", api_key: Optional[str] = None,
           system_prompt: Optional[str] = None, temperature: float = 0.0,
           logger: Optional[logging.Logger] = None,
           model_kwargs: Optional[Dict[str, Any]] = {},
           *, user_id: Optional[str] = None,
           session_id: Optional[str] = None,
           metadata: Optional[Dict[str, Any]] = None) → None
- _generate_session_id() → str
- count_tokens(text: str) → int
- async save_agent() → None
- async _on_llm_end(event_name: str, agent: "TinyAgent", **kwargs) → None
- to_dict() → Dict[str, Any]
- from_dict(data: Dict[str, Any]) → "TinyAgent"
- add_callback(callback: callable) → None
- async _run_callbacks(event_name: str, **kwargs) → None
- async connect_to_server(command: str, args: List[str], **kwargs) → None
- add_tool(tool_func_or_class: Any) → None
- add_tools(tools: List[Any]) → None
- async _execute_custom_tool(tool_name: str, tool_args: Dict[str, Any]) → str
- async run(user_input: str, max_turns: int = 10) → str
- async close() → None
- clear_conversation() → None
- as_tool(name: Optional[str] = None, description: Optional[str] = None) → Dict[str, Any]
- async init_async() → "TinyAgent"
- async create(**kwargs) → "TinyAgent"
- _apply_session_data(data: Dict[str, Any]) → None
- async run_example() → None

## Storage Submodule API
<!-- Content from STORAGE_API.md -->

## Base Storage Interface (base.py)
Defines abstract methods for storage backends.

### Class BaseStorage
Abstract storage interface.

#### Methods
- async save(key: str, data: Any) → None: Persist data by key.
- async load(key: str) → Any: Retrieve data by key.
- async delete(key: str) → None: Remove stored data.
- async list_keys() → List[str]: List all keys.

## RedisStorage (redis_storage.py)
Persistent storage using Redis.

**Constructor**
```python
RedisStorage(redis_url: str)
```

#### Methods
- Inherits BaseStorage methods.

## PostgresStorage (postgres_storage.py)
Persistent storage using PostgreSQL.

**Constructor**
```python
PostgresStorage(dsn: str)
```

#### Methods
- Inherits BaseStorage methods.

## JsonFileStorage (json_file_storage.py)
File-based storage using JSON.

**Constructor**
```python
JsonFileStorage(path: str)
```

#### Methods
- save(key: str, data: Any) → None
- load(key: str) → Any
- delete(key: str) → None
- list_keys() → List[str]

## SqliteStorage (sqlite_storage.py)
File-based storage using SQLite.

**Constructor**
```python
SqliteStorage(db_path: str)
```

#### Methods
- save(key: str, data: Any) → None
- load(key: str) → Any
- delete(key: str) → None
- list_keys() → List[str]

## Hooks Submodule API
<!-- Content from HOOKS_API.md -->

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

## Code Agent Submodule API
<!-- Content from CODE_AGENT_API.md -->

## example_tools (tools/example_tools.py)
Utility functions for sample code tools.

**Functions**
- `echo_tool(text: str) → str`: Returns the same text.

## TinyCodeAgent (tiny_code_agent.py)
High-level agent specialized for code tasks.

### Class TinyCodeAgent

#### Methods
- __init__(model: str, workspace: str, **kwargs) → None
- async run_code(user_prompt: str, code: str) → str: Execute code with the agent.

## example (example.py)
Demonstrates usage of TinyAgent and TinyCodeAgent.

```python
from tinyagent.code_agent import TinyCodeAgent
agent = TinyCodeAgent(model="gpt-4-code", workspace="./code_workspace")
result = await agent.run_code("Sort list in Python", "my_list = [3,1,2]")
```

## utils (utils.py)
Helper functions for code processing.

**Functions**
- `format_code(code: str) → str`: Normalize code indentation.
- `extract_imports(code: str) → List[str]`: Return import statements.

## helper (helper.py)
General utilities for the code agent.

**Functions**
- `load_file(path: str) → str`: Read file contents.
- `save_file(path: str, content: str) → None`: Write content to file.

## safety (safety.py)
Sandbox safety checks.

**Functions**
- `is_safe_code(code: str) → bool`: Static analysis for unsafe patterns.

## modal_sandbox (modal_sandbox.py)
Run code inside Modal sandbox.

### Class ModalSandbox

#### Methods
- __init__(token: str) → None
- async run(fn: Callable, *args, **kwargs) → Any: Invoke function in sandbox.
# STORAGE_API.md

# TinyAgent Storage Submodule API Documentation

## Base Storage Interface (base.py)
Defines abstract methods for storage backends.

### Class BaseStorage
Abstract storage interface.

#### Methods
- async save(key: str, data: Any) → None: Persist data by key.
- async load(key: str) → Any: Retrieve data by key.
- async delete(key: str) → None: Remove stored data.
- async list_keys() → List[str]: List all keys.

## RedisStorage (redis_storage.py)
Persistent storage using Redis.

**Constructor**
```python
RedisStorage(redis_url: str)
```

#### Methods
- Inherits BaseStorage methods.

## PostgresStorage (postgres_storage.py)
Persistent storage using PostgreSQL.

**Constructor**
```python
PostgresStorage(dsn: str)
```

#### Methods
- Inherits BaseStorage methods.

## JsonFileStorage (json_file_storage.py)
File-based storage using JSON.

**Constructor**
```python
JsonFileStorage(path: str)
```

#### Methods
- save(key: str, data: Any) → None
- load(key: str) → Any
- delete(key: str) → None
- list_keys() → List[str]

## SqliteStorage (sqlite_storage.py)
File-based storage using SQLite.

**Constructor**
```python
SqliteStorage(db_path: str)
```

#### Methods
- save(key: str, data: Any) → None
- load(key: str) → Any
- delete(key: str) → None
- list_keys() → List[str]
