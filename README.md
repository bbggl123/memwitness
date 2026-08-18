# MemWitness — AI Agent 的开放持久化记忆方案

> 一种基于文件的零依赖记忆模式，让任意 AI Agent 的会话都能变成可验证、可压缩、可自我进化的知识库。

> **🌐 语言切换**：[English](README-en.md) | 中文

---

## 问题

每个 AI Agent 都有记忆问题：

- **上下文窗口有限** —— 对话会被截断，历史会丢失
- **向量数据库不透明** —— embedding 能给答案但不给证据；你无法验证、编辑或审计
- **现有框架太重** —— MemGPT 需要数据库，LangMem 需要 Python，Redis Memory 需要基础设施。没有一个能"开箱即用"
- **无可追溯性** —— 当 Agent 说"我记得你说过 X"时，你无从检查它是真的、是幻觉、还是来自别的会话

## 解决方案

MemWitness 不是一个代码库，而是一种**设计模式 + 文件约定**，任何 Agent 框架都可以采用：

- **纯文件** —— 全部用 Markdown，Git 可追踪，人类可读，兼容 Obsidian。零运行时依赖
- **追加式日志** —— `memory/log/YYYY-MM-DD.md`，只增不改。任何高层记忆事实都可以从原始日志重建。这就是证据链
- **Agent 即压缩器** —— Agent 自己定期把原始日志总结为结构化记忆，而不是依赖外部数据库进程
- **[stated] 标签** —— 每条存储的事实都标注为用户所说、系统观察到、或 Agent 推断，任何人都能区分事实与推测
- **冲突仲裁** —— 当新记忆与旧记忆矛盾时，两者都保留时间戳，并写入仲裁记录。绝不静默覆盖
- **自我进化闭环** —— 错误、用户纠正、改进措施以结构化格式追踪，让 Agent 持续变好

## 为什么用文件，不用数据库？

属性 | MemWitness（文件） | MemGPT（数据库） | LangMem（Python）
--- | --- | --- | ---
运行时依赖 | **无** | PostgreSQL + Python | Python 3.10+
人类可读 | **是** | 否（SQL/JSON） | 部分
Git 版本化 | **是** | 否 | 否
跨框架 | **任意** | 仅 Python | 仅 Python
可验证证据 | **是（追加式）** | 否 | 否
部署时间 | **秒级**（复制文件） | 分钟+（建库） | 分钟（pip install）

**接受的取舍**：MemWitness 不给你毫秒级检索或自动 embedding 搜索。你得到的是一个可信任、可审计、随时可迁移的系统。对于大多数 Agent 用途（个人助手、编程辅助、研究伙伴），这是正确的取舍。

## 仓库结构

```
memwitness/
├── README.md                          ← 中文版（你在这里）
├── README-en.md                       ← English version
├── spec/                              ← 设计哲学
│   ├── philosophy.md                  ← 为什么用文件而不是数据库
│   ├── layered-architecture.md        ← 四层金字塔架构
│   └── comparison.md                  ← 与 MemGPT / LangMem 等的对比
├── rules/                             ← Agent 行为规则
│   ├── memory-behavior-rules.md       ← 读取 / 写入 / 仲裁规则
│   ├── stated-label-system.md         ← [stated] 标签体系
│   ├── conflict-arbitration.md        ← 矛盾处理机制
│   ├── self-improvement-loop.md       ← 错误 → 纠正 → 改进
│   └── privacy-redlines.md            ← 什么绝对不能存储
├── templates/                         ← 开箱可用的模板骨架
│   ├── skeleton/                      ← 克隆后填空即可
│   └── custom-configs/                ← 不同场景的变体
├── examples/                          ← 真实使用示例
│   ├── example-profile.md             ← 匿名化用户画像
│   ├── example-log.md                 ← 匿名化原始日志
│   └── example-conflict-resolution.md ← 真实仲裁案例
└── guides/                            ← 采用指南
    └── adopters.md                    ← 如何集成到各种框架
```

## 快速开始（30 秒）

```bash
# 1. 克隆骨架模板
git clone https://github.com/bbggl123/memwitness.git
cd memwitness/templates/skeleton

# 2. 填写模板文件
#    - MEMORY.md        ← 导航索引
#    - profile.md       ← 关于用户的稳定事实
#    - preferences.md   ← Agent 应该如何表现
#    - topics/*.md      ← 领域知识
#    - action-items.md  ← 待办追踪
#    - log/YYYY-MM-DD.md ← 追加式原始日志

# 3. 把目录给到你的 Agent（任何框架）
#    Agent 读取 rules/ 中的规则，遵循 MEMORY.md 的约定，
#    在与用户交互时自动写入记忆。
```

## 核心原则

1. **人类可验证优先** —— 每条记忆事实都应能追溯到来源（日志条目、用户消息、工具结果）
2. **绝不静默覆盖** —— 新信息与旧信息矛盾时，两者都保留并标注时间戳
3. **追加式原始日志** —— `log/` 目录是事实来源；高层文件是衍生物，可随时重建
4. **Agent 即压缩器** —— Agent 自己完成总结，不依赖外部进程
5. **隐私默认保护** —— 敏感类别（健康、宗教、政治、财务、生物特征）绝不存储，即使使用者主动提供

## 框架无关

MemWitness 最初在 [QwenPaw](https://github.com/agentscope-ai/QwenPaw) 上开发和验证，但此模式与框架无关。`rules/` 目录中的 Agent 行为指令可适配到：

- Claude Code / Opus
- OpenAI Codex
- Anthropic Computer Use
- 自定义 Agent 框架（LangChain、CrewAI、AutoGen 等）
- 任何能读写 Markdown 文件的系统

详见 [`guides/adopters.md`](https://github.com/bbggl123/memwitness/blob/main/guides/adopters.md)。

## 许可证

MIT —— 随便用，随便改，随便发布。
