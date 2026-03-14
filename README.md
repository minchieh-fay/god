# God — Antigravity 需求分析工作台

这个工程是 [Google Antigravity](https://antigravity.google/) 的工作目录。

Antigravity 在这里扮演系统分析师的角色：接收你的需求，分析项目背景，产出供下游 AI（Cursor、Trae 等）直接执行的 `SKILL.md` 施工图。

---

## 使用方式

1. 用 Antigravity 打开本目录
2. 告诉它你有什么需求，它会引导你完成后续步骤
3. 最终在 `projects/{项目名}/{日期}/{需求名}/SKILL.md` 得到施工图
4. 把施工图交给 Cursor 或 Trae 执行

---

## 目录结构

```
god/
├── projects/               # 所有项目
│   └── {project_name}/
│       ├── ARCH.md         # 项目技术档案（由 Antigravity 分析源码生成）
│       ├── MEMORY.md       # Antigravity 的项目级记忆（自动维护）
│       └── {YYYYMM}/{DD}/
│           ├── {需求名}.md          # 需求描述
│           └── {需求名}/SKILL.md    # 产出施工图
│
├── MEMORY.md               # Antigravity 的工作流级记忆（自动维护）
├── AGENTS.md               # Antigravity 规则入口
├── .agent/
│   ├── rules/              # Antigravity 工作规则
│   └── skills/             # Antigravity 技能
└── .gemini/
    └── GEMINI.md           # Antigravity 自动加载配置
```

---

## 如果你用 Cursor 或 Trae

本工程的规则和技能目录对应关系：

| 内容 | Antigravity | Cursor | Trae |
|------|-------------|--------|------|
| 规则 | `.agent/rules/` | `.cursor/rules/` | `.trae/rules/` |
| 技能 | `.agent/skills/` | `~/.cursor/skills/` | `~/.trae/skills/` |
| 自动加载 | `.gemini/GEMINI.md` | `.cursorrules` | `AGENTS.md` |

将对应目录下的文件复制或引用到目标 IDE 的规范路径即可。
