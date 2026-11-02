---
id: YYYY-MM-DD-HHmm-topic-slug          # must equal the filename without .md
date: 2026-06-10T14:32:00+03:00          # ISO 8601 with timezone
title: "Short human title (≤60 chars)"
summary: "One line, ≤160 chars. This is what RECALL mode shows in the list."
tags: [tag1, tag2]                       # 1–5 lowercase tags
source: claude-code                      # claude-code | codex | agent | claude-ai
project: "cofiner-api"                   # local agent: git repo name (or cwd basename); Claude.ai: Project name or "-"
directory: "/Users/dev/cofiner-api"   # local agent: absolute cwd; Claude.ai: "-"
chat: "Clerk SSO mimarisi"               # Claude.ai: conversation title/topic; local agent: "-"
language: tr                             # conversation language: tr | en
status: in-progress                      # in-progress | finalized | blocked
---

# {title}

## Context Snapshot

### Goal
<one paragraph: what is being built/planned and why>

### Decisions (settled — with rationale)
- <decision> — çünkü <rationale>
- ...

### Rejected Alternatives (do NOT re-propose)
- <alternative> — neden reddedildi: <reason>

### Constraints & Technical Details
- <stack, versions, exact names/paths/endpoints, naming conventions, limits>

### Current State
- Done: ...
- In progress: ...

### Next Steps (in order)
1. ...
2. ...

### Open Questions
- <only genuinely undecided items>

## Final Plan / Deliverable (verbatim)

<!-- If the conversation produced a final plan, design, document, schema, or code —
     the thing the user said they brought "to the final stage" — embed it here
     EXACTLY as last agreed. Omit this section only if no such artifact exists. -->

`````
<final plan / deliverable, verbatim>
`````

## Last Request (verbatim)

`````
<the user's message, exactly as written>
`````

## Last Response (verbatim)

`````
<the assistant's full reply, exactly as written>
`````

## Artifacts (optional)

- `path/or/name-of-file-produced.ext` — short note

## Resume Instructions (for the assistant reopening this entry)

- Adopt the Context Snapshot as established working context. Do not re-ask answered questions.
- Treat Decisions as settled; do not re-litigate or "improve" them unprompted.
- Never propose items listed under Rejected Alternatives.
- Continue from Next Steps step 1 unless the user redirects.
- Keep terminology, naming, and format consistent with the Final Plan section.
