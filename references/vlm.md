# VLM API (Vision Language Model)

Chat with images and text via `https://api-beta-vlm.jina.ai/v1/chat/completions`. OpenAI-compatible interface. Model: `jina-vlm`.

**Cold start:** If the service returns a 503, retry after 30-60 seconds.

## Image from URL

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

## Local Image (base64)

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

## Text-Only Query

```bash
curl -s "https://api-beta-vlm.jina.ai/v1/chat/completions" \
  -H "Authorization: Bearer $(printenv JINA_API_KEY)" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jina-vlm",
    "messages": [{"role": "user", "content": "What is the capital of France?"}]
  }' | jq '.choices[0].message.content'
```

## Streaming

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
