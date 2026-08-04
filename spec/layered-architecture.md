# Four-Layer Memory Architecture

> OptMem organizes memory into four layers, from raw data to distilled personality. Each layer has a distinct purpose, format, and update rule.

```
L3  Personality Summary    ←  Stable user portrait, auto-injected every session
L2  Scenario Archives      ←  Topic clusters with recency, grouped by context
L1  Atomic Memories        ←  Structured facts tagged with [stated]/[observed]
L0  Raw Logs               ←  Timestamped, append-only, never edited
```

---

## L0 · Raw Logs (`memory/log/YYYY-MM-DD.md`)

### Purpose
The immutable record of everything that happened. The single source of truth.

### Format
```
## YYYY-MM-DD

[HH:MM] [stated]: User said "I prefer Python over Java for backend"
[HH:MM+1] [observed]: Agent tried to compile Python code — user opened terminal
[HH:MM+3] [decided]: User chose FastAPI over Django for the new project
[HH:MM+10] [learned]: User read an article about async I/O performance
```

### Rules
- **Append only** — never edit, never delete, never rewrite
- **Corrections are new entries** — tag with `[override]` and reference the old entry
- **One line per event** — timestamp, tag, content
- **Tag taxonomy**: `[stated]` / `[observed]` / `[inferred]` / `[decided]` / `[learned]`

### Update frequency
Every interaction produces at least one log entry. A typical session generates 5–20 entries.

### Recoverability
All higher layers (L1–L3) can be reconstructed by reading and summarizing L0.

---

## L1 · Atomic Memories (`memory/topics/*.md`, `memory/areas/*.md`, daily notes)

### Purpose
Structured facts extracted from L0, organized by domain or project. Each entry carries its epistemic tag.

### Format
```
## Topics (memory/topics/learning.md)
- [stated] Prefers "essence extraction" reading style over simple summaries
- [stated] After learning, requires cross-referencing with existing knowledge
- [observed] Learns best through GitHub repo deep-dives
- [inferred] Likely has 3–5 years of software engineering experience

## Areas (memory/areas/knowledge-base.md)
- [stated] Building a personal knowledge base using sage-wiki
- [decided] Chose Markdown + Git over Notion for version control
```

### Organization
- **topics/** — domain-specific knowledge (learning preferences, tools, content)
- **areas/** — ongoing projects and responsibilities
- **YYYY-MM-DD.md** — daily notes bridging L0 and L1

### Rules
- Each fact on a single line
- Always include the epistemic tag
- Update as new information arrives; old entries are not deleted but superseded
- Cross-reference related files using `[[wikilink]]` format

### Update frequency
Updated during sessions when new atomic facts are identified. Not every interaction produces a new L1 entry.

---

## L2 · Scenario Archives (daily notes + area files)

### Purpose
Clusters of L1 facts grouped by context, with recency and relevance signals. These answer "what's currently important?"

### Organization
- **Daily notes** (`memory/YYYY-MM-DD.md`) — a summary of what happened that day, synthesizing L0 log entries
- **Area files** (`memory/areas/*.md`) — persistent project/topic files that accumulate over weeks/months

### Format
```
## 2026-08-04 Daily Summary
- Learned about TencentDB Agent Memory architecture
- Compared four-layer structure with OptMem — architecture is consistent
- User confirmed OptMem is worth open-sourcing
- Action item: prepare OpenDesign-inspired PRD requirements list
```

### Rules
- Daily notes summarize the day, not copy-paste the log
- Area files are living documents, updated as projects progress
- Recency matters: newer entries in area files get more weight

### Update frequency
- Daily notes: once per day (typically at session end)
- Area files: as needed during project work

---

## L3 · Personality Summary (`memory/profile.md`, `memory/preferences.md`)

### Purpose
The distilled, stable portrait of the user — the few facts that define who they are and how the Agent should behave. This is auto-injected at the start of every session.

### Format
```
## profile.md — Who the user is
- [stated] Goes by the nickname "志哥"
- [stated] Works in AI/ML, focused on Three.js and Web3D
- [stated] Prefers direct, colloquial communication

## preferences.md — How the Agent should behave
- [stated] Call the user "志哥"
- [stated] Use colloquial style with light workplace humor
- [stated] Be opinionated, not hedging
```

### Rules
- **profile.md** is for stable identity facts (< 300 words)
- **preferences.md** is for behavioral instructions
- These are the highest-priority files — read first every session
- Updates are rare (weeks to months between changes)

### Update frequency
Rare. Only updated when a stable fact about the user changes, or when a behavioral preference is explicitly set.

---

## How the Layers Work Together

### Session Start (Wake-Up)
```
1. Read MEMORY.md (navigation index)
2. Read profile.md + preferences.md (L3)
3. Read latest daily note (L2)
4. Read action-items.md (L2)
5. Optional: search topics/ for context-relevant facts (L1)
```

### During Interaction
```
1. Log every significant event to L0 (append-only)
2. Extract new atomic facts → L1 (with [stated] tags)
3. When contradictions arise → conflict-arbitration process
```

### Session End (Compression)
```
1. Synthesize L0 entries → daily note (L2)
2. Check if daily note exceeds threshold → compress to summary
3. Update area files if project state changed
4. Rarely: update profile.md/preferences.md (L3)
```

### Reconstruction (Any Time)
```
L3 ← summarize L2 ← aggregate L1 ← parse L0
```

Every layer can be rebuilt from the layer below it. The system is lossless in the sense that the raw log is always available.

---

## Comparison with Other Frameworks

| Layer | OptMem | MemGPT | LangMem | TencentDB |
|---|---|---|---|---|
| L0 Raw | Append-only Markdown logs | Messages table (DB) | Embeddings (vector) | Local files + DB |
| L1 Atomic | Tagged facts in topics/ | Core memory slots | Vector chunks | Structured cards |
| L2 Scenario | Daily notes + area files | Extended memory (DB) | None explicit | Contextual archives |
| L3 Personality | profile.md (hand-maintained) | System prompt | None explicit | Auto-extracted |
| Compression | Agent-as-compressor | External process | None | Scheduled trigger |
| Conflict handling | Explicit arbitration | Silent overwrite | None | Partial |
| Verifiability | Git + append-only log | DB audit log | None | DB audit log |
