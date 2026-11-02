---
name: look-again
description: >-
  Saves conversation checkpoints ("look again" entries) as .md files and
  resumes them later with full context. SAVE mode: when the user says "look
  again", "tekrar bak", "bunu kaydet tekrar bakacağım", or "save this for
  later", capture a structured context snapshot (decisions, rejected
  alternatives, next steps), the final plan, and the last request/response
  verbatim into a shared checkpoint folder (Claude Code, Codex and other local
  agents) or a downloadable file (Claude.ai). RECALL mode: when the user says "tekrar bakacaklarım nelerdi",
  "look again list", "kaldığımız yerden devam et", "continue where we left
  off", or asks about saved checkpoints, list entries with short summaries
  and/or reopen one to continue the work with preserved context and consistent
  terminology. Always use this skill for any mention of saving conversation
  state to revisit later, or resuming a previously saved topic — even if the
  user doesn't say "look again" literally.
compatibility: Local agents with a persistent home directory (Claude Code, OpenAI Codex, any Agent Skills client) share one folder, ~/.agents/look-again/ by default. Claude.ai presents the file for download; recall there needs the user to upload saved files.
metadata:
  version: "1.2.0"
  author: sametbrr@gmail.com
---

# Look Again

Save conversation checkpoints to revisit later, and list them on demand.

Two modes. Detect from the user's phrasing:

| Signal (TR / EN) | Mode |
|---|---|
| "look again", "tekrar bak" (as a command, not a question), "bunu kaydet", "buna sonra tekrar bakacağım", "save this for later" | **SAVE** |
| "tekrar bakacaklarım nelerdi", "look again list", "kayıtları göster", "what did I save", "neleri kaydetmiştim" | **RECALL** |

If genuinely ambiguous, ask one short question. Otherwise infer.

---

## Environment detection (do this first, in both modes)

### Checkpoint folder (local agents)

Claude Code and Codex share **one** folder, so a checkpoint saved in one can be recalled in the other. Resolve it in this order:

1. `$LOOK_AGAIN_DIR`, if set.
2. `~/.agents/look-again/`, the default.

**Legacy folder:** `~/.claude/look-again/` (versions ≤1.1). RECALL always reads it as well. If it holds entries and the default folder doesn't exist yet, mention it once after the list and offer to move them. Move only after the user says yes.

```bash
LA_DIR="${LOOK_AGAIN_DIR:-$HOME/.agents/look-again}"
LA_LEGACY="$HOME/.claude/look-again"
```

### Host

- **Local agent with a persistent home** (Claude Code, Codex CLI/IDE, other Agent Skills clients): read and write `$LA_DIR` directly. Set `source` from the host: `claude-code` if `CLAUDECODE=1` or `CLAUDE_CODE_SESSION_ID` is set, `codex` if `CODEX_THREAD_ID` is set (check Codex first, because Codex started from inside Claude Code inherits the `CLAUDE_*` variables), otherwise `agent`.
- **Sandbox blocks the write.** Codex's `workspace-write` sandbox usually forbids paths outside the workspace. Don't fail silently: say the write was blocked and offer two options. (a) The user approves the write or reruns with access to `$LA_DIR`. (b) Save to `./.look-again/` in the current directory this time.
- **Claude.ai (sandboxed VM):** the filesystem resets between conversations. Never pretend a local write persists.
  - SAVE → create the entry file in `/mnt/user-data/outputs/` and present it for download. Tell the user to keep these files (e.g. in `~/.agents/look-again/` on their machine) and upload them to a future chat to use RECALL.
  - RECALL → look for uploaded `.md` entry files in `/mnt/user-data/uploads/`. If there are none, do NOT fabricate a list. Tell the user to upload their saved look-again files, or to run this in a local agent.

Quick heuristic: if `/mnt/user-data/outputs` exists, you are on Claude.ai. Otherwise treat the host as a local agent.

---

## SAVE mode

### What gets captured

The purpose of an entry is **seamless continuation**: a future session reading only this file must be able to resume the work with full context, consistent terminology, and no re-asking of settled questions.

1. **Context Snapshot** — structured state capture (see template sections): goal, settled decisions **with rationale**, rejected alternatives (so they are never re-proposed), constraints & exact technical details (names, versions, paths, endpoints), current state, ordered next steps, genuinely open questions. Be specific — vague summaries break continuation. Written in the conversation's language.
2. **Final Plan / Deliverable (verbatim)** — if the conversation produced a final plan, design, document, schema, or code (the thing brought "to the final stage"), embed it exactly as last agreed. This — not the last message — is usually what the user wants back.
3. **Last request (verbatim)** — the user's most recent substantive message *before* the trigger. If the trigger message itself contains substantive content beyond the command (e.g. "şu hatayı çöz ... look again"), use that message minus the command phrase.
4. **Last response (verbatim)** — the assistant's full reply to that request, exactly as written. Do not summarize, do not trim. If the response included file artifacts, list their names/paths after the verbatim text.
5. **Resume Instructions** — keep the template's standing block; add entry-specific notes if needed.

