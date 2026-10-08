---
name: get-compare-llms
description: Compare different LLMs for coding web apps and provide a ranking with cost estimates.
---

# Step 1 - Parse `$ARGUMENTS`

Parse using these rules:

- If `$ARGUMENTS` is empty, ask the user to provide the LLMs they want to compare. Stop until they respond.

# Step 2 - Calibration

Ask the user how detailed the output should be, using the question tool:

- `Quick compare` — 2 rating columns (Long-horizon, Planning).
- `Deep` — 5 rating columns, main table only. Default if user skips.
- `Very deep` — 10 rating columns, both tables.
- Always ask via the question tool, never parse depth from `$ARGUMENTS`.
- Depth only filters displayed columns — ranking logic stays identical in all modes.

# Step 3 - Best Effort Ranking

- Rank only models from Step 1.
- No extra models.
- No fake names.
- Use real public names only.
- Research first.
- Use websearch for prices and benchmarks as of current month + year.
- Stamp date in output, format: `As of Month YYYY:`.
- If price unknown, write `unknown`, never guess.

## Benchmark sources

Use these sources for research.
Pick sources matching the rating column you score.

| # | Source | Best used for | URL |
|---|---|---|---|
| 1 | **SWE-bench Verified** | Real-world coding, debugging, long-horizon software engineering | [SWE-bench Verified](https://www.swebench.com/verified) |
| 2 | **SWE-bench** | Software engineering / agentic coding | [SWE-bench](https://www.swebench.com/) |
| 3 | **LiveCodeBench** | Code generation, self-repair, execution, test prediction | [LiveCodeBench](https://livecodebench.github.io/) |
| 4 | **Hugging Face Open LLM Leaderboard** | General reasoning / standardized LLM evaluation | [Open LLM Leaderboard](https://huggingface.co/open-llm-leaderboard) |
| 5 | **Hugging Face Leaderboards & Evaluations** | Discovering additional standardized benchmarks | [HF Leaderboards & Evaluations](https://huggingface.co/docs/leaderboards/main/index) |
| 6 | **LMSYS Chatbot Arena** | Human preference / practical model quality | [Chatbot Arena](https://lmarena.ai/) |
| 7 | **HumanEval** | Traditional code-generation capability | [HumanEval](https://github.com/openai/human-eval) |
| 8 | **MTEB** | Embeddings / retrieval / context-related capabilities | [MTEB Leaderboard](https://huggingface.co/spaces/mteb/leaderboard) |
| 9 | **IFEval** | Instruction following | [IFEval on Hugging Face](https://huggingface.co/collections/google/ifeval-65d7f0e2f0c3c0f1a1e5e5b4) |
| 10 | **Official model provider docs/pricing** | Price, context, capabilities, latency | **Provider-specific** |

## Category to source map

Map each rating column to its sources.

- Long-horizon → SWE-bench Verified, SWE-bench.
- Debug → SWE-bench Verified, LiveCodeBench.
- Planning → SWE-bench Verified, LiveCodeBench.
- Refactor → SWE-bench Verified, HumanEval.
- Frontend → Chatbot Arena, provider docs.
- Agentic → SWE-bench, SWE-bench Verified.
- Tests → LiveCodeBench, HumanEval.
- Review → SWE-bench Verified, Chatbot Arena.
- Instructions → IFEval.
- Context → MTEB, provider docs.
- Cost and latency → official model provider docs only.
- Rank for day-to-day web app coding: features, refactors, debugging.
- Score 100 pts total: quality 60, cost 25, latency 15.
- Quality = features plus refactors plus debugging skill.
- Higher quality outranks lower cost.
- Cost decides equal quality.
- Latency decides equal quality plus cost.
- Pick 1x baseline = rank 1 daily driver, not smartest max model.
- Blended cost = (3*in + 1*out)/4.
- Cost vs baseline = blended divided by baseline blended.
- Round to 1 decimal, format `Nx`.
- If in or out price unknown, Cost vs baseline = `unknown`.
- Keep rows sorted best to worst.
- No ties.
- Break ties by cheaper cost wins.
- Rate each category with stars: `★★★★★` down to `★☆☆☆☆`.

# Step 4 - Output

Provide the final comparison table with the ranking and cost estimates, filtered by chosen depth.

- Quick compare: main table with Model, In / Out / 1M, Cost, Long-horizon, Planning only.
- Deep: main table with Model, In / Out / 1M, Cost, plus 5 primary categories.
- Very deep: both tables (main + secondary), full 10 rating columns.
- Primary categories: Long-horizon, Debug, Planning, Refactor, Frontend.
- Second table: same row order, Model plus 5 secondary categories.
- Secondary categories: Agentic, Tests, Review, Instructions, Context.
- Example format only, not real data.
- Names and prices below are placeholders.
- Never output placeholders as real ranking.

Quick compare example:

```markdown
# Trust-Me-Bro Benchmark

As of October 2026, best effort ranking for **coding web apps**:

| Model   | In / Out / 1M | Cost | Long-horizon | Planning |
| :------ | :------------ | :--- | :----------- | :------- |
| Model-A | $3 / $15      | 1x   | ★★★★★        | ★★★★☆    |
| Model-B | $2 / $8       | 0.6x | ★★★☆☆        | ★★★☆☆    |

p.s. LLM with 1x cost is the baseline.
```

Deep / very deep example (very deep appends the secondary table):

```markdown
# Trust-Me-Bro Benchmark

As of October 2026, best effort ranking for **coding web apps**:

| Model   | In / Out / 1M | Cost | Long-horizon | Debug | Planning | Refactor | Frontend |
| :------ | :------------ | :--- | :----------- | :---- | :------- | :------- | :------- |
| Model-A | $3 / $15      | 1x   | ★★★★★        | ★★★★☆ | ★★★★☆    | ★★★★☆    | ★★★★☆    |
| Model-B | $2 / $8       | 0.6x | ★★★☆☆        | ★★★☆☆ | ★★★☆☆    | ★★★☆☆    | ★★★★☆    |
| Model-C | $15 / $75     | 5x   | ★★★★★        | ★★★★☆ | ★★★★★    | ★★★☆☆    | ★★★☆☆    |

p.s. LLM with 1x cost is the baseline.

Secondary categories (same row order):

| Model   | Agentic | Tests | Review | Instructions | Context |
| :------ | :------ | :---- | :----- | :----------- | :------ |
| Model-A | ★★★★☆   | ★★★★☆ | ★★★★☆  | ★★★★★        | ★★★★★   |
| Model-B | ★★★☆☆   | ★★★☆☆ | ★★★☆☆  | ★★★★☆        | ★★★★☆   |
| Model-C | ★★★★★   | ★★★★★ | ★★★★★  | ★★★☆☆        | ★★★★★   |
```
