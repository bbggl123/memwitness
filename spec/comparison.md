# Comparison with Other Agent Memory Systems

> An honest comparison of MemWitness against the major alternatives in the AI memory space.

---

## MemGPT (Christopher Akidau)

| Dimension | MemWitness | MemGPT |
|---|---|---|
| **Form** | File convention + behavior rules | Python framework with SQLite/PostgreSQL |
| **Depends on** | Zero (text files) | Python 3.10+, DB driver, LLM API |
| **Setup time** | Seconds (copy files) | Minutes (pip install, DB setup, config) |
| **Core memory** | User-managed `profile.md` | Core memory slots (auto-managed) |
| **Extended memory** | `topics/` + `areas/` + daily notes | Database tables (auto-indexed) |
| **Compression** | Agent-as-compressor (manual trigger) | Auto-compression via LLM calls |
| **Conflict handling** | Explicit arbitration, never silent | Silent overwrite by default |
| **Human auditability** | Full — `cat file` shows everything | Partial — requires DB queries |
| **Cross-framework** | Any (just text files) | Python ecosystem only |
| **Git-versionable** | Yes | No (DB state) |
| **Best for** | Personal assistants, small teams, audit-sensitive use | Large-scale deployments, Python-first teams |

**Key trade-off**: MemGPT automates more (auto-compression, auto-indexing), but at the cost of transparency and portability. MemWitness puts the user in control.

---

## LangMem (LangChain ecosystem)

| Dimension | MemWitness | LangMem |
|---|---|---|
| **Form** | File convention | Python library |
| **Depends on** | Zero | Python 3.10+, LangChain, vector DB |
| **Storage** | Markdown files | Vector embeddings (Pinecone/Weaviate/etc.) |
| **Retrieval** | File read + Agent search | Semantic search via embeddings |
| **Human readability** | Full | Low (vectors are opaque) |
| **Verifiability** | Yes (append-only log) | No (embeddings can't be traced) |
| **Cross-framework** | Any | LangChain ecosystem |
| **Best for** | Transparency-critical use | LangChain-native projects |

**Key trade-off**: LangMem gives you fast semantic retrieval, but you can never verify whether a retrieved memory is accurate. MemWitness gives you verification, but retrieval is manual.

---

## TencentDB Agent Memory (Tencent Cloud / Datawhale)

| Dimension | MemWitness | TencentDB Agent Memory |
|---|---|---|
| **Form** | File convention | Design pattern + DB-backed implementation |
| **Layer model** | 4 layers (L0–L3) | 4 layers (L0–L3) |
| **L0** | Append-only Markdown logs | Local files + DB (verbatim) |
| **L1** | Tagged facts in `topics/` | Structured cards with priority scores |
| **L2** | Daily notes + area files | Contextual archives with heat values |
| **L3** | Hand-maintained `profile.md` | Auto-extracted from L0–L2 |
| **Heat values** | None (Agent decides recency) | Retrieval-hit counter |
| **Auto-compression** | Agent-as-compressor | Scheduled trigger (5 turns / 10 min) |
| **Conflict handling** | Explicit arbitration | Candidate recall + LLM judgment |
| **Privacy rules** | Explicit redlines | Not specified |
| **Best for** | Simplicity + control | Teams wanting automation |

**Key similarity**: Both use a 4-layer model with append-only L0. TencentDB's heat-value system and auto-extraction are interesting additions that MemWitness doesn't currently implement.

---

## Redis Memory (Redis AI)

| Dimension | MemWitness | Redis Memory |
|---|---|---|
| **Form** | File convention | Redis module |
| **Depends on** | Zero | Redis 7+, Python client |
| **Storage** | Markdown files | Redis keys (TTL-based expiry) |
| **TTL / expiry** | None (permanent by design) | Configurable per key |
| **Human readability** | Full | Requires `redis-cli` |
| **Best for** | Permanent, auditable memory | Ephemeral, high-speed memory |

**Key difference**: Redis Memory is designed for ephemeral, high-throughput scenarios. MemWitness is designed for permanent, verifiable memory. They solve different problems.

---

## QwenPaw Built-in Memory

| Dimension | MemWitness | QwenPaw native |
|---|---|---|
| **Form** | File convention | Integrated memory system (MEMORY.md + search) |
| **Search** | Manual file read | `memory_search` tool (embedding-based) |
| **Compression** | Agent-as-compressor | `context compressed` + `recall_history` |
| **Cross-framework** | Yes | QwenPaw only |
| **Best for** | Portable, framework-agnostic | QwenPaw-native deployments |

**Key relationship**: MemWitness originated from patterns developed in QwenPaw. The native QwenPaw memory adds embedding-based search on top of the MemWitness file structure. MemWitness is the portable subset.

---

## Decision Matrix: Which Should You Use?

| If you need... | Use |
|---|---|
| Zero setup, human-readable, Git-versionable | **MemWitness** |
| Auto-compression, auto-indexing, Python ecosystem | **MemGPT** |
| Semantic search, LangChain integration | **LangMem** |
| Heat values, scheduled compression | **TencentDB Agent Memory** |
| High-speed ephemeral memory | **Redis Memory** |
| QwenPaw-native with embedding search | **QwenPaw built-in** |
| A mix (file structure + embedding search) | **MemWitness + your framework's search tool** |

**Our recommendation**: Start with MemWitness. It's the simplest to adopt, the most transparent, and it works with any Agent framework. If you later need embedding search or auto-compression, you can layer those on top — the file structure doesn't change.
