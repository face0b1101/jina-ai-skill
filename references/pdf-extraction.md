# PDF Extraction

Extract figures, tables, and equations from PDFs via `https://svip.jina.ai/extract-pdf`.

## From arXiv Paper ID

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

## From Any PDF URL

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

## Parameters

- `id` (arXiv ID) or `url` (direct PDF link) - one is required
- `max_edge` - max image dimension in px (default 1024)
- `type` - comma-separated filter: `figure`, `table`, `equation`

## Response

`floats[]` with `type`, `number`, `caption`, `page`, `image` (base64 PNG), `width`, `height`.
