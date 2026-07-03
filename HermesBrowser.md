# Explore how the Hermes Agent interact with browser tool #
## How Hermes Agent decides to invoke browser tool #



Let me trace the decision flow — from tool registration through to how the agent picks `browser_navigate` (or any `browser_*` tool) at runtime.
 

Searched for text `browser_navigate|browser_snapshot|browser_click` (`**/toolsets.py`), 5 results

Searched for text `TOOLSETS|_HERMES_CORE_TOOLS` (`**/toolsets.py`), 67 results




 

Read [](file:///home/qzhang8/hermes-agent/toolsets.py#1-1), lines 1 to 100




 

Searched for text `handle_function_call|browser_navigate` (`**/model_tools.py`), 8 results




 

Read [](file:///home/qzhang8/hermes-agent/model_tools.py#876-876), lines 876 to 950




 

Read [](file:///home/qzhang8/hermes-agent/model_tools.py#950-950), lines 950 to 1100




 

Read [](file:///home/qzhang8/hermes-agent/model_tools.py#1100-1100), lines 1100 to 1200



Now let me check how the agent loop in run_agent.py actually invokes tools:
 

Searched for text `tool_calls|handle_function_call` (`**/run_agent.py`), 48 results




 

Read [](file:///home/qzhang8/hermes-agent/run_agent.py#2090-2090), lines 2090 to 2160




 

No matches found. Your search pattern might be excluded completely by either the search.exclude settings or .*ignore files.
If you believe that it should have results, you can check into the .*ignore files and the exclude setting (here are some excluded patterns for reference:[**/node_modules,**/bower_components,**/*.code-search]).
Then if you want to include those files you can call the tool again by setting "includeIgnoredFiles" to true.




 

No matches found. Your search pattern might be excluded completely by either the search.exclude settings or .*ignore files.
If you believe that it should have results, you can check into the .*ignore files and the exclude setting (here are some excluded patterns for reference:[**/node_modules,**/bower_components,**/*.code-search]).
Then if you want to include those files you can call the tool again by setting "includeIgnoredFiles" to true.




 

No matches found. Your search pattern might be excluded completely by either the search.exclude settings or .*ignore files.
If you believe that it should have results, you can check into the .*ignore files and the exclude setting (here are some excluded patterns for reference:[**/node_modules,**/bower_components,**/*.code-search]).
Then if you want to include those files you can call the tool again by setting "includeIgnoredFiles" to true.




 

Searched for regex `tool_calls_data` (`**/run_agent.py`), 4 results




 

Read [](file:///home/qzhang8/hermes-agent/run_agent.py#1600-1600), lines 1600 to 1680




 

Searched for regex `response\.tool_calls|tool_calls:` (`**/run_agent.py`), 5 results




 

No matches found. Your search pattern might be excluded completely by either the search.exclude settings or .*ignore files.
If you believe that it should have results, you can check into the .*ignore files and the exclude setting (here are some excluded patterns for reference:[**/node_modules,**/bower_components,**/*.code-search]).
Then if you want to include those files you can call the tool again by setting "includeIgnoredFiles" to true.




 

No matches found. Your search pattern might be excluded completely by either the search.exclude settings or .*ignore files.
If you believe that it should have results, you can check into the .*ignore files and the exclude setting (here are some excluded patterns for reference:[**/node_modules,**/bower_components,**/*.code-search]).
Then if you want to include those files you can call the tool again by setting "includeIgnoredFiles" to true.




 

Searched for regex `handle_function_call` (`**/run_agent.py`), 1 result




 

Searched for regex `tool_calls|handle_function_call` (`**/agent/conversation_loop.py`), 45 results




 

Read [](file:///home/qzhang8/hermes-agent/agent/conversation_loop.py#760-760), lines 760 to 820




 

No matches found. Your search pattern might be excluded completely by either the search.exclude settings or .*ignore files.
If you believe that it should have results, you can check into the .*ignore files and the exclude setting (here are some excluded patterns for reference:[**/node_modules,**/bower_components,**/*.code-search]).
Then if you want to include those files you can call the tool again by setting "includeIgnoredFiles" to true.




 

Searched for regex `_run_tool|run_tool|execute.*tool` (`**/agent/conversation_loop.py`), 5 results




 

Read [](file:///home/qzhang8/hermes-agent/agent/conversation_loop.py#3900-3900), lines 3900 to 3980




 

No matches found. Your search pattern might be excluded completely by either the search.exclude settings or .*ignore files.
If you believe that it should have results, you can check into the .*ignore files and the exclude setting (here are some excluded patterns for reference:[**/node_modules,**/bower_components,**/*.code-search]).
Then if you want to include those files you can call the tool again by setting "includeIgnoredFiles" to true.




 

Searched for regex `def _execute_tool_calls` (`**/run_agent.py`), 3 results




 

Read [](file:///home/qzhang8/hermes-agent/run_agent.py#5104-5104), lines 5104 to 5200




 

Searched for regex `def execute_tool_calls|handle_function_call` (`**/agent/tool_executor.py`), 10 results




 

Read [](file:///home/qzhang8/hermes-agent/agent/tool_executor.py#240-240), lines 240 to 380




 

Read [](file:///home/qzhang8/hermes-agent/agent/tool_executor.py#1200-1200), lines 1200 to 1310




Now I have enough context to give a complete answer. Here's how Hermes Agent decides to invoke a browser tool:

---

## The Full Invocation Flow

### 1. Tool Discovery & Registration (build time / startup)

```
plugins/browser/<vendor>/provider.py
    ↓ registers via
agent.browser_registry.register_provider(BrowserbaseBrowserProvider)
agent.browser_registry.register_provider(BrowserUseBrowserProvider)
agent.browser_registry.register_provider(FirecrawlBrowserProvider)
    ↓
tools/browser_tool.py imports those classes and exposes them as module-level
names (BrowserbaseProvider, BrowserUseProvider, FirecrawlProvider) for
backward compat with tests that monkeypatch this module directly.
```

The `browser_*` tool functions (`browser_navigate`, `browser_snapshot`, etc.) are **registered** into the global tool registry via registry.py at import time. Each tool's schema (name, description, parameters) is collected and stored.

### 2. Toolset membership (what tools the agent is allowed to use)

In toolsets.py, the `_HERMES_CORE_TOOLS` list includes all `browser_*` tools:
```python
"browser_navigate", "browser_snapshot", "browser_click",
"browser_type", "browser_scroll", "browser_back",
"browser_press", "browser_get_images",
"browser_vision", "browser_console", "browser_cdp", "browser_dialog",
```

Every platform's toolset inherits this list. A session can be further scoped via `enabled_toolsets` / `disabled_toolsets` — the Tool Search bridge gates which tools are visible and callable in restricted sessions.

### 3. The model decides to call a browser tool

The LLM (Anthropic, OpenAI, etc.) receives a **system prompt** that lists all available tools in schema form. When it decides it needs to navigate to a URL, it emits a `browser_navigate` tool call in its response.

### 4. The API response is received by the agent loop

In run_agent.py / conversation_loop.py, the API response is processed. `assistant_message.tool_calls` is extracted:

```python
tool_calls = getattr(assistant_message, "tool_calls", None) or []
# ↓ passed to:
agent._execute_tool_calls(assistant_message, messages, effective_task_id, api_call_count)
```

### 5. Tool execution dispatch

tool_executor.py handles the actual dispatch. Two paths exist:

- **Sequential** (`_execute_tool_calls_sequential`): tools that may have side effects or share state run one at a time
- **Concurrent** (`_execute_tool_calls_concurrent`): read-only tools can run in parallel

For each `tool_call` in the batch:

```python
# 1. Parse arguments from JSON
function_args = json.loads(tool_call.function.arguments)

# 2. Tool Search bridge unwrap (if model used the bridge tool)
if function_name == _ts.TOOL_CALL_NAME:
    function_name = _underlying  # peel to real tool name

# 3. Scope gate check — is this tool allowed in this session?
if function_name not in _tool_search_scoped_names(agent):
    block_result = {"error": "not available in this session"}

# 4. Pre-tool-call plugin hooks (block directive)
block_message = get_pre_tool_call_block_message(...)
if block_message:
    block_result = {"error": block_message}

# 5. Guardrail check
guardrail_decision = agent._tool_guardrails.before_call(function_name, function_args)
if not guardrail_decision.allows_execution:
    block_result = agent._guardrail_block_result(...)

# 6. Actual dispatch via handle_function_call
function_result = _ra().handle_function_call(
    function_name,        # e.g. "browser_navigate"
    function_args,        # {"url": "https://...", "task_id": "..."}
    effective_task_id,
    tool_call_id=tool_call.id,
    ...
)
```

### 6. `handle_function_call` in model_tools.py

This is the central dispatcher that:

1. **Coerces args** to schema-declared types (`"42"` → `42`)
2. **Tool Search bridge dispatch** — handles `tool_search`, `tool_describe`, `tool_call` inline
3. **Middleware** — applies `tool_request_middleware` (transforms args before dispatch)
4. **Plugin block hooks** — `pre_tool_call` can block execution
5. **ACP/Zed edit approval** — gates `write_file` / `patch`
6. **Registry dispatch** — looks up the tool in the global registry and calls it

```python
# model_tools.py ~line 876
def handle_function_call(function_name, function_args, ...):
    # Coerce string args to declared types
    function_args = coerce_tool_args(function_name, function_args)
    
    # Tool Search bridge...
    
    # Pre-tool-call plugin hooks (block)
    block_message = get_pre_tool_call_block_message(...)
    if block_message:
        return json.dumps({"error": block_message}, ...)
    
    # ACP edit approval
    edit_block_message = maybe_require_edit_approval(function_name, function_args)
    if edit_block_message:
        return edit_block_message
    
    # Dispatch via registry
    result = registry.dispatch(
        function_name, function_args,
        task_id=task_id, session_id=session_id, user_task=user_task,
    )
    
    # Post-tool-call plugin hooks
    # transform_tool_result hooks...
    return result
```

### 7. The registry resolves to the actual function

registry.py holds the map of tool name → handler. For `browser_navigate`:

```python
registry.dispatch("browser_navigate", {"url": "https://...", "task_id": "..."}, ...)
    ↓
tools/browser_tool.browser_navigate(url="https://...", task_id="...")
```

### 8. Inside `browser_navigate` — the actual browser tool

browser_tool.py `browser_navigate` function (~line 2291):

1. **URL safety checks** — `is_safe_url`, website policy check
2. **Session resolution** — `_get_session_info(task_id)` (creates a local or CDP session if needed)
3. **Backend selection** — `_get_cloud_provider()` decides local vs Browser Use vs Browserbase:
   - Explicit `"local"` in config → local Chromium only
   - `browser.cloud_provider` set → uses that specific cloud provider
   - Otherwise → auto-detect: Browser Use first, then Browserbase
4. **Command construction** — builds the `agent-browser` CLI command with the right CDP URL, session ID, and browser flags
5. **Subprocess execution** — `_run_browser_command()` runs the CLI, handles Lightpanda→Chrome fallback
6. **Result extraction** — parses stdout, extracts screenshot path, returns text snapshot

### 9. Result flows back

```
browser_navigate returns JSON string
    ↓
handle_function_call passes through transform_tool_result hooks
    ↓
tool_executor appends tool result message to messages list
    ↓
agent loop continues — next API call or final response to user
```

---

## Summary Diagram

```
LLM decides: "I'll call browser_navigate"
        ↓
API response: { tool_calls: [{ function: { name: "browser_navigate", arguments: "{...}" } }] }
        ↓
run_agent._execute_tool_calls()
        ↓
tool_executor.execute_tool_calls_sequential / _concurrent
  ├─ Parse args (JSON)
  ├─ Tool Search bridge unwrap
  ├─ Scope gate check (session toolset)
  ├─ Plugin pre-tool-call hooks
  ├─ Guardrail check
  └─ handle_function_call("browser_navigate", {...})
          ↓
      model_tools.coerce_tool_args()      # type coercion
      model_tools.handle_function_call()    # central dispatcher
          ├─ tool_request middleware
          ├─ plugin block check
          ├─ ACP/Zed edit approval
          └─ registry.dispatch()
                  ↓
              tools/registry.py
                  ↓
              tools/browser_tool.browser_navigate()
                  ├─ URL safety checks
                  ├─ _get_cloud_provider()  → Browser Use / Browserbase / local
                  ├─ _get_session_info()   → CDP session
                  ├─ _run_browser_command() → agent-browser CLI
                  └─ returns text snapshot
          ↓
      transform_tool_result hooks
          ↓
      tool result appended to messages
          ↓
      agent loop continues
```

The key design properties:
- **Layered gating** at every step (scope → plugin hooks → guardrails → edit approval)
- **Cloud vs local resolved lazily** inside the tool, not at dispatch time
- **Session isolation** per `task_id` with CDP sessions and cleanup threads
- **Plugin-extensible** — new browser providers register themselves and participate in resolution automatically
-
- ## browser_registry.py ##
Updated todo list

The file browser_registry.py is a small, focused module that acts as the **central registry for cloud browser providers**. Here's a breakdown:

### Purpose
It keeps a lookup table of available cloud browser backends (Browser Use, Browserbase, Firecrawl, third-party plugins) and decides which one a `browser_*` tool call should route to.

### State
```python
_providers: Dict[str, BrowserProvider] = {}   # name -> provider
_lock = threading.Lock()                       # guards all access
```
A module-level dict guarded by a lock, so registration and lookup are thread-safe.

### Public API

| Function | What it does |
|----------|--------------|
| `register_provider(provider)` | Adds a provider keyed by `provider.name`. Validates type/name, and re-registering the same name overwrites (handy for hot-reload/tests). Called by plugins at import time via `PluginContext.register_browser_provider`. |
| `list_providers()` | Returns all providers sorted by name. |
| `get_provider(name)` | Fetches one provider by name, or `None`. |
| `_reset_for_tests()` | Clears the registry (test-only). |

### The heart: `_resolve(configured)`
Decides the **active** provider using this precedence:

1. **`configured == "local"`** → return `None`, disabling cloud mode.
2. **Explicit config set** (`browser.cloud_provider`) → return that provider *even if `is_available()` is False*. This is deliberate — the dispatcher then surfaces a precise "X_API_KEY is not set" error rather than silently falling back.
3. **Legacy auto-detect** → walk `_LEGACY_PREFERENCE = ("browser-use", "browserbase")` and return the first one whose `is_available()` is True.
4. Otherwise → `None` (dispatcher falls back to local browser mode).

### Key design intentions (documented in the module)
- **Firecrawl is deliberately excluded from auto-detect.** It shares `FIRECRAWL_API_KEY` with the web-extract plugin, so users who set that key for web extraction must NOT get silently routed to a paid cloud browser. Firecrawl only activates via explicit config.
- **No "single-eligible shortcut"** (unlike `web_search_registry._resolve`) — only the two legacy names are auto-eligible; everything else, including third-party plugins, requires explicit config.
- **`_is_available_safe()`** wraps each provider's `is_available()` in a try/except so a buggy provider can't crash the whole resolution.

### Relationships
- Implements against the `BrowserProvider` ABC in browser_provider.py.
- Consumed by `tools.browser_tool._get_cloud_provider()`, whose historic behavior this registry was carefully written to preserve.

Updated todo list

Read [](file:///home/qzhang8/hermes-agent/agent/browser_provider.py)

The file browser_provider.py defines the **abstract base class (ABC)** that every cloud browser backend must implement. It's the interface contract that the registry (from your last file) stores and resolves.

### Purpose
It defines the pluggable-backend interface for cloud browser providers (Browserbase, Browser Use, Firecrawl, third-party vendors). Providers register instances that then service every cloud-mode `browser_*` tool call.

### The `BrowserProvider` ABC

**Required abstract members** (subclasses must implement all):

| Member | Type | Responsibility |
|--------|------|----------------|
| `name` | property | Stable short ID used in the `browser.cloud_provider` config key (e.g. `browserbase`, `browser-use`, `firecrawl`). Lowercase, hyphens allowed. |
| `is_available()` | method | Cheap check (env var present, dep importable) — **must not** make network calls; runs at registration time and on every `hermes tools` paint. |
| `create_session(task_id)` | method | Creates a cloud session, returns metadata dict. May raise `ValueError`/`RuntimeError`. |
| `close_session(session_id)` | method | Terminates a session; returns bool, should not raise. |
| `emergency_cleanup(session_id)` | method | Best-effort teardown from atexit/signal handlers; must not raise. |

**Optional overridable members** (have defaults):

| Member | Default | Purpose |
|--------|---------|---------|
| `display_name` | returns `name` | Human-readable label in `hermes tools`. |
| `get_setup_schema()` | minimal entry from `display_name` | Provider metadata (badge, API-key prompts, `post_setup` hook) for the `hermes tools` picker. |

### The session metadata contract
`create_session()` must return a dict with at least:
```python
{
    "session_name": str,   # unique name for agent-browser --session
    "bb_session_id": str,  # provider session ID (for close/cleanup)
    "cdp_url": str,        # CDP websocket URL
    "features": dict,      # feature flags enabled
    # optional: "external_call_id" for managed-gateway billing
}
```
Note the `bb_session_id` key is a **legacy name** (originally Browserbase-specific) kept verbatim so `tools.browser_tool` doesn't need translation — it now holds *any* provider's session ID.

### Backward-compat shims
The pre-refactor ABC (`CloudBrowserProvider`) used different method names. To avoid churning ~6 callsites in `tools.browser_tool` and breaking downstream subclasses, two thin aliases are provided:
- `is_configured()` → delegates to `is_available()`
- `provider_name()` → delegates to `display_name`

### Design context (from the module docstring)
- Mirrors `agent.web_search_provider.WebSearchProvider` exactly — same shape, registration flow, and picker integration.
- Replaced the deleted in-tree `tools.browser_providers.base.CloudBrowserProvider` (PR #25214), **preserving the lifecycle contract bit-for-bit** so the dispatcher stays a pure registry lookup with no per-provider conditionals.

### How it fits together
```mermaid
graph LR
    A[Plugin] -->|register_browser_provider| B[browser_registry]
    C[BrowserProvider ABC] -->|subclassed by| A
    B -->|_resolve selects active| D[tools.browser_tool]
    D -->|create/close/cleanup| C
```

In short: this is the **contract**; browser_registry.py is the **directory**; `tools.browser_tool` is the **consumer**.

Updated todo list

Searched for files matching `**/tools/browser_tool.py`, 1 match

Read [](file:///home/qzhang8/hermes-agent/tools/browser_tool.py#1-1), lines 1 to 120

This is a large file. Let me get an overview of its structure so I can show it meaningfully. 

Ran terminal command:  wc -l tools/browser_tool.py && echo "---FUNCTIONS/CLASSES---" && grep -nE "^(def|class|async def) |^_[A-Z_]+ = |^# ---" tools/browser_tool.py | head -120

Read [](file:///home/qzhang8/hermes-agent/tools/browser_tool.py#446-446), lines 446 to 597

Here's an explanation of browser_tool.py — at ~3,900 lines, it's the **consumer** that ties the provider ABC and registry into the actual `browser_*` agent tools.

### Purpose
It implements the browser-automation tools the agent calls (`browser_navigate`, `browser_click`, etc.), driving the `agent-browser` CLI. It supports three backends transparently:
- **Local Chromium** (default, zero-cost, headless — works on Linux servers)
- **Browser Use** (cloud, default for Nous subscribers)
- **Browserbase** (cloud, direct credentials)
- Plus optional **Camofox** and **Lightpanda** engines

### Structure by section

**1. Imports & backward-compat shims (lines 1–115)**
Notably it re-exports legacy names for callers that still import them from this module after the PR #25214 refactor moved providers into `plugins/browser/<vendor>/`:
```python
from agent.browser_provider import BrowserProvider as CloudBrowserProvider   # legacy alias
from agent.browser_registry import get_provider as _registry_get_browser_provider
from plugins.browser.browserbase.provider import BrowserbaseBrowserProvider as BrowserbaseProvider
# ... browser_use, firecrawl
```

**2. PATH / environment discovery (119–200)**
Helpers to locate `agent-browser`, `node`, `npx` in minimal-PATH environments (systemd, Termux, Homebrew).

**3. Config resolvers (200–348)**
Timeouts, vision/extraction models, CDP overrides, dialog policy.

**4. Provider resolution (446–597)** — the bridge to your last two files:
- `_get_cloud_provider()` reads `browser.cloud_provider` from config **once and caches it** for the process lifetime.
  - Explicit `"local"` → disables cloud, returns `None`
  - Explicit config → routes through `agent.browser_registry.get_provider()` (so third-party plugins participate); warns if the name is unregistered
  - Otherwise → auto-detect: Browser Use → Browserbase (the `_LEGACY_PREFERENCE` walk)
- `_ensure_browser_plugins_loaded()` idempotently triggers plugin discovery for code paths that skipped `model_tools`.
- `_is_legacy_provider_registry_overridden()` detects test monkeypatches of `_PROVIDER_REGISTRY` and honors them.

**5. Session lifecycle & cleanup (1063–1650)**
Per-task session keys, PID ownership files, orphan reaping, a background cleanup thread, atexit/emergency teardown, local vs CDP session creation.

**6. Command execution (1753–2290)**
`_find_agent_browser()`, `_run_browser_command()` (the core subprocess runner with Lightpanda→Chrome fallback logic), plus content extraction/truncation helpers with LLM summarization.

**7. Public agent tools (2291–3345)** — the actual model-facing surface:

| Tool | Action |
|------|--------|
| `browser_navigate` | Go to a URL |
| `browser_snapshot` | Accessibility-tree (ariaSnapshot) page text |
| `browser_click` / `browser_type` | Interact via `@e1`-style refs |
| `browser_scroll` / `browser_back` / `browser_press` | Navigation/input |
| `browser_console` | Read/eval JS console |
| `browser_get_images` | Extract page images |
| `browser_vision` | LLM vision on a screenshot |

**8. Requirements & cleanup (3347–3873)**
`check_browser_requirements()`, Chromium install detection, Docker detection, screenshot/recording cleanup, and the schema map at the end (`_BROWSER_SCHEMA_MAP`).

### How it fits together
```mermaid
graph LR
    A[BrowserProvider ABC] -->|implemented in| B[plugins/browser/*]
    B -->|register| C[browser_registry]
    C -->|_get_cloud_provider| D[browser_tool.py]
    D -->|drives| E[agent-browser CLI]
    D -->|exposes browser_* tools| F[Agent]
```

### Notable design points
- **Security-first**: fail-closed URL safety (`_is_safe_url` defaults to blocking if the safety module is unavailable), website-access policy checks, private-URL auto-local routing.
- **Legacy-name preservation**: `bb_session_id`, `CloudBrowserProvider`, `is_configured()` all kept so the dispatcher stays a pure registry lookup with no per-provider branching.
- **Robust cleanup**: multiple layers (thread worker, atexit, emergency, orphan reaper) so cloud sessions and local Chromium processes don't leak.

Want me to open any specific section (e.g. `_run_browser_command`, the session cleanup, or one of the tool functions) in full?
In short: **it's pure selection logic** — registering providers and picking the right one — with no capability routing (every browser provider implements the full lifecycle, so there's no search/extract/crawl split like the web subsystem has).
