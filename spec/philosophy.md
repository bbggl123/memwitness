# OptMem Design Philosophy

> Why files, not databases? Why Markdown, not structured data? Why does the Agent itself do the compressing?

---

## 1. The Core Insight: Memory Is a Coordination Problem, Not a Storage Problem

Most AI memory systems solve the **storage** problem: "how do I put this into a vector database?"

OptMem solves the **coordination** problem: "how do I make sure the Agent and the user agree on what was said, what matters, and what changed?"

A vector database gives you answers. It doesn't give you accountability. When an Agent says "I remember you like Three.js projects", there's no way to verify whether the user actually said that, or whether the Agent is conflating it with something else.

OptMem gives you a paper trail. Every fact has a source. Every contradiction is recorded. Every compression is done by the Agent itself, with the raw log preserved.

## 2. Files Over Databases

### Why this choice?

| Factor | Files | Database |
|---|---|---|
| **Setup cost** | Zero — copy files | Minutes to hours (install, configure, migrate) |
| **Human audit** | `cat file.md` | `SELECT * FROM memory WHERE ...` (schema-dependent) |
| **Git integration** | Native — version history, diff, rollback | No — DB state is opaque to git |
| **Framework coupling** | None — any process can read/write text | High — requires specific drivers/ORMs |
| **Portability** | Drop files on any machine, any OS | Migration scripts, connection strings |
| **Recoverability** | Restore from git or backup | Recovery tooling, point-in-time restore |

### When NOT to use OptMem

OptMem is not a replacement for a database in all scenarios:

- **High-frequency writes** (thousands per second): files can't handle this
- **Multi-agent concurrent writes** without coordination: file locking is primitive
- **Sub-50ms retrieval latency requirements**: file reads are orders of magnitude slower

OptMem targets the vast majority of personal/collaborative Agent setups, where write frequency is low (once per session or less) and human readability matters more than raw speed.

## 3. Append-Only Logs as the Source of Truth

The `memory/log/` directory follows a single, inviolable rule:

> **Never edit. Only append.**

This means:

1. Every interaction, decision, and observation is recorded as a timestamped line
2. Corrections are written as new entries tagged `[override]`, not by editing the old entry
3. Higher-level memory files (`topics/`, `profile.md`) are **derivatives** — they can be reconstructed from the log at any time

This property gives OptMem something no vector database can match: **verifiable provenance**. If the Agent ever says "I remember this", you can check the log and confirm.

## 4. The Agent as Compressor

Most memory systems delegate summarization to an external process (a cron job, a background worker, a scheduled function).

OptMem has the **Agent itself** perform compression:

- **Reason**: The Agent has the best understanding of what matters in its own context. An external process doesn't know which facts are relevant, which are noise, and which should trigger follow-up action
- **Trigger**: When daily notes exceed a threshold (e.g., 500 lines), the Agent compresses them into a summary
- **Format**: `nap <date-range> "<compressed summary>"` — a single line that preserves the gist while freeing up context space
- **Recoverability**: The compressed summary can always be expanded by reading the original log

This makes the Agent an active participant in memory management, not just a consumer.

## 5. The [stated] Label System

Every fact stored in OptMem is labeled with its epistemic status:

| Tag | Meaning | Example |
|---|---|---|
| `[stated]` | The user explicitly said this | `[stated] prefers dark mode` |
| `[observed]` | The Agent observed this in behavior | `[observed] opens the terminal first thing` |
| `[inferred]` | The Agent deduced this from other facts | `[inferred] likely a software engineer` |

This is not just metadata — it's a **truth guarantee**. The user can always distinguish what they said from what the Agent guessed.

## 6. Privacy by Design, Not by Afterthought

Privacy is not a feature. It's a constraint on what can be stored at all.

OptMem defines **absolute exclusions** — categories that are never written to memory, even if the user explicitly offers them:

- Health data, medical conditions, mental health diagnoses
- Religion, nationality, ethnicity
- Political beliefs, sexual orientation, gender identity
- Personal finances, government IDs, credit card numbers
- Home addresses, personal phone numbers
- Criminal history, abuse experiences
- Psychological test results (MBTI, Enneagram, etc.)

**The test**: "If a colleague saw this line in the memory settings page, would the user be uncomfortable?" If yes, don't write it.

## 7. Conflict Arbitration as a Feature, Not a Bug

In most systems, a new memory silently overwrites an old one. This is dangerous:

- The user said "I like Python" last month, but "I'm switching to Rust" this week. If the old memory is overwritten, the Agent can never explain the transition.
- The Agent misremembered something. If it silently corrects itself, there's no audit trail.

OptMem's rule is simple: **never silently overwrite**. When a contradiction is detected:

1. Both the old and new values are preserved, with timestamps
2. A conflict record is written to `memory/conflict-records.md`
3. The Agent notes the conflict in the next interaction, seeking confirmation if needed

This turns conflicts into **learning opportunities** rather than data loss.
