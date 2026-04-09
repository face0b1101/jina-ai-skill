# DeepSearch

Multi-step research agent that iteratively searches, reads web pages, and reasons until it finds the best answer. Handles complex questions requiring synthesis across multiple sources. OpenAI-compatible chat completions format via `https://deepsearch.jina.ai/v1/chat/completions`. Model: `jina-deepsearch-v1`.

**Streaming strongly recommended.** DeepSearch requests average ~57s and can exceed 120s. Disabling streaming risks 524 timeout errors. Streaming also surfaces intermediate reasoning steps so you can observe progress.

**When to use DeepSearch vs web search:** DeepSearch is for complex, multi-hop questions that require iterative reasoning and synthesis. For simple factual lookups, use `svip.jina.ai` web search instead.

## Streaming (Recommended)

```bash
curl -s -X POST "https://deepsearch.jina.ai/v1/chat/completions" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-deepsearch-v1",
    "stream": true,
    "reasoning_effort": "medium",
    "messages": [
      {"role": "user", "content": "What are the key differences between RAFT and standard fine-tuning for domain-specific RAG?"}
    ]
  }'
```

Streaming delivers SSE events. Reasoning steps appear inside `<think>...</think>` tags in `choices[].delta.content`. The final chunk includes `finish_reason: "stop"`, `usage` token counts, `visitedURLs`, and `readURLs`.

## Non-Streaming

```bash
curl -s -X POST "https://deepsearch.jina.ai/v1/chat/completions" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-deepsearch-v1",
    "messages": [
      {"role": "user", "content": "Compare the mass of the top 5 heaviest dinosaurs with modern animals"}
    ]
  }' | jq '.choices[0].message.content'
```

## Multi-Turn Conversation

Pass prior messages to continue a research thread:

```bash
curl -s -X POST "https://deepsearch.jina.ai/v1/chat/completions" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-deepsearch-v1",
    "stream": true,
    "messages": [
      {"role": "user", "content": "What is Jina AI node-deepresearch?"},
      {"role": "assistant", "content": "node-deepresearch is an open-source project by Jina AI..."},
      {"role": "user", "content": "How does its gap-question FIFO queue differ from recursive decomposition?"}
    ]
  }'
```

## Parameters Reference

| Parameter              | Type       | Description                                                                                    |
| ---------------------- | ---------- | ---------------------------------------------------------------------------------------------- |
| `reasoning_effort`     | `string`   | `low`, `medium`, or `high`. Preset controlling depth/breadth trade-off. Default: `medium`.     |
| `budget_tokens`        | `integer`  | Max tokens for the entire process. Overrides `reasoning_effort`.                               |
| `max_attempts`         | `integer`  | Max retries for answer evaluation. Overrides `reasoning_effort`.                               |
| `team_size`            | `integer`  | Number of parallel agents. They share `budget_tokens` but have independent `max_attempts`.     |
| `no_direct_answer`     | `boolean`  | Force web search even for trivial queries (disables answering from model knowledge at step 1). |
| `boost_hostnames`      | `string[]` | Domains given higher priority for reading.                                                     |
| `bad_hostnames`        | `string[]` | Domains excluded from reading.                                                                 |
| `only_hostnames`       | `string[]` | Restrict reading exclusively to these domains.                                                 |
| `search_provider`      | `string`   | Set to `"arxiv"` to restrict all searches to arXiv.                                            |
| `search_language_code` | `string`   | Force search query language (e.g. `"en"`, `"zh"`). Auto-detected by default.                   |

## Response Shape

Standard OpenAI chat completion, plus DeepSearch-specific fields on the final chunk:

- `choices[].delta.annotations[]` - `url_citation` objects with `title`, `url`, `exactQuote`, `dateTime`
- `visitedURLs` - all URLs the agent visited during research
- `readURLs` - URLs whose full content was read
- `usage.prompt_tokens`, `usage.completion_tokens`, `usage.total_tokens` - total tokens consumed across all search/read/reason steps
