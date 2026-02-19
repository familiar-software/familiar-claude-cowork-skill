---
name: familiar
description: Locate, interpret, and filter the user's past behavior. Use when the harness needs to answer questions about past activity, repeated behaviors, or questions where on-screen behavior can be helpful.
---

# Familiar Stills Markdown

## Purpose

Use a repository of markdown files that represent actions, interactions, and visuals the user had on-screen.
These files are already preprocessed with a stable structure and are optimized for CLI retrieval.

## Locate The Data

1. Use a provided `contextFolderPath` if available.
2. Otherwise use `FAMILIAR_CONTEXT_FOLDER` when set.
3. Otherwise read `~/.familiar/settings.json` and use `contextFolderPath`.
4. If no path can be resolved, ask the user for the context folder location.
5. Stills markdown root is `<contextFolderPath>/familiar/stills-markdown`.

## Directory Structure

- Session folder pattern: `<contextFolderPath>/familiar/stills-markdown/session-<timestamp>/`
- Capture file pattern: `<contextFolderPath>/familiar/stills-markdown/session-<timestamp>/<captureTimestamp>.md`

## Naming Conventions

- Session name format: `session-<timestamp>`
- `<timestamp>` is ISO-8601 with `:` and `.` replaced by `-`
- Example session: `session-2026-02-05T16-35-35-626Z`
- Example capture file: `2026-02-05T16-35-35-770Z.md`
- Lexicographic sort order equals chronological order

## Content Format

Each file describes exactly one still image.

```md
---
format: familiar-layout-v0
screen_resolution: <width>x<height>
grid: <cols>x<rows>
app: <app name or unknown>
window_title_raw: <raw title or unknown>
window_title_norm: <normalized title or unknown>
url: <url or unknown>
---
# Layout Map
SCREEN <width>x<height>
GRID <cols>x<rows>
[HEADER] (x1,y1)-(x2,y2) text: "..."
[SIDEBAR] (x1,y1)-(x2,y2) text: "..."
[CONTENT] (x1,y1)-(x2,y2) text: "..."
[IMAGE] (x1,y1)-(x2,y2) desc: "..."

# OCR
- "Exact text line 1"
- "Exact text line 2"
```

Treat literal `unknown` and `UNCLEAR` as missing data.

## Working with Sequences (Important)

- A file with timestamp `X+1` occurred before `X+2`.
- When multiple markdown files exist in a session, preserve exact order to reconstruct user flow.
- For multi-step questions (for example "how did I solve X?"), use the ordered sequence, not a single frame.

## Filtering Options

- Time range:
  - Fetch all sessions that match the date range.
  - "today" means all sessions on that date, not only the latest one.
  - "from X to Y" includes both X and Y.
- App targeting: use frontmatter `app`.
- Window targeting: use `window_title_norm` or `window_title_raw`.
- Web targeting: use `url`.
- Text search: use exact matches in `# OCR`.
- Layout semantics: match `[HEADER]`, `[SIDEBAR]`, `[CONTENT]`, `[IMAGE]` blocks.
- Display targeting: use `screen_resolution` or `grid`.

## Retrieval Strategy

1. Narrow to a time range first and collect all relevant sessions.
2. Read relevant session files in chronological order.
3. Use `# OCR` for exact text and `Layout Map` for structure.
4. Avoid narrow keyword-only retrieval unless the request is explicitly keyword-specific.
5. For broad reflection requests, read the complete relevant sequence.

## Corework / Cloud Constraints

- If filesystem access is denied, ask for permission and retry.
- If running in a remote environment without local `~/.familiar` access, request either:
  - a synced copy of `<contextFolderPath>/familiar/stills-markdown`, or
  - direct path access provided by the user.
- Never assume unavailable files exist.

## Guardrails

- Do not infer text that is not explicitly present.
- Treat `UNCLEAR` and `unknown` as missing data.
- Avoid scanning full history unless the user explicitly asks for it.
