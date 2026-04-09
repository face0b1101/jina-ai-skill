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
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '<json-body>' | jq .
```

Always use `$(printenv JINA_API_KEY)` instead of `$JINA_API_KEY` in curl headers to avoid empty-expansion issues. Some Reader API endpoints work without a key but have strict rate limits. Search, Embeddings, Reranker, and PDF extraction always require a key.

## Available APIs

### Reader API - `POST https://r.jina.ai/`

Extract clean markdown from any URL. Body: `{"url": "..."}`.
Returns `data.content` (markdown), `data.title`, `data.url`.
For curl examples, link/image extraction, screenshots, and headers reference, read [references/reader.md](references/reader.md).

### Web, Academic, and Image Search - `POST https://svip.jina.ai/`

Search the web, arXiv, SSRN, or images. Body: `{"q": "...", "num": 10}`.
Add `"domain": "arxiv"` or `"domain": "ssrn"` for academic search, `"type": "images"` for image search.
For curl examples, time filters, localisation, academic search, image search, and query expansion, read [references/search.md](references/search.md).

### DeepSearch - `POST https://deepsearch.jina.ai/v1/chat/completions`

Multi-step research agent for complex, multi-hop questions. OpenAI chat completions format. Model: `jina-deepsearch-v1`. Streaming strongly recommended (requests average ~57s).
For curl examples, streaming/non-streaming usage, multi-turn conversations, parameters, and response shape, read [references/deepsearch.md](references/deepsearch.md).

### Embeddings - `POST https://api.jina.ai/v1/embeddings`

Generate text or image vector embeddings. OpenAI-compatible format. Model: `jina-embeddings-v5-text-small` (text) or `jina-clip-v2` (images).
Body: `{"model": "...", "input": ["..."]}`.
For curl examples, task parameters, and image/CLIP embeddings, read [references/embeddings.md](references/embeddings.md).

### Reranker - `POST https://api.jina.ai/v1/rerank`

Rerank documents by relevance to a query. Model: `jina-reranker-v3`.
Body: `{"model": "jina-reranker-v3", "query": "...", "documents": [...]}`.
For curl examples and response shape, read [references/reranker.md](references/reranker.md).

### PDF Extraction - `POST https://svip.jina.ai/extract-pdf`

Extract figures, tables, and equations from PDFs. Body: `{"url": "..."}` or `{"id": "<arxiv-id>"}`.
Returns `floats[]` with `type`, `caption`, `image` (base64 PNG).
For curl examples, arXiv paper extraction, and parameter details, read [references/pdf-extraction.md](references/pdf-extraction.md).

### VLM (Vision Language Model) - `POST https://api-beta-vlm.jina.ai/v1/chat/completions`

Chat with images and text. OpenAI-compatible interface. Model: `jina-vlm`. Cold start: retry after 30-60s on 503.
For curl examples, base64 images, streaming, and text-only usage, read [references/vlm.md](references/vlm.md).

### Token Counting - `POST https://api.jina.ai/v1/segment`

Count tokens in text. Body: `{"content": "..."}`. Returns `num_tokens` and `tokenizer`.

## Tips

- **Pair search with read:** search returns snippets; follow up with Reader API on the best URLs for full content.
- **Use `jq`** to format verbose JSON responses.
- **Query expansion for research:** expand first, then run multiple searches for comprehensive coverage.
- **Reranker for RAG:** rerank retrieved candidates before feeding to an LLM.
- **All endpoints return JSON:** set `Accept: application/json` on every request.
