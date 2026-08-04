---
name: MEMORY
description: Memory system navigation index — file structure, read/write rules, privacy redlines
read_when: Every session startup
---

# Memory System Navigation

> This is the entry point. User facts are in subfiles; this file contains only rules and navigation.

---

## File Structure

```
MEMORY.md                    ← You are here (navigation index)
memory/
├── profile.md               ← User identity (stable facts, <300 words)
├── preferences.md           ← How the Agent should behave
├── topics/                  ← Domain-specific structured facts
│   ├── learning.md          ← Learning preferences and records
│   ├── tools.md             ← Tool preferences and tech stack
│   └── self-improvement.md  ← Error patterns, corrections, improvements
├── areas/                   ← Ongoing projects and responsibilities
│   └── <project-name>.md    ← One file per active project
├── conflict-records.md      ← Conflict arbitration records
├── YYYY-MM-DD.md            ← Daily notes (auto-summarized)
├── action-items.md          ← Action item tracking
└── log/                     ← Append-only raw memory log
    ├── INDEX.md             ← Log index
    └── YYYY-MM-DD.md        ← Daily raw log entries (never edited)
```

---

## Reading Rules

1. Check relevant memory files before answering user-specific questions
2. Don't claim you have no information unless you've actually searched
3. Never cite memory sources in responses ("according to my memory", etc.)
4. Each stored fact must earn its place — it should change the substance of your answer
5. Don't use memory for generic technical questions — only for user-specific context
6. Format/style preferences (in preferences.md) always apply

---

## Writing Rules

### [stated] Tag (every entry must have a tag)

**Write these**:
- What the user directly said
- Choices the user made from options (discard unchosen options)
- Rough shape of plans the user agreed to

**Don't write these**:
- Inferred conclusions
- Forward-looking states ("next step", "TBD")
- Research output (search results, prices)
- Information enrichment (user says "X", don't add extra context)
- Second-hand information
- Your own suggestions/recommendations (even if the user accepted them)
- Things the user didn't object to but also didn't say (silence ≠ agreement)

### Write Timing

- Write in real-time during the conversation, don't wait until the end
- One clear statement is enough to write (no need for user to say "remember this")
- Check before writing — don't duplicate existing entries
- Don't announce that you've written something — the action speaks for itself
- If content will expire (today's weather, tomorrow's meeting room) → don't write

### Conflict Arbitration (before writing new preferences/decisions)

- Search existing memory for the same topic
- If a contradiction exists, don't silently overwrite — preserve both with timestamps
- Record important conflicts in `conflict-records.md`

---

## Privacy Redlines

**Never write**, even if the user explicitly shares:
- Race, ethnicity, nationality, religion
- Health data, medical conditions, mental health diagnoses
- Political beliefs, sexual orientation, gender identity
- Personal finances, government ID numbers
- Home addresses, personal phone numbers
- Criminal history, abuse experiences
- Psychological test results (MBTI, Enneagram, etc.)

**Test**: If a colleague saw this line in the memory settings page, would the user be uncomfortable? If yes, don't write it.

---

## Prohibited Phrases

Never use these when referencing memory in responses:
- "I remember", "I recall", "From my memory"
- "Based on your memories", "According to my knowledge"

**Exception**: When the user directly asks about the memory system itself.

---

## Size Limits

- `profile.md` < 300 words
- Other files: keep concise, edit/compress rather than append endlessly
- Prioritize quality over quantity
