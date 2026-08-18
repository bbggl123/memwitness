# Memory Behavior Rules

> This document defines how an Agent should interact with the MemWitness file structure.
> These rules are designed to be framework-agnostic — any Agent that can read/write text files can follow them.

---

## Rule 1: Session Startup (Wake-Up)

**When**: Every time a new session begins.

**Do**:
1. Read `MEMORY.md` — the navigation index that describes the file layout
2. Read `profile.md` — who the user is (stable identity facts)
3. Read `preferences.md` — how the Agent should behave (behavioral instructions)
4. Read the latest daily note (`memory/YYYY-MM-DD.md`) for recent context
5. Read `action-items.md` to check for pending tasks
6. Optionally: search `topics/` for facts relevant to the current session

**Budget**: Keep startup reads under 3000 tokens. Prioritize profile + preferences + latest daily note. Defer deep topic reads until needed.

---

## Rule 2: During Interaction (Logging)

**When**: Every significant interaction.

**Do**:
1. **Log to L0** — append a timestamped entry to `memory/log/YYYY-MM-DD.md`:
   ```
   [HH:MM] [tag]: Content of the event
   ```
   Tags: `[stated]` (user said it), `[observed]` (Agent observed it), `[decided]` (user decided), `[learned]` (new knowledge acquired)
2. **Extract L1 facts** — if the user explicitly states something new, add it to the appropriate `topics/*.md` or `areas/*.md` file with the `[stated]` tag
3. **Check for conflicts** — before writing a new preference/decision, search existing memory for the same topic. If a contradiction exists, follow the conflict arbitration process (see `conflict-arbitration.md`)

**Don't**:
- Don't log trivial exchanges (e.g., "ok", "thanks", "got it")
- Don't log information that would expire (today's weather, a one-time meeting room number)
- Don't log anything on the privacy redlines list

---

## Rule 3: Writing New Memory

**When**: The user explicitly states a fact, makes a decision, or sets a preference.

**Do**:
1. **Check existing memory first** — read the relevant file before writing
2. **Use the correct tag** — `[stated]` for user's own words, `[observed]` for Agent observations
3. **Write to the right layer**:
   - Stable identity fact → `profile.md`
   - Behavioral preference → `preferences.md`
   - Domain knowledge → `topics/<domain>.md`
   - Project/ongoing work → `areas/<project>.md`
   - One-time event → `log/YYYY-MM-DD.md` (L0 only)
4. **Don't delete old entries** — append new ones. Old entries are historical record.

**Don't**:
- Don't write inferred conclusions — only what the user actually said or did
- Don't write forward-looking states ("next step", "TBD", "to be planned")
- Don't write research output (search results, prices, recommendations) — those aren't user facts
- Don't announce that you've written something — the action speaks for itself

---

## Rule 4: Writing — What Counts

### Write these:
- User's direct statements
- User's choices from multiple options (discard unchosen options)
- Rough shape of plans the user has agreed to
- Decisions the user has made

### Don't write these:
- Inferred conclusions ("likes X" → "probably likes X category")
- Research output (search results, prices, recommended places)
- Information enrichment ("Holton, MI" → don't add county info)
- Second-hand information ("I heard X is good")
- Agent's own suggestions/recommendations (even if the user accepted them)
- Things the user didn't object to but also didn't say (silence ≠ agreement)

---

## Rule 5: Session End (Compression)

**When**: A session ends, or when daily notes exceed ~500 lines.

**Do**:
1. **Write daily summary** — create/update `memory/YYYY-MM-DD.md` with a concise summary of the day's key events
2. **Check compression threshold** — if the daily note is very long (>500 lines), compress it:
   ```
   nap 2026-08-01~2026-08-03 "Compressed summary of events from Aug 1-3: [key decisions, new knowledge, important interactions]"
   ```
3. **Update area files** — if any ongoing project state changed, update `areas/*.md`
4. **Rarely: update L3** — only if a stable identity fact or core preference changed

---

## Rule 6: Reading Before Answering

**When**: Answering a question that might depend on user-specific information.

**Do**:
1. Check relevant memory files before answering
2. If memory search returns nothing, answer normally — don't claim to have no information unless you've actually searched
3. **Never cite memory sources** — don't say "according to my memory", "you previously said", or "based on your memories"

---

## Rule 7: Never Use These Phrases

When referencing memory in a response, never use:

| Don't say | Why |
|---|---|
| "I remember..." / "I recall..." | Suggests the Agent is a person |
| "According to my memory" / "Based on your memories" | Reveals the memory system as an implementation detail |
| "As we discussed..." | Only acceptable when the user explicitly asks about the memory system itself |

**Exception**: When the user directly asks about the memory system ("how does your memory work?"), it's fine to reference past discussions.

---

## Rule 8: Privacy Redlines

**Never write** the following categories, even if the user explicitly shares them:

- Race, ethnicity, nationality, religion
- Health data, medical conditions, mental health diagnoses
- Political beliefs, sexual orientation, gender identity
- Personal finances, government ID numbers
- Home addresses, personal phone numbers
- Criminal history, abuse experiences
- Psychological test results (MBTI, Enneagram, etc.)

**Test**: If a colleague saw this line in the memory settings page, would the user be uncomfortable? If yes, don't write it.

**Partial write**: If a sentence is partially sensitive, write only the non-sensitive part. Omit the sensitive part entirely — don't leave a placeholder.
