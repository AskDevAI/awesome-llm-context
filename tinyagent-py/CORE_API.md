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