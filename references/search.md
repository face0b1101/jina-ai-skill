# Search APIs

All search endpoints use `POST https://svip.jina.ai/`. Requires API key.

## Web Search

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
