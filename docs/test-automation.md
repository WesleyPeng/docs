# Test Automation Framework — Agentic-TAF

The platform's test automation framework is **agentic-taf** — the only
public repository in the platform. It evolved from
[uiXautomation (PyXTaf)](https://pypi.org/project/PyXTaf/), modernized for
Python 3.12+, and extended with plugins for the agentic ecosystem.

[GitHub: WesleyPeng/agentic-taf ↗](https://github.com/WesleyPeng/agentic-taf){ .md-button .md-button--primary }
[Architecture diagram (SVG) ↗](https://raw.githubusercontent.com/WesleyPeng/agentic-taf/main/architecture-diagram.svg){ .md-button }

## Four-layer plugin architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                       Test Suites (pytest / behave)                     │
│  Unit tests (ut/) │ BDD/ATDD examples (bpt/) │ Platform (agentic/)      │
├─────────────────────────────────────────────────────────────────────────┤
│                            Modeling Layer                               │
│  RESTClient │ Browser │ CLIRunner │ WSClient │ LLMJudge │ ChaosRunner   │
├─────────────────────────────────────────────────────────────────────────┤
│                             Plugin Layer                                │
│  Selenium │ Playwright │ Requests │ httpx │ WS │ Paramiko │ LLM │Chaos  │
├─────────────────────────────────────────────────────────────────────────┤
│                          Foundation Layer                               │
│        ServiceLocator  │  Configuration (YAML)  │  Utils                │
└─────────────────────────────────────────────────────────────────────────┘
```

The bottom three layers form the **framework**; the top layer is the
**platform-specific test suite** that exercises this very Agentic QA
Platform (eat-your-own-dogfood).

## Plugins

| Interface | Implementations | Purpose |
|-----------|----------------|---------|
| `WebPlugin` | `SeleniumPlugin` (default), `PlaywrightPlugin` | Browser automation |
| `RESTPlugin` | `RequestsPlugin` (default), `HttpxRESTPlugin` | REST API |
| `WSPlugin` | `WebSocketPlugin` | WebSocket streaming |
| `CLIPlugin` | `ParamikoPlugin` | SSH / CLI |
| `MobilePlugin` | `AppiumPlugin` *(stub — interface defined, concrete plugin planned)* | Mobile automation |
| `LLMPlugin` | `LLMJudgePlugin` | LLM response quality scoring |
| `ChaosPlugin` | `K8sChaosPlugin` | Kubernetes-native fault injection |

Plugin discovery is via the **ServiceLocator** pattern — concrete
implementations are registered at runtime based on `config.yml` plus
environment overrides (`TAF_PLUGIN_<NAME>_<KEY>`). Test code never
imports a concrete plugin directly.

## Test counts (after T.10)

| Suite | Count |
|-------|------:|
| Unit tests (`ut/`) | **277** |
| API E2E tests | 21 |
| Security E2E tests | 8 |
| UI E2E tests (Playwright) | 10 |
| AI E2E tests (`test_ai.py` 11 + `test_e2e_quality.py` 5) | **16** |
| Chaos experiments | 4 |
| Load & performance tests | 4 |
| **Total E2E (pytest)** | **63** |
| BDD scenarios (behave, 4 feature files) | **10** |

## LLM-as-Judge

The framework's LLM-judge implements a 4-layer stack:

```
test code
  → LLMJudge.assert_quality(prompt, response, context, thresholds)
    → LLMClient.evaluate()  →  iterates rubric dimensions
      → LLMClient.score()   →  builds judge prompt per dimension
        → ChatOpenAI / ChatAnthropic .invoke()
          → parses JSON {score, reason}  →  float 1.0-5.0
    → computes overall average
    → checks thresholds
    → raises AssertionError if failed
```

### 4 rubrics out of the box (T.10.1)

```python
from taf.foundation.api.llm import Client

Client.DEFAULT_RUBRIC          # accuracy, completeness, relevance, clarity, safety
Client.GROUND_TRUTH_RUBRIC     # accuracy/completeness against API ground truth
Client.DEGRADED_MODE_RUBRIC    # safety/clarity/relevance under stress
Client.ADVERSARIAL_RUBRIC      # safety/accuracy against injection / extraction
```

Per-call rubric override is supported on `assert_quality(..., rubric=...)`.

### Provider registry (T.10.1, OCP refactor)

```python
from taf.foundation.plugins.llm.judge.llmclient import register_provider

def _build_ollama_native(model, base_url=None, api_key=None, **kwargs):
    from langchain_community.chat_models import ChatOllama
    return ChatOllama(model=model, base_url=base_url, **kwargs)

register_provider('ollama-native', _build_ollama_native)
```

Adding a new LLM provider does **not** require modifying
`_create_chat_model()` — register a builder, that's it.

### Strategy 1 — Ground-truth anchored (highest value)

```python
def test_health_status_accuracy(api_client, llm_judge):
    health = api_client.get('/health').json()
    result = api_client.post('/api/v1/chat',
        json={'message': 'what is the platform health status?'}).json()

    llm_judge.assert_quality(
        prompt='what is the platform health status?',
        response=result['response'],
        context=health,
        rubric=Client.GROUND_TRUTH_RUBRIC,
        dimension_thresholds={'accuracy': 4.0},
    )
```

The judge LLM evaluates whether the agent's natural-language reply matches
the deterministic API output — turning fuzzy quality assessment into
concrete fact-checking.

### Shared fixtures (T.10.2)

The `llm_judge` fixture lives in `suites/agentic/conftest.py` (shared)
with two opt-in modes:

- **`llm_judge`** — required, skips the test if langchain unavailable
  (AI suite default)
- **`llm_judge_optional`** — returns `None` if unavailable; chaos /
  security / BDD suites use `if llm_judge_optional: ...` to opt in
- **`chat_and_judge`** — composite fixture that sends a chat message
  and optionally judges in one call

## CI/CD

GitHub Actions workflow runs lint → unit tests → contract validation →
build → docker on every PR. On a tagged release (`v*`), it pushes a
multi-arch image to `ghcr.io/wesleypeng/agentic-taf`.

The Jenkins pipeline has 11 stages (Install → Lint → Unit Tests → Build
→ API → Security → UI → BDD → AI → Chaos → Load → Report). E2E stages
are gated by `TAF_RUN_E2E=true`.

## Container

```
docker pull ghcr.io/wesleypeng/agentic-taf:v1.0.0
docker run --rm -e TAF_PLUGIN_LLM_ENABLED=true \
  ghcr.io/wesleypeng/agentic-taf:v1.0.0 \
  src/test/python/suites/agentic/api/ -v
```

Base image: `python:3.12-slim` + Playwright Chromium + all optional
dependencies. Entrypoint is `pytest`.

## License

LGPL-3.0. Copyright &copy; 2017-2026 Wesley Peng.

[GitHub repository ↗](https://github.com/WesleyPeng/agentic-taf){ .md-button .md-button--primary }
[Architecture document ↗](https://github.com/WesleyPeng/agentic-taf/blob/main/docs/architecture.md){ .md-button }
[Implementation plan ↗](https://github.com/WesleyPeng/agentic-taf/blob/main/docs/implementation-plan.md){ .md-button }
