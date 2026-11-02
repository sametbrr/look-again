# look-again — agent guide

Agent Skill + plugin (Claude Code and Codex) that saves conversation checkpoints as markdown and resumes them.

## Layout
- `skills/look-again/SKILL.md`: the skill. `skills/look-again/assets/entry-template.md` is the checkpoint template.
- `.claude-plugin/plugin.json` and `.codex-plugin/plugin.json`: manifests. Keep `version` identical in both and in SKILL.md `metadata.version`.
- `.github/workflows/release.yml`: tags `v<version>` from `.claude-plugin/plugin.json` on push to main.

## Rules
- Host-neutral wording. No hardcoded `~/.claude/...` paths except the documented legacy folder.
- Checkpoint data lives in `$LOOK_AGAIN_DIR` or `~/.agents/look-again/`, never inside the plugin dir.
- Update `CHANGELOG.md` and both READMEs (EN + TR mirror) with every version bump.
- Never commit or push without explicit approval from the maintainer.

## Checks
- `claude plugin validate .`
- Codex: install from a local marketplace in an isolated `CODEX_HOME` and confirm `look-again:look-again` appears in `codex debug prompt-input`.
