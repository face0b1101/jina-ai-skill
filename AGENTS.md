# Project Rules

> **Single Source of Truth** for AI coding assistants

______________________________________________________________________

______________________________________________________________________

## Releasing to GitHub

This project ships as a **zip of user-facing files** attached to a GitHub Release. The zip is built ephemerally — it is never committed to the repo.

### Files included in the release zip

Only these files belong in the zip:

| File              | Purpose                                    |
| ----------------- | ------------------------------------------ |
| `SKILL.md`        | The skill manifest (core deliverable)      |
| `references/*.md` | Detailed API instructions loaded on demand |
| `README.md`       | Installation and usage guide               |
| `LICENCE`         | CC BY 4.0 licence text                     |
| `CHANGELOG.md`    | Release history                            |

Do **not** include `AGENTS.md`, dotfiles (`.gitignore`, `.markdownlint.json`), or any other development artefact.

### Release workflow

1. **Update `CHANGELOG.md`** — move items from `[Unreleased]` into a new version heading with today's date. Follow [Keep a Changelog](https://keepachangelog.com/) format.

2. **Commit** the changelog (and any other release-ready changes) with message `release: vX.Y.Z — <summary>`.

3. **Tag** with an annotated tag: `git tag -a vX.Y.Z -m "vX.Y.Z"`.

4. **Push** commit and tag: `git push origin main --tags`.

5. **Build the zip** in `/tmp` (not in the repo):

   ```bash
   zip /tmp/jina-ai-skill-vX.Y.Z.zip SKILL.md references/*.md README.md LICENCE CHANGELOG.md
   ```

6. **Create the GitHub Release** with the zip attached:

   ```bash
   gh release create vX.Y.Z /tmp/jina-ai-skill-vX.Y.Z.zip \
     --title "vX.Y.Z" \
     --notes "<release notes from CHANGELOG>"
   ```

### Important notes

- The `releases/latest` URL in `README.md` resolves automatically — no need to update it.
- GitHub adds "Source code" archives to every release by default; these cannot be removed. The uploaded zip is the intended download for end users.
- Use [Semantic Versioning](https://semver.org/): bump **minor** for new API coverage or features, **patch** for fixes or doc corrections.

______________________________________________________________________

## AI Assistant Operating Rules

Concise policy reference for all coding agents touching this repository. Keep responses factual and avoid speculative language.

### 1. Communication & Planning

- Always mention assumptions; ask the user to confirm anything ambiguous before editing.
- Follow the required plan/approval workflow when prompted and wait for explicit approval to execute.
- Use UK-English spelling in comments, documentation, and commit messages.

### 2. File Safety

- Do **not** edit `.env` or other environment files; only reference `.env.example`.
- Delete files only when you created them or the user explicitly instructs you to remove older assets.
- Never run destructive git commands (`git reset --hard`, `git checkout --`, `git restore`, `rm -rf .git`) unless the user provides written approval in this thread.
- Treat rename automation as a one-time setup; never re-run it on an established project.

### 3. Collaboration Etiquette

- If another agent has edited a file, read their changes and build on them-do not revert or overwrite.
- Coordinate before touching large refactors that might conflict with ongoing work.
- Keep diffs minimal and reviewable; use targeted edits rather than rewriting whole files.

### 4. Git & Commits

- Check `git status` before staging and before committing.
- Keep commits atomic and list paths explicitly, e.g. `git commit -m "feat: add CI" -- path/to/file`.
- For new files: `git restore --staged :/ && git add <paths> && git commit -m "<msg>" -- <paths>`.
- Quote any paths containing brackets/parentheses when staging to avoid globbing.
- Never amend existing commits unless the user instructs you to.
- Don't plaster all commits and git issues with "Made with AI", "AI did everything" or anything similar.

### 5. Pre-flight Checklist

1. Read the task, confirm assumptions, and outline the approach.
2. Inspect the relevant files (include imports/configs for context).
3. Review changes for spelling, formatting, and consistency before committing.
4. Summarise edits, mention tests, and flag follow-up work in the final response.
