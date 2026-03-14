# God — Antigravity 需求分析工作台

这个工程是 [Google Antigravity](https://antigravity.google/) 的工作目录。

Antigravity 在这里扮演系统分析师的角色：接收你的需求，分析项目背景，产出供下游 AI（Cursor、Trae 等）直接执行的 `SKILL.md` 施工图。

---

## 使用方式

1. 用 Antigravity 打开本目录
2. 在 `projects/{项目名}/` 下自行创建目录和需求文件 `{需求名}.md`（目录结构自由）
3. 将需求文件拖入 Antigravity 聊天框
4. 最终在 `projects/{项目名}/{你的目录}/{需求名}/SKILL.md` 得到施工图
5. 把施工图交给 Cursor 或 Trae 执行

---

## 目录结构

```
god/
├── projects/               # 所有项目
│   └── {project_name}/
│       ├── ARCH.md         # 项目技术档案（由 Antigravity 分析源码生成）
│       ├── MEMORY.md       # Antigravity 的项目级记忆（自动维护）
│       └── {用户自定义目录}/
│           ├── {需求名}.md          # 需求描述（用户拖入聊天框触发分析）
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
