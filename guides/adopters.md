# Framework Adoption Guide

> OptMem is framework-agnostic. This guide explains how to integrate it with various Agent frameworks and platforms.

---

## Core Integration Principle

OptMem doesn't require any framework-specific code. The integration is purely about **giving the Agent access to the memory files** and **instructing it to follow the rules**.

For any framework, the integration steps are:

1. **Place the files** — put the `memory/` directory somewhere the Agent can read/write
2. **Inject the rules** — add the contents of `rules/` to the Agent's system prompt or instruction file
3. **Configure access** — ensure the Agent has read/write permission on the memory directory
4. **Start using** — the Agent reads on startup, writes during interaction, compresses at session end

---

## QwenPaw

### Integration
QwenPaw has native memory support that is compatible with the OptMem structure:

1. Place `memory/` in the Agent's workspace directory
2. QwenPaw's built-in `memory_search` tool works with the OptMem file structure
3. The `MEMORY.md` rules are compatible with QwenPaw's memory system
4. Add `rules/` content to the Agent's system prompt

### What's Different
- QwenPaw adds embedding-based semantic search on top of the file structure
- QwenPaw has `recall_history` for cross-session retrieval — OptMem uses file reading for the same purpose
- OptMem's append-only log complements QwenPaw's context compression

### Compatibility
✅ Fully compatible. OptMem is the portable subset of QwenPaw's memory system.

---

## Claude Code / Opus

### Integration
1. Place `memory/` in the project directory
2. Add a `.claude/CLAUDE.md` (or equivalent instruction file) that references the OptMem rules
3. Instruct Claude to:
   - Read `MEMORY.md` and `profile.md` at session start
   - Append to `memory/log/YYYY-MM-DD.md` during conversation
   - Update `topics/` and `areas/` as new facts emerge

### Sample CLAUDE.md Addition
```markdown
## Memory System

I use an OptMem-based memory system. Follow these rules:

1. **Startup**: Read `memory/MEMORY.md`, `memory/profile.md`, and `memory/preferences.md`
2. **During conversation**: Append significant events to `memory/log/YYYY-MM-DD.md` with `[HH:MM] [tag]: content` format
3. **New facts**: When the user states something new, add it to the appropriate `memory/topics/*.md` file with `[stated]` tag
4. **Conflicts**: Before writing new preferences, check existing memory. If contradictory, preserve both with timestamps
5. **Never store**: Health, religion, politics, personal finances, or other sensitive categories (see `memory/rules/privacy-redlines.md`)

Full rules: `memory/rules/memory-behavior-rules.md`
```

### Compatibility
✅ Compatible. Claude's file access + instruction system maps directly to OptMem.

---

## OpenAI Codex

### Integration
1. Place `memory/` in the project directory
2. Add instructions to the `.codex/` directory or system prompt
3. Instruct Codex to follow the OptMem behavior rules

### Sample System Prompt Addition
```
You have a memory system in the `memory/` directory. Follow these conventions:
- Read memory on startup (profile.md, preferences.md)
- Log significant events to memory/log/YYYY-MM-DD.md
- Tag all entries with [stated], [observed], or [inferred]
- Never silently overwrite conflicting information
- Never store sensitive personal data
```

### Compatibility
✅ Compatible with file-based approach. Codex's tool use enables file read/write.

---

## LangChain / LangGraph

### Integration
1. Create a custom memory class that wraps file I/O
2. Implement `load_memory_variables` to read OptMem files
3. Implement `save_context` to append to the log

### Sample Implementation Pattern
```python
class OptMemMemory(BaseMemory):
    def __init__(self, memory_path: str):
        self.memory_path = Path(memory_path)
    
    def load_memory_variables(self, inputs: dict) -> dict:
        profile = read_file(self.memory_path / "profile.md")
        preferences = read_file(self.memory_path / "preferences.md")
        return {"memory": f"Profile: {profile}\nPreferences: {preferences}"}
    
    def save_context(self, inputs: dict, outputs: dict):
        timestamp = datetime.now().strftime("%H:%M")
        entry = f"[{timestamp}] [stated]: {format_context(inputs, outputs)}"
        append_to_log(self.memory_path / "log", entry)
```

### Compatibility
⚠️ Requires custom integration code. LangChain's built-in memory (vector store, etc.) is different from OptMem's file approach.

---

## CrewAI

### Integration
1. Use OptMem files as shared context between crew members
2. Each Agent in the crew reads the same memory files
3. Use `task_callback` to write to the log after each task

### Compatibility
⚠️ Works as shared context. CrewAI's native memory is different but OptMem can supplement it.

---

## AutoGen

### Integration
1. Place `memory/` in the shared workspace
2. Configure each Agent to read memory on initialization
3. Use file I/O for logging (AutoGen Agents have file access)

### Compatibility
⚠️ Works with file access. AutoGen's conversation state is separate from OptMem.

---

## Custom / In-House Frameworks

### Integration
1. Identify where the Agent reads its initial context
2. Add OptMem file reading to that initialization
3. Identify where the Agent can write files
4. Add OptMem logging to that write path
5. Add OptMem rules to the Agent's instruction set

### Compatibility
✅ Always compatible. OptMem is pure text files — any system with file I/O can use it.

---

## Quick Reference: Integration Checklist

| Step | What to do | Status |
|------|-----------|--------|
| 1 | Clone or copy the `templates/skeleton/` to your workspace | ☐ |
| 2 | Fill in `profile.md` and `preferences.md` | ☐ |
| 3 | Add `rules/` content to the Agent's system instructions | ☐ |
| 4 | Configure Agent to read memory files on startup | ☐ |
| 5 | Configure Agent to append to `log/YYYY-MM-DD.md` during interaction | ☐ |
| 6 | Configure Agent to write new facts to `topics/` or `areas/` | ☐ |
| 7 | Test: start a session and verify memory is read/written correctly | ☐ |
| 8 | (Optional) Add a periodic compression step at session end | ☐ |
