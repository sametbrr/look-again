[![GitHub release](https://img.shields.io/github/v/release/sametbrr/look-again?display_name=tag&sort=semver)](https://github.com/sametbrr/look-again/releases/latest)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/agentskills.io-compatible-blue)](https://agentskills.io)

# Look Again

A Claude Code skill that saves conversation checkpoints as markdown files and resumes them later with full context — decisions, rejected alternatives, and the final plan preserved verbatim.

> 🇹🇷 Türkçe için [README.tr.md](README.tr.md)

---

## Quick Start

```bash
git clone https://github.com/sametbrr/look-again ~/.claude/skills/look-again
```

```
> "look again"          # save the current state as a checkpoint
> "look again list"     # list saved checkpoints, pick one to continue
```

That's it — entries land in `~/.claude/look-again/` and survive between sessions.

---

## Features

- **Structured snapshots** — goal, settled decisions with rationale, rejected alternatives, constraints, and ordered next steps
- **Verbatim fidelity** — the final plan and the last request/response are embedded exactly as written, never summarized
- **Seamless continuation** — reopening an entry restores working context; settled decisions are never re-asked
- **Frontmatter index** — RECALL lists entries by reading only frontmatter, so large archives stay fast
- **Dual environment** — persistent disk on Claude Code, download/upload flow on Claude.ai

---

## Requirements

- Claude Code (or any [agentskills.io](https://agentskills.io)-compatible agent)
- On Claude.ai: ability to download and re-upload `.md` files (no persistent disk)

---

## Installation

```bash
git clone https://github.com/sametbrr/look-again ~/.claude/skills/look-again
```

Start a new Claude Code session. The skill loads automatically when you mention saving or resuming a checkpoint.

---

## Usage

The skill detects the mode from natural language — Turkish or English:

| Mode | Trigger examples | What happens |
|---|---|---|
| **SAVE** | "look again", "tekrar bak", "save this for later", "bunu kaydet" | Writes a checkpoint file: context snapshot + final plan + last exchange verbatim |
| **RECALL** | "look again list", "tekrar bakacaklarım nelerdi", "continue where we left off" | Lists entries newest-first; pick one to switch into continuation mode |

Entries are named `YYYY-MM-DD-HHmm-<topic-slug>.md` and follow [assets/entry-template.md](assets/entry-template.md). In continuation mode the assistant adopts the saved context, never re-proposes rejected alternatives, and continues from the saved next steps.

---

## Limitations

On Claude.ai the sandbox filesystem resets between conversations, so checkpoints cannot persist server-side.

**Workaround:** SAVE presents the entry as a downloadable file — keep it locally (e.g. in `~/.claude/look-again/`) and upload it back to a future chat to RECALL.

---

## License

MIT — see [LICENSE](LICENSE).
