# Embeddings API

Generate vector embeddings via `https://api.jina.ai/v1/embeddings`. OpenAI-compatible format.

## Text Embeddings

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

Set `"task": "text-matching"` for similarity search, or omit for general-purpose embeddings.

## Image Embeddings (CLIP)

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
