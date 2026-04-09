# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.3.0] - 2026-04-09

### Changed

- Refactored `SKILL.md` from a 500-line monolith to a ~90-line routing manifest; auth pattern is the only curl example in the file
- Each API entry now provides a description-only entry (endpoint, body, response shape in prose) with an explicit link to its reference file for curl examples
- Moved all detailed curl examples, parameter tables, and response shapes into dedicated `references/` files for progressive disclosure (on-demand loading)

### Added

- `references/reader.md` — Reader API: URL to markdown, link extraction, image extraction, screenshots, headers reference
- `references/search.md` — Web search (time-filtered, localised), academic search (arXiv, SSRN), image search, query expansion
- `references/deepsearch.md` — Streaming, non-streaming, multi-turn, parameters table, response shape
- `references/embeddings.md` — Text embeddings, image/CLIP embeddings, task parameter guidance
- `references/reranker.md` — Full example, response shape, RAG usage notes
- `references/pdf-extraction.md` — arXiv paper ID, URL, parameters, response shape
- `references/vlm.md` — Image from URL, base64, text-only, streaming, cold-start note

## [1.2.1] - 2026-04-08

### Fixed

- Fix broken blockquote continuation in DeepSearch section (blank line between consecutive blockquotes)

## [1.2.0] - 2026-04-08

### Added

- DeepSearch section - multi-step research agent (read-search-reason loops) via `deepsearch.jina.ai/v1/chat/completions`
- Streaming and non-streaming curl examples for DeepSearch
- Multi-turn conversation example for DeepSearch
- DeepSearch parameters reference table (reasoning_effort, budget_tokens, max_attempts, team_size, hostname controls, etc.)
- DeepSearch response shape documentation (url_citation annotations, visitedURLs, readURLs)

### Changed

- Updated frontmatter description to include DeepSearch

## [1.1.0] - 2026-02-25

### Added

- VLM API (Vision Language Model) section — image+text chat, streaming, base64 input
- Example usage section in README with natural-language prompt examples
- Claude Desktop installation instructions (zip upload via Settings > Capabilities)
- CHANGELOG.md
- CC BY 4.0 licence (LICENCE file)

### Changed

- Replace placeholder GitHub URLs with `face0b1101/jina-ai-skill`

## [1.0.0] - 2026-02-23

### Added

- Initial release of Jina AI skill for AI coding assistants and Claude Desktop
- Comprehensive REST API documentation for Jina AI interactions via curl
- Authentication guide with Bearer token and `JINA_API_KEY` environment variable
- Reader API: URL to clean markdown, link extraction, image extraction, screenshots
- Web Search with time filtering, location, and language parameters
- Academic Search: arXiv and SSRN via domain parameter
- Image Search
- Query Expansion for deeper research coverage
- Embeddings API: text embeddings (`jina-embeddings-v5-text-small`) and image embeddings (`jina-clip-v2`)
- Reranker API (`jina-reranker-v3`) for document relevance sorting
- PDF Extraction: figures, tables, and equations from arXiv papers or any PDF URL
- Token Counting via Segment API
- Best practices and tips for working with Jina AI APIs
- Installation instructions for Cursor, Claude Code (personal and project), and Claude Desktop
- README with "Why a skill and not MCP?" rationale

[1.0.0]: https://github.com/face0b1101/jina-ai-skill/releases/tag/v1.0.0
[1.1.0]: https://github.com/face0b1101/jina-ai-skill/releases/tag/v1.1.0
[1.2.0]: https://github.com/face0b1101/jina-ai-skill/releases/tag/v1.2.0
[1.2.1]: https://github.com/face0b1101/jina-ai-skill/releases/tag/v1.2.1
[1.3.0]: https://github.com/face0b1101/jina-ai-skill/releases/tag/v1.3.0
[unreleased]: https://github.com/face0b1101/jina-ai-skill/compare/v1.3.0...HEAD
