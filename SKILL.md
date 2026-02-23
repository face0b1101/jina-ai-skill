---
name: jina-ai
description: >-
  Use Jina AI APIs via REST/curl for web reading, web/academic/image search,
  text/image embeddings, document reranking, and PDF extraction. No SDK or MCP
  required — just curl with a Bearer token. Covers: Reader API (URL to markdown,
  screenshots), Search API (web, arXiv, SSRN, images, query expansion),
  Embeddings API, Reranker API, and PDF figure/table extraction.
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

## Tips

- **Always pair search with read.** Search results return snippets. Follow up with the Reader API on the best URLs to get full content.
- **Use `jq`** to format JSON output — Jina responses can be verbose.
- **Rate limits apply** to keyless Reader requests. Add your API key for higher limits.
- **Query expansion for research** — use the expansion endpoint first, then run multiple searches with the expanded queries for comprehensive coverage.
- **Embeddings task parameter** — set `"task": "text-matching"` for similarity search, or omit for general-purpose embeddings.
- **Reranker for RAG** — after retrieving candidate documents (via search or vector DB), rerank them to improve relevance before feeding to an LLM.
- **PDF extraction filters** — use `"type": "figure"` to skip tables/equations and reduce response size.
- **All endpoints return JSON** — set `Accept: application/json` on every request.
