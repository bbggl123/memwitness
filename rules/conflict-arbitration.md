# Conflict Arbitration

> When new information contradicts an existing memory fact, OptMem never silently overwrites. This document defines the conflict arbitration process.

---

## The Core Rule

> **Never silently overwrite.**

If a new memory fact contradicts an existing one, both must be preserved with timestamps, and a conflict record must be written. The Agent should then note the conflict in the next interaction and seek confirmation if needed.

---

## Step 1: Detection

**When writing a new memory fact, always check**: Does this contradict anything already stored?

**How to check**:
1. Read the relevant memory file (e.g., `preferences.md` for preference changes)
2. Search for entries on the same topic
3. Compare the new fact with existing facts

**Conflict indicators**:
- Direct contradiction ("User likes Python" vs "User prefers Rust")
- Superseded preference ("Use Model A" → "Use Model B")
- Corrected fact (Agent misremembered something, user corrected it)

---

## Step 2: Classification

Classify the conflict into one of three types:

### Type A: Iterative Change
The user is intentionally updating their preference or decision.

**Example**: "I was using Model A, but now I want to switch to Model B."

**Handling**:
- Preserve the old value with a timestamp
- Record the new value
- Log in `conflict-records.md` as a normal iteration
- No need to seek confirmation — the user made the change

### Type B: Correction
The Agent had an error, and the user is correcting it.

**Example**: Agent wrote "User uses Model X" but user says "No, I never used Model X, I use Model Y."

**Handling**:
- Mark the old entry as `[override]` in the log
- Write the correct value as `[stated]`
- Log in `conflict-records.md` with the correction details
- Log as a self-improvement entry (error pattern + correction + prevention)

### Type C: Genuine Contradiction
Two valid but different statements from the user on the same topic.

**Example**: In one session, user says "I prefer Python." In another, user says "I prefer Go." No indication that one supersedes the other.

**Handling**:
- Preserve both statements with timestamps
- Log in `conflict-records.md` with a ⚠️ marker
- Flag for manual resolution — the Agent should mention it in the next interaction
- Do not resolve on the Agent's own — wait for user clarification

---

## Step 3: Recording

Write a conflict record to `memory/conflict-records.md`:

```markdown
## C001 · [Brief Title]

**Discovered**: YYYY-MM-DD
**Type**: Iterative Change / Correction / Genuine Contradiction

| # | Time | Content | Source |
|---|------|---------|--------|
| 1 | YYYY-MM-DD | Old value | Log reference |
| 2 | YYYY-MM-DD | New value | Log reference |

**Resolution**: [How it was handled]
```

### Conflict Record Examples

#### Iterative Change
```markdown
## C001 · LLM Provider Change

**Discovered**: 2026-08-04
**Type**: Iterative Change

| # | Time | Content | Source |
|---|------|---------|--------|
| 1 | 2026-07-24 | Used Model A | Log 2026-07-24 |
| 2 | 2026-07-29 | Switched to Model B | Log 2026-07-29 |

**Resolution**: Normal iteration. Old value preserved as history.
```

#### Correction
```markdown
## C002 · API Parameter Name

**Discovered**: 2026-08-03
**Type**: Correction

**Old**: Used `images` parameter for OCR API
**New**: Correct parameter is `image_file`
**Resolution**: Fixed. Error logged in self-improvement.md.
```

#### Genuine Contradiction
```markdown
## C003 · Preferred Programming Language

**Discovered**: 2026-08-05
**Type**: Genuine Contradiction ⚠️

| # | Time | Content | Source |
|---|------|---------|--------|
| 1 | 2026-07-20 | "I prefer Python" | Log 2026-07-20 |
| 2 | 2026-08-03 | "I prefer Go" | Log 2026-08-03 |

**Resolution**: ⚠️ Awaiting user clarification. Mentioned in conversation on 2026-08-05.
```

---

## Step 4: Resolution

### For Iterative Changes
- Nothing further needed. The conflict record serves as an audit trail.

### For Corrections
- Update the affected memory file (replace old value with corrected value)
- Log the correction in `self-improvement.md` as an error pattern
- The conflict record serves as an audit trail

### For Genuine Contradictions
- Flag in the next conversation: "I noticed something — last month you mentioned X, but this week you said Y. Can you clarify which one is current?"
- Once the user clarifies, classify as Type A (if one supersedes) or keep as Type C (if both are valid in different contexts)

---

## Best Practices

1. **Check before writing** — always read the target file before adding a new entry
2. **Err on the side of recording** — if you're unsure whether something is a conflict, record it
3. **Don't be afraid of conflicts** — they're features, not bugs. They show the system is working
4. **Keep conflict records concise** — the goal is traceability, not verbosity
5. **Review periodically** — check `conflict-records.md` monthly for unresolved contradictions
