# Claude orchestration patterns

Five small Jupyter notebooks, one per pattern, that call Claude through a [Portkey](https://portkey.ai/docs/integrations/llms/anthropic) gateway. They are made for learning: small cells, a markdown explanation before each step, and logs for every prompt, response, token count and timing.

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

All configuration comes from three variables in `.env` at the repo root. Notebooks find it automatically with `python-dotenv`:

| Variable | Example | Purpose |
|---|---|---|
| `PORTKEY_BASE_URL` | `https://api.portkey.ai` | Gateway root (no `/v1`; the SDK adds `/v1/messages`) |
| `PORTKEY_API_KEY` | `pk-...` | Sent as the `x-portkey-api-key` header |
| `MODEL` | `@anthropic/claude-sonnet-4-5-20250929` | Portkey `@provider-slug/model` name |

Every notebook uses the official `anthropic` SDK with `base_url` pointed at Portkey. If cell 0.4 (smoke test) prints `gateway OK`, your configuration works.

## Notes

- Notebook 05 asks for approval with `input()`. Set `HUMAN_MODE = "approve"` or `"deny"` to use Run All without stopping.
- Notebook 02 runs several calls in parallel and repeats them for timing and voting, so it uses the most tokens.
