# [stated] Label System

> Every fact in OptMem carries an epistemic tag. This document defines the tag taxonomy, when to use each tag, and why it matters.

---

## The Four Tags

### `[stated]` — The User Explicitly Said This

**Use when**: The user directly uttered the statement in conversation.

**Examples**:
```
- [stated] Prefers Python for backend development
- [stated] Wants the AI assistant to use colloquial language
- [stated] Is preparing for an AI competition (hackathon)
```

**Rules**:
- Must be a direct quote or faithful paraphrase of what the user said
- If the user chose from options, record the choice and discard unchosen options
- Do not tag agent-generated content as `[stated]`

---

### `[observed]` — The Agent Observed This in Behavior

**Use when**: The Agent noticed a pattern or behavior through interaction, but the user didn't explicitly state it.

**Examples**:
```
- [observed] User opens terminal before starting coding tasks
- [observed] User tends to work late at night
- [observed] User reviews AI output carefully before accepting
```

**Rules**:
- Must be based on actual observed behavior, not speculation
- Less authoritative than `[stated]` — the user can override it
- Use sparingly — only for patterns that have been observed multiple times

---

### `[inferred]` — The Agent Deduced This from Other Facts

**Use when**: The Agent has combined multiple facts to draw a reasonable conclusion.

**Examples**:
```
- [inferred] Likely has 3-5 years of software engineering experience (based on tools used and project complexity)
- [inferred] Prefers CLI tools over GUI (based on tool choices)
```

**Rules**:
- Must be clearly labeled as inference — never present as fact
- The user should be informed when an inference is made
- If the user corrects an inference, replace it with `[stated]`
- Inferences are the least authoritative — treat as hypotheses until confirmed

---

### `[decided]` — The User Made a Decision

**Use when**: The user has made an explicit choice between alternatives, or committed to a course of action.

**Examples**:
```
- [decided] Chose FastAPI over Django for the new project
- [decided] Switched from model A to model B (iteration, not conflict)
```

**Rules**:
- Decisions can change — if the user makes a new decision, record both with timestamps
- Don't confuse `[decided]` with `[stated]` — a statement of preference is `[stated]`, a concrete choice is `[decided]`

---

### `[learned]` — New Knowledge Was Acquired

**Use when**: The user or Agent learned something new (e.g., from reading an article, completing a tutorial).

**Examples**:
```
- [learned] Studied Three.js Scene/Camera/Renderer basics
- [learned] Learned about the OptMem layered architecture from TencentDB comparison
```

**Rules**:
- Tag the knowledge, not the act of learning
- `[learned]` entries go in L0 (raw log) primarily; knowledge facts go in L1 (topics)

---

## Why Tags Matter

### 1. Truth Guarantee
The user can always distinguish what they said (`[stated]`) from what the Agent guessed (`[inferred]`). This is the core trust property of OptMem.

### 2. Conflict Resolution
When two facts contradict, the tag tells you which one has higher authority:
- `[stated]` > `[inferred]` (user's word trumps agent's guess)
- `[observed]` > `[inferred]` (evidence trumps speculation)
- `[decided]` > `[stated]` (a concrete choice overrides a general preference)

### 3. Compression Safety
When compressing L0 logs into L1 summaries, tags help the Agent decide what to preserve:
- `[stated]` entries: always preserve
- `[decided]` entries: always preserve
- `[learned]` entries: preserve key concepts, discard details
- `[inferred]` entries: preserve only if confirmed by later evidence

### 4. Audit Trail
Any human reviewer can trace a memory fact back to its source:
```
This fact in profile.md is tagged [stated]
    → Found in log/2026-07-28.md as [stated] entry
        → Original user message timestamped at 14:32
```

## Tag Hierarchy (Authority)

```
[decided]  ← highest authority (concrete choice)
    |
[stated]   ← high authority (user's own words)
    |
[observed] ← medium authority (evidence-based)
    |
[inferred] ← lowest authority (speculation)
```

When compressing or summarizing, higher-authority tags get priority. Lower-authority tags can be dropped if space is limited.

## Common Mistakes

| Mistake | Correct Approach |
|---|---|
| Tagging everything as `[stated]` | Only tag as `[stated]` if the user actually said it |
| Tagging Agent observations as `[stated]` | Use `[observed]` for behavior the Agent noticed |
| Forgetting to tag at all | Every fact must have a tag |
| Using `[inferred]` without evidence | Only infer from multiple corroborating facts |
| Treating `[inferred]` as fact | Always present inferences as hypotheses |
