---
name: jina-ai
description: >-
  Use Jina AI APIs via REST/curl for web reading, web/academic/image search,
  text/image embeddings, document reranking, PDF extraction, vision-language
  chat, and deep research. No SDK or MCP required - just curl with a Bearer
  token. Covers: Reader API (URL to markdown, screenshots), Search API (web,
  arXiv, SSRN, images, query expansion), DeepSearch (multi-step research
  agent), Embeddings API, Reranker API, PDF figure/table extraction, and VLM
  API (image + text chat).
---

# Jina AI

All Jina AI interaction is via REST API using `curl`. No SDK, client library, or MCP server required.

## Authentication

Every request needs a Jina API key (get one free at <https://jina.ai>):

```bash
JINA_API_KEY="jina_xxxxxxxxxxxx"

# All requests follow this pattern:
curl -s "https://<endpoint>" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '<json-body>' | jq .
```

**Important — variable expansion in curl:**

- Always use `$(printenv JINA_API_KEY)` instead of `$JINA_API_KEY` in curl headers to avoid empty-expansion issues.
- Some Reader API endpoints work without a key but have strict rate limits. Search, Embeddings, Reranker, and PDF extraction always require a key.

## Reader API

Extract clean markdown content from any URL via `https://r.jina.ai/`.

### Read URL to Markdown

```bash
curl -s -X POST "https://r.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-Retain-Images: none" \
  -d '{ "url": "https://example.com/article" }' | jq .
```

Response contains `data.title`, `data.url`, and `data.content` (markdown).

### Read with Link Extraction

```bash
curl -s -X POST "https://r.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-With-Links-Summary: all" \
  -H "X-Retain-Images: none" \
  -d '{ "url": "https://example.com" }' | jq .
```

Returns `data.links` as an array of `[anchorText, url]` pairs.

### Read with Image Extraction

```bash
curl -s -X POST "https://r.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-With-Images-Summary: true" \
  -d '{ "url": "https://example.com" }' | jq .
```

Returns `data.images` alongside the markdown content.

### Capture Screenshot

```bash
# First screen only (faster)
curl -s -X POST "https://r.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-Return-Format: screenshot" \
  -d '{ "url": "https://example.com" }' | jq '.data.screenshotUrl'

# Full page capture
curl -s -X POST "https://r.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-Return-Format: pageshot" \
  -d '{ "url": "https://example.com" }' | jq '.data.pageshotUrl'
```

### Reader Headers Reference

| Header                  | Values                   | Effect                                    |
| ----------------------- | ------------------------ | ----------------------------------------- |
| `X-Return-Format`       | `screenshot`, `pageshot` | Return screenshot URL instead of markdown |
| `X-With-Links-Summary`  | `all`                    | Include all hyperlinks in response        |
| `X-With-Images-Summary` | `true`                   | Include all images in response            |
| `X-Retain-Images`       | `none`                   | Strip images from markdown output         |
| `X-Md-Link-Style`       | `discarded`              | Strip markdown link formatting            |

## Web Search

Search the web via `https://svip.jina.ai/`. Requires API key.

```bash
curl -s -X POST "https://svip.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "q": "latest developments in LLM agents",
    "num": 10
  }' | jq '.results'
```

### Time-Filtered Search

```bash
curl -s -X POST "https://svip.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "q": "transformer architecture advances",
    "num": 10,
    "tbs": "qdr:w"
  }' | jq '.results'
```

Time filter values: `qdr:h` (past hour), `qdr:d` (past day), `qdr:w` (past week), `qdr:m` (past month), `qdr:y` (past year).

### Localised Search

```bash
curl -s -X POST "https://svip.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "q": "best coffee shops",
    "num": 10,
    "location": "London",
    "gl": "gb",
    "hl": "en"
  }' | jq '.results'
```

Optional params: `location` (city name), `gl` (country code), `hl` (language code).

## Academic Search

### arXiv

```bash
curl -s -X POST "https://svip.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "q": "attention mechanism neural networks",
    "domain": "arxiv",
    "num": 10
  }' | jq '.results'
```

### SSRN (Social Sciences)

```bash
curl -s -X POST "https://svip.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "q": "corporate governance emerging markets",
    "domain": "ssrn",
    "num": 10
  }' | jq '.results'
```

## Image Search

```bash
curl -s -X POST "https://svip.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "q": "neural network architecture diagram",
    "type": "images"
  }' | jq '.results'
```

Each result includes `imageUrl`, `title`, and other metadata.

## Query Expansion

Expand a query into multiple diverse reformulations for deeper research:

```bash
curl -s -X POST "https://svip.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "q": "machine learning for climate change",
    "query_expansion": true
  }' | jq '.results'
```

Returns an array of expanded query strings. Use these to run multiple searches for broader coverage.

## DeepSearch

Multi-step research agent that iteratively searches, reads web pages, and reasons until it finds the best answer. Handles complex questions requiring synthesis across multiple sources. OpenAI-compatible chat completions format via `https://deepsearch.jina.ai/v1/chat/completions`. Model: `jina-deepsearch-v1`.

> **Streaming strongly recommended.** DeepSearch requests average ~57s and can exceed 120s. Disabling streaming risks 524 timeout errors. Streaming also surfaces intermediate reasoning steps so you can observe progress.
>
> **When to use DeepSearch vs web search:** DeepSearch is for complex, multi-hop questions that require iterative reasoning and synthesis. For simple factual lookups, use `s.jina.ai` web search instead.

### Streaming (Recommended)

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

### Non-Streaming

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

### Multi-Turn Conversation

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

### DeepSearch Parameters Reference

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

### Response Shape

Standard OpenAI chat completion, plus DeepSearch-specific fields on the final chunk:

- `choices[].delta.annotations[]` - `url_citation` objects with `title`, `url`, `exactQuote`, `dateTime`
- `visitedURLs` - all URLs the agent visited during research
- `readURLs` - URLs whose full content was read
- `usage.prompt_tokens`, `usage.completion_tokens`, `usage.total_tokens` - total tokens consumed across all search/read/reason steps

## Embeddings API

Generate vector embeddings via `https://api.jina.ai/v1/embeddings`. OpenAI-compatible format.

### Text Embeddings

```bash
curl -s -X POST "https://api.jina.ai/v1/embeddings" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-embeddings-v5-text-small",
    "task": "text-matching",
    "input": [
      "How is the weather today?",
      "What is the current weather like?"
    ]
  }' | jq '.data[] | {index, embedding: .embedding[:3]}'
```

Response: `data[].embedding` (float array), `data[].index`, plus `usage` token counts.

### Image Embeddings (CLIP)

```bash
curl -s -X POST "https://api.jina.ai/v1/embeddings" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-clip-v2",
    "input": [
      { "image": "https://example.com/photo.jpg" },
      { "image": "data:image/jpeg;base64,/9j/..." }
    ]
  }' | jq '.data[] | {index, embedding: .embedding[:3]}'
```

Accepts image URLs or base64-encoded image strings.

## Reranker API

Rerank documents by relevance to a query via `https://api.jina.ai/v1/rerank`:

```bash
curl -s -X POST "https://api.jina.ai/v1/rerank" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-reranker-v3",
    "query": "What is deep learning?",
    "top_n": 3,
    "documents": [
      "Deep learning is a subset of machine learning using neural networks with many layers.",
      "The weather forecast predicts rain tomorrow.",
      "Neural networks can learn hierarchical representations of data.",
      "Python is a popular programming language."
    ]
  }' | jq '.results'
```

Response: `results[].index` (position in original array), `results[].relevance_score`, `results[].document.text`.

## PDF Extraction

Extract figures, tables, and equations from PDFs via `https://svip.jina.ai/extract-pdf`:

### From arXiv Paper ID

```bash
curl -s -X POST "https://svip.jina.ai/extract-pdf" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "2301.12345",
    "max_edge": 1024
  }' | jq '{num_floats: .meta.num_floats, num_pages: .meta.num_pages, floats: [.floats[] | {type, number, caption}]}'
```

### From Any PDF URL

```bash
curl -s -X POST "https://svip.jina.ai/extract-pdf" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://example.com/paper.pdf",
    "max_edge": 1024,
    "type": "figure,table"
  }' | jq '.floats[] | {type, number, caption}'
```

Parameters: `id` (arXiv ID) or `url` (direct PDF link), `max_edge` (max image dimension in px, default 1024), `type` (comma-separated filter: `figure`, `table`, `equation`).

Response: `floats[]` with `type`, `number`, `caption`, `page`, `image` (base64 PNG), `width`, `height`.

## Token Counting

Count tokens in text via `https://api.jina.ai/v1/segment`:

```bash
curl -s -X POST "https://api.jina.ai/v1/segment" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '{ "content": "Your text to count tokens for." }' | jq '{num_tokens, tokenizer}'
```

## VLM API (Vision Language Model)

Chat with images and text via `https://api-beta-vlm.jina.ai/v1/chat/completions`. OpenAI-compatible interface. Model: `jina-vlm`.

> **Cold start:** If the service returns a 503, retry after 30–60 seconds.

### Image from URL

```bash
curl -s "https://api-beta-vlm.jina.ai/v1/chat/completions" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-vlm",
    "messages": [{
      "role": "user",
      "content": [
        {"type": "text", "text": "Describe this image"},
        {"type": "image_url", "image_url": {"url": "https://example.com/photo.jpg"}}
      ]
    }]
  }' | jq '.choices[0].message.content'
```

### Local image (base64)

```bash
curl -s "https://api-beta-vlm.jina.ai/v1/chat/completions" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-vlm",
    "messages": [{
      "role": "user",
      "content": [
        {"type": "text", "text": "What is in this image?"},
        {"type": "image_url", "image_url": {"url": "data:image/jpeg;base64,'$(base64 -i image.jpg)'"}}
      ]
    }]
  }' | jq '.choices[0].message.content'
```

### Text-only query

```bash
curl -s "https://api-beta-vlm.jina.ai/v1/chat/completions" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-vlm",
    "messages": [{"role": "user", "content": "What is the capital of France?"}]
  }' | jq '.choices[0].message.content'
```

### Streaming response

Add `"stream": true` to receive tokens as they are generated:

```bash
curl -s "https://api-beta-vlm.jina.ai/v1/chat/completions" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-vlm",
    "stream": true,
    "messages": [{
      "role": "user",
      "content": [
        {"type": "text", "text": "List all visible objects as a JSON array of tags"},
        {"type": "image_url", "image_url": {"url": "https://example.com/photo.jpg"}}
      ]
    }]
  }'
```

## Tips

- **Always pair search with read.** Search results return snippets. Follow up with the Reader API on the best URLs to get full content.
- **Use `jq`** to format JSON output — Jina responses can be verbose.
- **Rate limits apply** to keyless Reader requests. Add your API key for higher limits.
- **Query expansion for research** — use the expansion endpoint first, then run multiple searches with the expanded queries for comprehensive coverage.
- **Embeddings task parameter** — set `"task": "text-matching"` for similarity search, or omit for general-purpose embeddings.
- **Reranker for RAG** — after retrieving candidate documents (via search or vector DB), rerank them to improve relevance before feeding to an LLM.
- **PDF extraction filters** — use `"type": "figure"` to skip tables/equations and reduce response size.
- **All endpoints return JSON** — set `Accept: application/json` on every request.
