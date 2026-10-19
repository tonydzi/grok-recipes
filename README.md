# grok-recipes

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
