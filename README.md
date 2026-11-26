# Claude orchestration patterns

Five small Jupyter notebooks, one per pattern, that call Claude through the Anthropic Messages API. They are made for learning: small cells, a markdown explanation before each step, and logs for every prompt, response, token count and timing.

| Notebook | Pattern | Who controls the flow |
|---|---|---|
| [01_prompt_chaining](notebooks/01_prompt_chaining.ipynb) | Fixed sequence of calls with a code gate between steps | Your code |
| [02_parallelization](notebooks/02_parallelization.ipynb) | Sectioning (fan-out/fan-in) and voting | Your code |
| [03_orchestrator_subagents](notebooks/03_orchestrator_subagents.ipynb) | Model plans subtasks; subagents run in isolated contexts | Model plans, code runs |
| [04_evaluator_optimizer](notebooks/04_evaluator_optimizer.ipynb) | Generate, critique, revise loop with a hard stop | Your code (loop), model (judgment) |
| [05_human_in_the_loop](notebooks/05_human_in_the_loop.ipynb) | Tool-using agent with an approval gate enforced in code | Model drives, code enforces |

Read them in order. Each one builds on the previous idea, but every notebook has its own setup cells, so any of them runs on its own.

## Setup

```bash
python -m venv .venv
source .venv/bin/activate        # Windows Git Bash: source .venv/Scripts/activate
pip install -r requirements.txt

cp .env.example .env             # then edit .env with your values
jupyter lab                      # or open the folder in VS Code
```

## Configuration

Configuration lives in `.env` at the repo root, and the notebooks find it automatically with `python-dotenv`. The variable names match the ones used by the Anthropic SDK and Claude Code, so one file can configure both.

| Variable | Required | Purpose |
|---|---|---|
| `ANTHROPIC_BASE_URL` | No (defaults to `https://api.anthropic.com`) | Endpoint root, without `/v1` |
| `ANTHROPIC_API_KEY` or `ANTHROPIC_AUTH_TOKEN` | One of them | API key for direct access, or bearer token for a gateway |
| `ANTHROPIC_MODEL` | Yes | Model ID as your endpoint names it |
| `ANTHROPIC_CUSTOM_HEADERS` | No | Extra headers, one `Name: value` per line |

### Using an LLM gateway

Any gateway that exposes the Anthropic Messages API works without code changes. Set `ANTHROPIC_BASE_URL` to the gateway, put its credential in `ANTHROPIC_AUTH_TOKEN`, and use the model ID format the gateway expects. If the gateway requires extra request headers, for routing, tenancy or provider selection, list them in `ANTHROPIC_CUSTOM_HEADERS`. Notebook 05 uses tool use, so the gateway must pass `tools` and `tool_use` blocks through.

If cell 0.4 (smoke test) prints `gateway OK`, your configuration works. Variables already set in your shell take precedence over `.env`.

## Notes

- Notebook 05 asks for approval with `input()`. Set `HUMAN_MODE = "approve"` or `"deny"` to use Run All without stopping.
- Notebook 02 runs several calls in parallel and repeats them for timing and voting, so it uses the most tokens.
