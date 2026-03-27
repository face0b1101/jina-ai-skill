# Jina AI Skill

Agent Skill that teaches AI assistants how to use Jina AI APIs via REST/curl — no SDK or MCP required.

## What's in it

| File       | What it covers                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------ |
| `SKILL.md` | Auth, Reader API (URL→markdown, screenshots), Web/Academic/Image search, Embeddings, Reranker, PDF extraction, VLM |

The entire API surface fits in a single file — no `references/` directory needed.

## Capabilities

- **Reader** — convert any URL to clean markdown; extract links and images; capture screenshots
- **Web Search** — search the web with time, location, and language filters
- **Academic Search** — query arXiv and SSRN
- **Image Search** — find images across the web
- **Query Expansion** — expand a query into diverse reformulations for deeper research
- **Embeddings** — generate text and image (CLIP) vectors; OpenAI-compatible format
- **Reranker** — sort documents by relevance to a query
- **PDF Extraction** — pull figures, tables, and equations from any PDF or arXiv paper
- **Vision Language Model (VLM)** — chat with images and text, streaming supported
- **Token Counting** — count tokens via the Segment API

## Configuration

Set your Jina API key as an environment variable before starting your assistant:

```bash
export JINA_API_KEY="jina_xxxxxxxxxxxx"
```

Get a free key at [jina.ai](https://jina.ai). No server to run, no dependencies to install.

## Example usage

Once configured, just ask your assistant in natural language:

- "Read this article and summarise the key points: https://example.com/post"
- "Search the web for the latest developments in LLM agents"
- "Find arXiv papers on attention mechanisms published this year"
- "Generate embeddings for these three product descriptions and show cosine similarity"
- "Rerank these search results by relevance to my query"
- "Extract all figures and tables from this PDF: https://example.com/paper.pdf" (or a file reference from workspace)
- "Take a screenshot of https://example.com and describe the layout"

The assistant reads the skill, writes the `curl` commands, runs them, and explains the results.

## Installation

### Personal skill (all projects)

Clone into the shared skills directory:

```bash
git clone https://github.com/face0b1101/jina-ai-skill.git \
  ~/.agents/skills/jina-ai
```

Compatible agents (Cursor, Claude Code, VS Code, Codex, and others) scan `~/.agents/skills/` automatically per the [Agent Skills convention](https://agentskills.io/client-implementation/adding-skills-support).

### Project skill (one repo)

From your project root:

```bash
git clone https://github.com/face0b1101/jina-ai-skill.git /tmp/jina-ai-skill
mkdir -p .agents/skills/jina-ai
cp /tmp/jina-ai-skill/SKILL.md .agents/skills/jina-ai/
```

Commit the `.agents/skills/jina-ai/` directory so your team gets it too.

### Claude Desktop

1. Download the zip from the [latest release](https://github.com/face0b1101/jina-ai-skill/releases/latest)
2. In Claude, go to **Settings > Capabilities** and ensure **Code execution** is enabled
3. Under **Skills**, click **Upload skill** and select the zip

Claude will automatically use the skill when your prompt involves Jina AI. See [Using Skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude) for more detail.

### Verify

After installing, check that your agent can see the skill. In Claude Code, run `/skills` and confirm `jina-ai` appears in the list.

## Why a skill and not MCP?

Jina AI is a set of REST APIs with a single Bearer token. It doesn't need a protocol translation layer — it needs a good explanation.

**A skill is just markdown** that tells the LLM how authentication works, gives it `curl` patterns for every endpoint, and documents request/response shapes. The LLM reads this, then writes and executes the actual API calls.

**An MCP server** would mean a separate process to run and maintain, tool schemas consuming context tokens on every turn, and version compatibility issues between the server and your environment.

Skills achieve progressive disclosure by design: `SKILL.md` loads when triggered, and the LLM only reads the sections it needs. No tool schema overhead, no server process, zero dependencies.

MCP still makes sense for stateful connections, binary protocols, complex auth flows (OAuth), and non-HTTP tools. Jina AI doesn't need any of that.

## Licence

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — see [LICENCE](LICENCE) for full text.
