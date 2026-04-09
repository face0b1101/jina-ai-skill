# Reader API

Extract clean markdown content from any URL via `https://r.jina.ai/`.

## Read URL to Markdown

```bash
curl -s -X POST "https://r.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-Retain-Images: none" \
  -d '{ "url": "https://example.com/article" }' | jq .
```

Response contains `data.title`, `data.url`, and `data.content` (markdown).

## Read with Link Extraction

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

## Read with Image Extraction

```bash
curl -s -X POST "https://r.jina.ai/" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-With-Images-Summary: true" \
  -d '{ "url": "https://example.com" }' | jq .
```

Returns `data.images` alongside the markdown content.

## Capture Screenshot

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

## Headers Reference

| Header                  | Values                   | Effect                                    |
| ----------------------- | ------------------------ | ----------------------------------------- |
| `X-Return-Format`       | `screenshot`, `pageshot` | Return screenshot URL instead of markdown |
| `X-With-Links-Summary`  | `all`                    | Include all hyperlinks in response        |
| `X-With-Images-Summary` | `true`                   | Include all images in response            |
| `X-Retain-Images`       | `none`                   | Strip images from markdown output         |
| `X-Md-Link-Style`       | `discarded`              | Strip markdown link formatting            |
