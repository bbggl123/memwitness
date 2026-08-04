# Self-Improvement Loop

> An Agent should not just store memory — it should learn from its mistakes and get better over time. This document defines the self-improvement loop.

---

## The Four-Quadrant Model

The self-improvement system tracks four types of entries, each serving a distinct purpose:

```
Error Patterns (E###)      →  What went wrong?
    ↓
User Corrections (C###)    →  How did the user fix it?
    ↓
Improvement Measures (I###)→  What was done to prevent recurrence?
    ↓
Success Patterns (S###)    →  What worked well and should be kept?
```

---

## E### — Error Pattern Records

**When**: An error occurs during task execution, or the user corrects a mistake.

**Format**:
```markdown
### E### · [Brief Title] (Date)
- **Symptom**: What happened
- **User Correction**: What the user said (verbatim or close paraphrase)
- **Improvement**: What was done to fix it
- **Status**: ✅ Fixed / ⚠️ Partial / ❌ Unresolved
```

**Example**:
```markdown
### E001 · Template Violation (2026-07-28)
- **Symptom**: Used custom output format instead of the required template
- **User Correction**: "I've told you many times to follow the template"
- **Improvement**: Added highest-priority rule in memory: check template before output
- **Status**: ✅ Fixed
```

---

## C### — User Correction Patterns

**When**: The user repeatedly corrects the Agent's behavior, revealing a pattern.

**Format**:
```markdown
### C### · "[Correction Type]"
- **Scenario**: When does this correction happen?
- **Correction**: What does the user say?
- **Response**: How should the Agent respond next time?
```

**Example**:
```markdown
### C001 · "Figure it out yourself" type
- **Scenario**: Agent reports "I can't do this" when hitting a technical bottleneck
- **Correction**: User asks the Agent to try alternatives before reporting failure
- **Response**: Try at least 3 alternative approaches before reporting; include what was tried and the results
```

---

## I### — Improvement Measures

**When**: A concrete action is taken to prevent a class of errors.

**Format**:
```markdown
| # | Measure | Trigger | How to Execute |
|---|---------|---------|----------------|
| I001 | Check template before output | Learning task complete | Search memory for "template" |
| I002 | Deduplicate before storing | Knowledge base write | Search existing files |
```

**Rules**:
- Each improvement measure should be specific and actionable
- Include the trigger condition (when to apply)
- Include the execution method (how to apply)
- Update this table as new measures are added

---

## S### — Success Patterns

**When**: A behavior or pattern proves effective and should be preserved.

**Format**:
```markdown
### S### · [Pattern Name]
- **Description**: What worked
- **Why it works**: The mechanism behind the success
- **When to apply**: Context where this pattern is most effective
```

**Example**:
```markdown
### S001 · Dual Write (Knowledge Base + Permanent Memory)
- **Description**: After learning, write to both the knowledge base and the permanent memory files
- **Why it works**: Knowledge base provides retrieval; permanent memory provides behavioral constraints
- **When to apply**: Every learning session
```

---

## The Loop in Action

```
1. Task starts
2. Error occurs (or user corrects behavior)
3. → Record in E### section
4. → Identify the correction pattern → Record in C### section
5. → Create improvement measure → Record in I### section
6. Next task starts
7. → Read E/C/I/S sections before beginning
8. → Apply improvement measures to avoid repeating errors
9. If improvement works → promote to S### section
```

---

## Self-Improvement File Structure

The self-improvement file should have this structure:

```markdown
# Self-Improvement Records

## 1. Error Pattern Records (E###)
### E001 · ...
### E002 · ...

## 2. User Correction Patterns (C###)
### C001 · ...
### C002 · ...

## 3. Improvement Measures
| # | Measure | Trigger | Execution |

## 4. Success Patterns (S###)
### S001 · ...
### S002 · ...
```

---

## Best Practices

1. **Record immediately** — don't wait until the end of the session. Record errors and corrections as they happen.
2. **Read before each task** — before starting a new task, read the self-improvement file to check for relevant error patterns.
3. **Don't repeat** — if you see an E### entry for a mistake you've made before, don't make it again.
4. **Promote successes** — if an improvement measure works consistently, move it to the Success Patterns section.
5. **Keep it concise** — each entry should be 3-5 lines. The goal is quick scanning, not exhaustive documentation.
6. **Review periodically** — monthly, review all E/C/I/S entries and remove resolved ones or promote improvements.
