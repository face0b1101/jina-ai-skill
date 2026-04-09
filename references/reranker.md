# Reranker API

Rerank documents by relevance to a query via `https://api.jina.ai/v1/rerank`.

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

After retrieving candidate documents (via search or vector DB), rerank them to improve relevance before feeding to an LLM.