### File format

Use the template at [assets/entry-template.md](assets/entry-template.md). Rules:

- **Filename:** `YYYY-MM-DD-HHmm-<topic-slug>.md` (e.g. `2026-06-10-1432-clerk-jwt-validation.md`). Build the slug deterministically:
  1. Take 2–5 words that name the topic.
  2. Transliterate Turkish characters: `ç→c ğ→g ı→i ö→o ş→s ü→u` (and uppercase equivalents).
  3. Lowercase; replace every non-alphanumeric run with a single hyphen; trim leading/trailing hyphens.
  4. **Collision:** if the target filename already exists (same minute, same topic), append `-2`, `-3`, … to the slug. Never overwrite an existing entry.
- **Frontmatter is the index.** RECALL mode reads only frontmatter, so `title` and `summary` (one line, ≤160 chars) must stand alone without opening the body.
- **Context fields are mandatory.** Fill `project`, `directory`, `chat`:
  - Local agent (Claude Code, Codex, …): `directory` = `pwd`; `project` = git repo name (`basename $(git rev-parse --show-toplevel)` if inside a repo, else cwd basename); `chat` = `-`.
  - Claude.ai: `chat` = the conversation's title/topic (infer from the conversation if not explicit); `project` = Claude Project name if the chat is in one, else `-`; `directory` = `-`.
- **Verbatim blocks** are wrapped in five-backtick fences (`` ````` ``) so nested Markdown/code fences inside the original messages don't break the file. If the verbatim content itself contains a run of five or more backticks, use a fence one backtick longer than the longest run inside the content.

### Steps (local agent)

```bash
mkdir -p "$LA_DIR"
```

Write the entry file there, then confirm to the user with the saved path and the one-line summary. Nothing else — no echo of the full content.

### Steps (Claude.ai)

Write the entry file to `/mnt/user-data/outputs/`, present it, and remind the user to store it (ideally in their local `~/.agents/look-again/`) and re-upload it when they want to recall.

---

## RECALL mode

1. Collect entry files:
   - Local agent: `ls "$LA_DIR"/*.md "$LA_LEGACY"/*.md 2>/dev/null`. Also check `./.look-again/` if it exists. If the same `id` appears twice, list it once.
   - Claude.ai: `.md` files in `/mnt/user-data/uploads/` that contain look-again frontmatter (`id:` + `summary:` keys).
2. Read **only the frontmatter** of each file (e.g. `awk '/^---$/{n++; if(n==2)exit} n==1' file` or `head -n 15`). Do not load full bodies — entries can be large.
3. Present a list sorted newest-first. **List language** = the language of the message that triggered RECALL; if that's ambiguous, use the current conversation's language (entry `language:` fields describe the entries, not the list). Entries with `status: blocked` or `status: finalized` get that status appended to their summary cell (e.g. `… (finalized)`); they are listed, not hidden.

```
| # | Tarih | Başlık | Nerede | Özet |
|---|-------|--------|--------|------|
| 1 | 2026-06-10 14:32 | Clerk JWT doğrulama | cofiner-api | Clerk'in .NET SDK eksikliği; JWKS ile manuel doğrulama planı. |
```

("Nerede" = `project` if set, otherwise `chat`.)

4. End with: "Birini açmamı ister misin? (# numarası yeter)". When the user picks one — or asks to continue a saved topic — load that single file fully and **switch into continuation mode**:
   - Adopt the Context Snapshot as established working context; follow the entry's Resume Instructions.
   - Open with a 3–5 line recap (goal, where it stopped, next step) — then continue from Next Steps step 1 unless redirected.
   - Treat Decisions as settled and never re-propose Rejected Alternatives. Ask only about items listed under Open Questions, and only if blocking.
   - Keep terminology, naming, and output format consistent with the entry's Final Plan section.
5. Zero entries found → say so plainly; on Claude.ai, also explain the upload requirement. Never invent entries.

---

## Edge cases

- **Trigger as the very first message** (nothing to save): say there's no prior exchange yet; offer to save after the next response.
- **`git rev-parse` fails** (not a repo, git missing): set `project` = cwd basename; never let this abort the save.
- **`$LA_DIR` can't be created** (permissions, read-only fs, sandbox): report the error and offer to write the entry to `./.look-again/` instead — never drop the content silently.
- **Reopening a `finalized` entry:** continue normally, but note in the recap that the entry was marked finalized and ask whether to reopen it (set `status: in-progress`) or just consult it.
- **Duplicate save of the same exchange:** allowed, but note the earlier entry's filename in the confirmation.
- **Very long responses:** still verbatim. Disk is cheap; fidelity is the point of this skill.
- **Corrupted/foreign .md in the folder:** skip files lacking the `id:`/`summary:` frontmatter keys; mention skipped count only if >0.
