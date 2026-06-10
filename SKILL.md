---
name: look-again
description: >-
  Saves conversation checkpoints ("look again" entries) as .md files and
  resumes them later with full context. SAVE mode: when the user says "look
  again", "tekrar bak", "bunu kaydet tekrar bakacağım", or "save this for
  later", capture a structured context snapshot (decisions, rejected
  alternatives, next steps), the final plan, and the last request/response
  verbatim into ~/.claude/look-again/ (Claude Code) or a downloadable file
  (Claude.ai). RECALL mode: when the user says "tekrar bakacaklarım nelerdi",
  "look again list", "kaldığımız yerden devam et", "continue where we left
  off", or asks about saved checkpoints, list entries with short summaries
  and/or reopen one to continue the work with preserved context and consistent
  terminology. Always use this skill for any mention of saving conversation
  state to revisit later, or resuming a previously saved topic — even if the
  user doesn't say "look again" literally.
compatibility: Claude Code (persistent disk at ~/.claude/look-again/) and Claude.ai (file presented for download; recall requires the user to upload their saved files).
metadata:
  version: "1.0.0"
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

- **Claude Code / persistent local machine:** `~/.claude/look-again/` survives between sessions. Read/write there directly.
- **Claude.ai (sandboxed VM):** the filesystem resets between conversations. Never pretend a write to `~/.claude/` persists.
  - SAVE → create the entry file in `/mnt/user-data/outputs/` and present it to the user for download. Tell them: keep these files (e.g. in their local `~/.claude/look-again/`), and upload them back to a future chat to use RECALL.
  - RECALL → look for uploaded `.md` entry files in `/mnt/user-data/uploads/`. If none are present, do NOT fabricate a list — tell the user to upload their saved look-again files (or run this in Claude Code).

Quick heuristic: if `/mnt/user-data/outputs` exists, you are on Claude.ai; if `~/.claude/` is a real persistent home directory (Claude Code CLI), write there.

---

## SAVE mode

### What gets captured

The purpose of an entry is **seamless continuation**: a future session reading only this file must be able to resume the work with full context, consistent terminology, and no re-asking of settled questions.

1. **Context Snapshot** — structured state capture (see template sections): goal, settled decisions **with rationale**, rejected alternatives (so they are never re-proposed), constraints & exact technical details (names, versions, paths, endpoints), current state, ordered next steps, genuinely open questions. Be specific — vague summaries break continuation. Written in the conversation's language.
2. **Final Plan / Deliverable (verbatim)** — if the conversation produced a final plan, design, document, schema, or code (the thing brought "to the final stage"), embed it exactly as last agreed. This — not the last message — is usually what the user wants back.
3. **Last request (verbatim)** — the user's most recent substantive message *before* the trigger. If the trigger message itself contains substantive content beyond the command (e.g. "şu hatayı çöz ... look again"), use that message minus the command phrase.
4. **Last response (verbatim)** — Claude's full reply to that request, exactly as written. Do not summarize, do not trim. If the response included file artifacts, list their names/paths after the verbatim text.
5. **Resume Instructions** — keep the template's standing block; add entry-specific notes if needed.

### File format

Use the template at [assets/entry-template.md](assets/entry-template.md). Rules:

- **Filename:** `YYYY-MM-DD-HHmm-<topic-slug>.md` (slug: lowercase ASCII, hyphens, 2–5 words, e.g. `2026-06-10-1432-clerk-jwt-validation.md`).
- **Frontmatter is the index.** RECALL mode reads only frontmatter, so `title` and `summary` (one line, ≤160 chars) must stand alone without opening the body.
- **Context fields are mandatory.** Fill `project`, `directory`, `chat`:
  - Claude Code: `directory` = `pwd`; `project` = git repo name (`basename $(git rev-parse --show-toplevel)` if inside a repo, else cwd basename); `chat` = `-`.
  - Claude.ai: `chat` = the conversation's title/topic (infer from the conversation if not explicit); `project` = Claude Project name if the chat is in one, else `-`; `directory` = `-`.
- **Verbatim blocks** are wrapped in five-backtick fences (`` ````` ``) so nested Markdown/code fences inside the original messages don't break the file.

### Steps (Claude Code)

```bash
mkdir -p ~/.claude/look-again
```

Write the entry file there, then confirm to the user with the saved path and the one-line summary. Nothing else — no echo of the full content.

### Steps (Claude.ai)

Write the entry file to `/mnt/user-data/outputs/`, present it, and remind the user to store it (ideally in their local `~/.claude/look-again/`) and re-upload it when they want to recall.

---

## RECALL mode

1. Collect entry files:
   - Claude Code: `ls ~/.claude/look-again/*.md`
   - Claude.ai: `.md` files in `/mnt/user-data/uploads/` that contain look-again frontmatter (`id:` + `summary:` keys).
2. Read **only the frontmatter** of each file (e.g. `awk '/^---$/{n++} n==1' file` or `head -n 15`). Do not load full bodies — entries can be large.
3. Present a list sorted newest-first, in the user's language:

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
5. Zero entries found → say so plainly; on Claude.ai additionally explain the upload requirement. Never invent entries.

---

## Edge cases

- **Trigger as the very first message** (nothing to save): say there's no prior exchange yet; offer to save after the next response.
- **Duplicate save of the same exchange:** allowed, but note the earlier entry's filename in the confirmation.
- **Very long responses:** still verbatim. Disk is cheap; fidelity is the point of this skill.
- **Corrupted/foreign .md in the folder:** skip files lacking the `id:`/`summary:` frontmatter keys; mention skipped count only if >0.
