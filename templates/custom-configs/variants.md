# Custom Configurations

> Variants of the OptMem skeleton for different use cases.

---

## 1. Minimal Configuration (Quick Start)

For simple Agent setups where you want memory but don't need the full structure:

```
memory/
├── MEMORY.md          ← Simplified index (just the basics)
├── profile.md         ← User identity
├── preferences.md     ← Behavior rules
└── log/               ← Append-only log
    └── YYYY-MM-DD.md
```

**When to use**: Personal chat assistants, single-purpose Agents, short-lived sessions.

---

## 2. Full Configuration (Default)

The complete skeleton as provided in `templates/skeleton/`. Includes all four layers, conflict arbitration, and self-improvement tracking.

**When to use**: Long-running Agents, personal assistants with complex history, research companions.

---

## 3. Multi-User Configuration

For Agents serving multiple users:

```
memory/
├── MEMORY.md
├── users/
│   ├── user-alice/
│   │   ├── profile.md
│   │   ├── preferences.md
│   │   ├── topics/
│   │   └── log/
│   └── user-bob/
│       ├── profile.md
│       ├── preferences.md
│       ├── topics/
│       └── log/
└── shared/
    ├── topics/        ← Shared knowledge across all users
    └── areas/         ← Shared projects
```

**When to use**: Shared Agent instances, team workspaces, multi-tenant deployments.

**Note**: Each user's `profile.md` and `preferences.md` are session-scoped. The Agent must know which user it's talking to before reading memory.

---

## 4. Project-Only Configuration

For Agents focused on a single project, without personal memory:

```
memory/
├── MEMORY.md
├── profile.md         ← Minimal: just the project context
├── areas/
│   └── project.md     ← All project-related facts here
└── log/
    └── YYYY-MM-DD.md
```

**When to use**: CI/CD Agents, code review Agents, project-specific assistants.
