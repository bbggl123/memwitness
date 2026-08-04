---
name: conflict-resolution-example
description: Example conflict arbitration records (anonymized)
sources: [example]
---

# Example Conflict Records

> Fully fabricated examples for demonstration purposes.
> Shows the format of conflict arbitration records.

---

## C001 · Language Preference Change

**Discovered**: 2026-08-15
**Type**: Iterative Change

| # | Time | Content | Source |
|---|------|---------|--------|
| 1 | 2026-07-20 | "I prefer Python for backend development" | Log 2026-07-20 |
| 2 | 2026-08-15 | "I prefer Go for CLI tools" | Log 2026-08-15 |

**Resolution**: Normal iteration. Old value preserved as historical preference. Not a contradiction — the user has different preferences for different contexts (Python for backend, Go for CLI).

---

## C002 · Tool Correction

**Discovered**: 2026-08-16
**Type**: Correction

**Old**: Memory recorded "User uses Tool X for code formatting"
**New**: User corrected: "I never used Tool X, I use Tool Y"
**Root Cause**: Agent inferred Tool X usage from a conversation where the user was evaluating multiple tools

**Resolution**: 
- Old entry marked as `[override]` in log
- Correct value written as `[stated]`
- Error pattern logged: "Don't infer tool usage from evaluation conversations"
- Self-improvement entry added: "Only record tool preferences from explicit statements, not from comparative discussions"

---

## C003 · Architecture Decision Conflict

**Discovered**: 2026-08-18
**Type**: Genuine Contradiction ⚠️

| # | Time | Content | Source |
|---|------|---------|--------|
| 1 | 2026-08-10 | "Use monolithic architecture for simplicity" | Log 2026-08-10 |
| 2 | 2026-08-18 | "Split into microservices for scalability" | Log 2026-08-18 |

**Resolution**: ⚠️ Awaiting user clarification. 
- Possible explanations: (a) the second statement supersedes the first, (b) different projects have different requirements, (c) the user changed their mind
- Agent mentioned the conflict in conversation on 2026-08-18
- Resolution pending user response
