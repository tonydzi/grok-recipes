# grok-recipes

Four ready-to-read Python notebooks that show how to put safety checks around an AI model (Grok), so it cannot quietly do the wrong thing.

**Who it's for:** developers building apps on Grok (or any OpenAI-style API) who want guardrails before they spend money on API calls.
**What you get:** four notebooks (tool-call guardrails, cited answers, evals, prompt-injection defense) that run offline with no API key.

## Quick start
1. Install Python 3.12 or newer.
2. Run `git clone https://github.com/tonydzi/grok-recipes && cd grok-recipes`, then `pip install -r requirements.txt`.
3. Run `pytest --nbmake examples/`.
4. Open any `examples/*/guide.ipynb` in Jupyter to read it.

**Done when:** pytest prints a last line like `4 passed in 19.07s` (the time varies; the first run can take a minute while Jupyter starts) and no API key is set. If `XAI_API_KEY` is set in your shell, unset it first, or the notebooks may call the paid API.

> Want an AI agent to do it for you? Paste this to Claude Code / Codex / Cursor:
> "Read the 'For AI agents' section of https://github.com/tonydzi/grok-recipes and set it up for me."

## For AI agents

### Preconditions
- Python 3.12+ (the CI uses 3.12) and pip.
- Packages come from `requirements.txt`: openai, pydantic, python-dotenv, jupyter, nbmake, pytest.
- No account is needed for the offline run. A live run needs an xAI API key, which only the human can provide.
- Ask the human: offline only, or also live Grok calls? Install into a virtual environment, not the global Python.

### Install
```bash
git clone https://github.com/tonydzi/grok-recipes
cd grok-recipes
python -m venv .venv
. .venv/bin/activate          # Windows PowerShell: .venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

### Configure
Offline mode needs nothing. For live calls only: copy `.env.example` to `.env` and set `XAI_API_KEY` to the key the human gives you. Never print, log or commit the key.

### Verify
```bash
unset XAI_API_KEY             # PowerShell: Remove-Item Env:XAI_API_KEY -ErrorAction SilentlyContinue
python -m pytest --nbmake examples/
```
Expected: exit code 0 and a last line like `4 passed in 19.07s` (4 notebooks in `examples/`). Run it with `XAI_API_KEY` unset; the CI does the same on purpose, and a set key switches the notebooks from offline demo to live (paid) calls. This check can fail: each notebook contains `assert` cells that go red if a guard stops guarding. It proves the offline demos run, not that live Grok calls work.

### Uninstall / rollback
Delete the cloned folder (it contains the `.venv`). Nothing global is changed.

### Rules for the agent
- Do not run live calls without the human's yes and key; they cost money.
- Never write the API key into a notebook, a commit or a log. `.env` stays out of git.
- Do not "fix" a failing self-check cell by weakening it; it is there to fail when a guard stops guarding.

## Details

[![tests](https://github.com/tonydzi/grok-recipes/actions/workflows/tests.yml/badge.svg)](https://github.com/tonydzi/grok-recipes/actions/workflows/tests.yml)
[![python](https://img.shields.io/badge/python-3.12%2B-blue)](requirements.txt)
[![license](https://img.shields.io/badge/license-MIT-green)](LICENSE)

**Four production-readiness cookbooks for Grok: guardrails, grounded RAG, evals, and prompt-injection defense.**
Every recipe wraps the model in **deterministic, zero-token, fail-closed** checks written in plain Python —
and every recipe runs end-to-end **without an API key**, so you can read and execute the safety layer before you spend a cent.

## Quickstart — no API key

```bash
git clone https://github.com/tonydzi/grok-recipes && cd grok-recipes
pip install -r requirements.txt
pytest --nbmake examples/        # all four execute offline; each ends in a self-check cell that fails if a guard stops guarding
```

Then open any `examples/*/guide.ipynb` in Jupyter. Add `XAI_API_KEY` to a `.env` (see `.env.example`) and re-run:
the same guards now wrap live Grok calls, unchanged.

## The recipes

| Recipe | What it teaches | Deterministic core |
|---|---|---|
| [Deterministic Guardrails for Tool Calls](examples/deterministic_tool_call_guardrails/guide.ipynb) | Four checks around a function-calling loop | argument validation · action-tier gate for irreversible tools · output verification · grounding check |
| [Grounded RAG with Verifiable Citations](examples/grounded_rag_with_citations/guide.ipynb) | A retrieval loop where every sentence must be cited *and supported* | citation verifier: uncited / bad-id / unsupported → repair once, else refuse |
| [Evaluating Grok Applications](examples/evaluating_grok_applications/guide.ipynb) | An eval harness that stays cheap as it grows | tier-1 graders (JSON, schema, label, content) short-circuit before the tier-2 Grok judge; regression gate between variants |
| [Defending Against Prompt Injection](examples/defending_against_prompt_injection/guide.ipynb) | Keeping *tool output as data, never instructions* | fence · zero-token injection scan · taint-based capability control · egress allow-list |

Each notebook is self-contained: explanation in markdown, runnable Python in code cells, outputs committed.
They use the OpenAI-compatible client at `https://api.x.ai/v1`, the same idiom as xAI's own cookbook.

## Where these came from

All four were written for [xai-org/xai-cookbook](https://github.com/xai-org/xai-cookbook) and proposed there in July 2026:
[#45](https://github.com/xai-org/xai-cookbook/pull/45) guardrails (23 Jul), [#46](https://github.com/xai-org/xai-cookbook/pull/46) grounded RAG with citations,
[#48](https://github.com/xai-org/xai-cookbook/pull/48) evals, [#49](https://github.com/xai-org/xai-cookbook/pull/49) prompt-injection defense (all 24 Jul).
Three were withdrawn on 1 Aug so one contributor would not hold four slots in a 21-PR queue; #45 was rebuilt against the new
front-matter catalog on 1 Oct and closed on 15 Oct with no review.

In October 2026 the cookbook's maintainer rebuilt the repo and shipped his own TypeScript examples on the same four topics:
`prompt-injection-defenses`, `reasoning-effort-evals`, `docs-to-cited-answers`, `refund-agent-human-approval`.
Those are the official ones; read them first. These four are the Python notebooks that preceded them, kept here as-is with their
July dates and committed outputs, because the deterministic, run-without-a-key approach is still the thing worth copying.

The guardrails recipe answers the request in xai-cookbook [issue #18](https://github.com/xai-org/xai-cookbook/issues/18)
for deterministic function-call contracts; thanks to [@rokoss21](https://github.com/rokoss21) for raising the topic.

## Related

- [verbatim-citation-gate](https://github.com/tonydzi/verbatim-citation-gate) — the framework-agnostic citation auditor the RAG recipe applies.
- [agent-leash](https://github.com/tonydzi/agent-leash) — the wider control model behind taint-based capability control.
- [awesome-verified-agents](https://github.com/tonydzi/awesome-verified-agents) — tools that produce evidence about what an agent actually did.

## License

MIT. Built and used at [Palo Alto AI Research Lab](https://github.com/tonydzi/tonydzi).

## AI contributors

This project is built by a human + AI team. Credit for AI tools on commits follows one policy, one source for every repo: [AI-CONTRIBUTORS.md](https://github.com/tonydzi/.github/blob/main/AI-CONTRIBUTORS.md).
