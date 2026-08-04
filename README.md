# OptMem — Open Persistent Memory for AI Agents

> A file-based, zero-dependency memory pattern that turns any AI Agent's session into a verifiable, compressible, self-improving knowledge base.

[![License](https://img.shield.io/badge/license-MIT-blue.svg)](https://github.com/bbggl123/optmem/blob/main/LICENSE)
[![Zero Dependencies](https://img.shield.io/badge/dependencies-0-brightgreen.svg)]()
[![Format](https://img.shield.io/badge/format-Markdown-orange.svg)]()

---

## The Problem

Every AI Agent has a memory problem:

- **Context windows are finite** — conversations get truncated, history is lost
- **Vector databases are opaque** — embeddings give answers but not evidence; you can't verify, edit, or audit them
- **Existing frameworks are heavy** — MemGPT needs a database, LangMem needs Python, Redis Memory needs infrastructure. None of them work "out of the box" for a simple Agent setup
- **No accountability** — when an Agent says "I remember you said X", there's no way to check if it's true, hallucinated, or from a different session

## The Solution

OptMem is not a code library. It's a **design pattern + file convention** that any Agent framework can adopt:

- **Pure files** — everything is Markdown. Git-tracked. Human-readable. Obsidian-compatible. Zero runtime dependencies
- **Append-only logs** — a `memory/log/YYYY-MM-DD.md` that is never edited. Every higher-level memory fact can be reconstructed from the raw log. This is the evidence chain
- **Agent as compressor** — the Agent itself periodically summarizes raw logs into structured memories, rather than relying on external DB processes
- **[stated] labels** — every stored fact is tagged as user-said, system-observed, or agent-inferred, so anyone can distinguish truth from speculation
- **Conflict arbitration** — when a new memory contradicts an old one, both are preserved with timestamps, and a record is written. Nothing is silently overwritten
- **Self-improvement loop** — errors, user corrections, and improvements are tracked in a structured format, making the Agent get better over time

## Why Files, Not Databases?

| Property | OptMem (Files) | MemGPT (DB) | LangMem (Python) |
|---|---|---|---|
| Runtime deps | **None** | PostgreSQL + Python | Python 3.10+ |
| Human readable | **Yes** | No (SQL/JSON) | Partial |
| Git-versionable | **Yes** | No | No |
| Cross-framework | **Any** | Python only | Python only |
| Verifiable evidence | **Yes (append-only)** | No | No |
| Deploy time | **Seconds** (copy files) | Minutes+ (DB setup) | Minutes (pip install) |

**Trade-off accepted**: OptMem does not give you sub-millisecond retrieval or automatic embedding search. You get a system you can trust, audit, and take anywhere. For most Agent use cases (personal assistants, coding helpers, research companions), that's the right trade.

## Repository Structure

```
optmem/
├── README.md                          ← You are here
├── spec/                              ← Design philosophy
│   ├── philosophy.md                  ← Why files, not databases
│   ├── layered-architecture.md        ← The four-layer pyramid
│   └── comparison.md                  ← vs MemGPT / LangMem / others
├── rules/                             ← Behavior rules for Agents
│   ├── memory-behavior-rules.md       ← Read / write / arbitrate
│   ├── stated-label-system.md         ← [stated] tag taxonomy
│   ├── conflict-arbitration.md        ← Handling contradictions
│   ├── self-improvement-loop.md       ← Error → correction → improvement
│   └── privacy-redlines.md            ← What must never be stored
├── templates/                         ← Ready-to-use skeletons
│   ├── skeleton/                      ← Clone and fill in
│   └── custom-configs/                ← Variants for different use cases
├── examples/                          ← Real-world usage examples
│   ├── example-profile.md             ← Anonymized profile
│   ├── example-log.md                 ← Anonymized raw log
│   └── example-conflict-resolution.md ← Real arbitration case
└── guides/                            ← Adoption guides
    └── adopters.md                    ← How to integrate with various frameworks
```

## Quick Start (30 seconds)

```bash
# 1. Clone the skeleton
git clone https://github.com/bbggl123/optmem.git
cd optmem/templates/skeleton

# 2. Fill in the template files
#    - MEMORY.md        ← your navigation index
#    - profile.md       ← stable facts about the user
#    - preferences.md   ← how the Agent should behave
#    - topics/*.md      ← domain-specific knowledge
#    - action-items.md  ← to-do tracking
#    - log/YYYY-MM-DD.md ← append-only raw log

# 3. Give the directory to your Agent (any framework)
#    The Agent reads the rules in rules/, follows the conventions in MEMORY.md,
#    and writes memory as it interacts with the user.
```

## Core Principles

1. **Human-verifiable first** — every memory fact should be traceable to a source (log entry, user message, tool result)
2. **Never silently overwrite** — when new information contradicts old, preserve both with timestamps
3. **Append-only raw logs** — the `log/` directory is the source of truth; higher-level files are derivatives that can be rebuilt
4. **Agent as compressor** — the Agent itself performs summarization, not an external process
5. **Privacy by default** — sensitive categories (health, religion, politics, finances, biometrics) are never stored, even if the user offers them

## Framework-Agnostic

OptMem was originally developed and validated on [QwenPaw](https://github.com/agentscope-ai/QwenPaw), but the pattern is framework-independent. The `rules/` directory contains Agent behavior instructions that can be adapted to:

- Claude Code / Opus
- OpenAI Codex
- Anthropic Computer Use
- Custom Agent frameworks (LangChain, CrewAI, AutoGen, etc.)
- Any system that can read/write Markdown files

See [`guides/adopters.md`](./guides/adopters.md) for integration guidance.

## License

MIT — use it, modify it, ship it.
