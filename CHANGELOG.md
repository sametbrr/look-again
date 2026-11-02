# Changelog

All notable changes to this project are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2026-11-02

### Changed
- Plugin layout: the skill moved to `skills/look-again/` so Codex discovers it too. Claude Code skill ID is unchanged (`look-again:look-again`).
- Checkpoint folder is now host-neutral and shared by Claude Code and Codex: `$LOOK_AGAIN_DIR`, else `~/.agents/look-again/`. RECALL still reads the legacy `~/.claude/look-again/` and offers to move it.
- Host-neutral wording; `source` accepts `codex` and `agent`.

### Added
- `.codex-plugin/plugin.json` (Codex plugin), `AGENTS.md`, `version` in both manifests.
- Sandbox handling: if the write is blocked (e.g. Codex `workspace-write`), offer approval or `./.look-again/`.

### Fixed
- Release workflow reads the version from `.claude-plugin/plugin.json`, checks the Codex manifest matches, and passes notes via `--notes-file`. Commit text is no longer interpolated into shell.

## [1.1.0] - 2026-06-11

### Fixed
- Frontmatter-only read example: the `awk` one-liner now stops at the closing `---` (`awk '/^---$/{n++; if(n==2)exit} n==1'`); the previous form never terminated the frontmatter block.

### Added
- Formal slug algorithm: Turkish transliteration table, lowercase/hyphen normalization, and `-2`/`-3` suffix rule for filename collisions.
- RECALL language rule: list language follows the triggering message; mixed-language archives fall back to the current conversation language.
- Error-handling rules: `git rev-parse` failure fallback, unwritable directory reporting, and corrupted-frontmatter entries shown as skipped instead of breaking the list.
- Fence-escaping rule for verbatim blocks containing five or more backticks.
- `status` field semantics for RECALL listings.
- `LICENSE` file (MIT), `CHANGELOG.md`, `README.md` + `README.tr.md`, auto-release workflow.

## [1.0.0] - 2026-06-10

### Added
- Initial release: SAVE mode (structured context snapshot + final plan + last request/response verbatim into `~/.claude/look-again/` or a downloadable file) and RECALL mode (frontmatter-only listing, single-entry continuation with preserved context).
- Entry template (`assets/entry-template.md`) with frontmatter index fields.
