[![GitHub release](https://img.shields.io/github/v/release/sametbrr/look-again?display_name=tag&sort=semver)](https://github.com/sametbrr/look-again/releases/latest)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/agentskills.io-compatible-blue)](https://agentskills.io)

# Look Again

An Agent Skill and plugin for Claude Code and Codex that saves conversation checkpoints as markdown files and resumes them later with full context — decisions, rejected alternatives, and the final plan preserved verbatim.

> 🇹🇷 Türkçe için [README.tr.md](README.tr.md)

---

## Quick Start

```bash
claude plugin marketplace add sametbrr/skill-hub && claude plugin install look-again@skill-hub   # Claude Code
codex plugin marketplace add sametbrr/skill-hub && codex plugin add look-again@skill-hub         # Codex
```

```
> "look again"          # save the current state as a checkpoint
> "look again list"     # list saved checkpoints, pick one to continue
```

That's it — entries land in `~/.agents/look-again/` and survive between sessions. Claude Code and Codex share the folder, so you can save in one and resume in the other.

---

## Features

- **Structured snapshots** — goal, settled decisions with rationale, rejected alternatives, constraints, and ordered next steps
- **Verbatim fidelity** — the final plan and the last request/response are embedded exactly as written, never summarized
- **Seamless continuation** — reopening an entry restores working context; settled decisions are never re-asked
- **Frontmatter index** — RECALL lists entries by reading only frontmatter, so large archives stay fast
- **Cross-agent** — one shared checkpoint folder for Claude Code and Codex; download/upload flow on Claude.ai

---

## Requirements

- Claude Code, OpenAI Codex, or any [agentskills.io](https://agentskills.io)-compatible agent
- On Claude.ai: ability to download and re-upload `.md` files (no persistent disk)

---

## Installation

**Claude Code** (plugin)

```bash
claude plugin marketplace add sametbrr/skill-hub
claude plugin install look-again@skill-hub
```

**Codex** (plugin)

```bash
codex plugin marketplace add sametbrr/skill-hub
codex plugin add look-again@skill-hub
```

**Manual** (any Agent Skills client): copy `skills/look-again/` into `~/.claude/skills/` or `~/.agents/skills/`.

Start a new session. The skill loads automatically when you mention saving or resuming a checkpoint.

### Checkpoint folder

| Order | Location |
|---|---|
| 1 | `$LOOK_AGAIN_DIR`, if set |
| 2 | `~/.agents/look-again/` (default, shared by Claude Code and Codex) |
| legacy | `~/.claude/look-again/` (v1.1 and earlier): still read by RECALL, which offers to move it |

In Codex's `workspace-write` sandbox a write outside the workspace may be blocked. The skill then asks you to approve it, or saves to `./.look-again/` for that time.

---

## Usage

The skill detects the mode from natural language — Turkish or English:

| Mode | Trigger examples | What happens |
|---|---|---|
| **SAVE** | "look again", "tekrar bak", "save this for later", "bunu kaydet" | Writes a checkpoint file: context snapshot + final plan + last exchange verbatim |
| **RECALL** | "look again list", "tekrar bakacaklarım nelerdi", "continue where we left off" | Lists entries newest-first; pick one to switch into continuation mode |

Entries are named `YYYY-MM-DD-HHmm-<topic-slug>.md` and follow [skills/look-again/assets/entry-template.md](skills/look-again/assets/entry-template.md). In continuation mode the assistant adopts the saved context, never re-proposes rejected alternatives, and continues from the saved next steps.

---

## Limitations

On Claude.ai the sandbox filesystem resets between conversations, so checkpoints cannot persist server-side.

**Workaround:** SAVE presents the entry as a downloadable file — keep it locally (e.g. in `~/.agents/look-again/`) and upload it back to a future chat to RECALL.

---

## License

MIT — see [LICENSE](LICENSE).
