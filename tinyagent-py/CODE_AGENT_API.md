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