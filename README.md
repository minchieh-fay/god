# God — Antigravity 需求分析工作台

这个工程是 [Google Antigravity](https://antigravity.google/) 的工作目录。

Antigravity 在这里扮演**系统分析师**的角色：接收你的需求，分析项目背景，产出供下游 AI（Cursor、Trae 等）直接执行的 `SKILL.md` 施工图。

> **它不写代码，只出施工图。** 代码由你选择的开发 AI 拿着施工图去实现。

---

## 快速开始

**推荐先阅读 [`projects/sample/`](./projects/sample/README.md)，里面有完整的使用示例：**

- [`projects/sample/ARCH.md`](./projects/sample/ARCH.md)：项目技术档案的格式参考
- [`projects/sample/add-mail.md`](./projects/sample/add-mail.md)：需求文件的写法参考
- [`projects/sample/add-mail/SKILL.md`](./projects/sample/add-mail/SKILL.md)：产出施工图的格式参考

---

## 使用流程

**Step 1**：用 Antigravity 打开本目录

**Step 2**：在 `projects/` 下为你的项目新建一个目录（如 `projects/my-project/`）

**Step 3**：在项目目录下（子目录自由创建）写需求文件，参考 `projects/sample/add-mail.md` 的格式

**Step 4**：将需求文件**拖入 Antigravity 聊天框**，发送"出施工图"

**Step 5**：Antigravity 生成 `SKILL.md` 后，将整个 `{需求名}/` 目录复制到目标工程的 `.agent/skills/`，交给 Cursor / Trae 执行

---

## 目录结构

```
god/
├── projects/
│   ├── sample/                  # 示例工程（格式参考）
│   │   ├── README.md
│   │   ├── ARCH.md              # 项目技术档案示例
│   │   ├── add-mail.md          # 需求文件示例
│   │   └── add-mail/SKILL.md    # 施工图示例
│   └── {your-project}/          # 你的项目（本地保留，不上传）
│       ├── ARCH.md
│       ├── MEMORY.md            # Antigravity 自动维护的项目记忆
│       └── {自定义目录}/
│           ├── {需求名}.md
│           └── {需求名}/SKILL.md
│
├── MEMORY.md                    # Antigravity 工作流级记忆（自动维护）
├── AGENTS.md                    # Antigravity 规则入口
├── .agent/
│   ├── rules/                   # Antigravity 工作规则
│   └── skills/                  # Antigravity 内置技能
└── .gemini/
    └── GEMINI.md                # Antigravity 自动加载配置
```

---

## 如果你用 Cursor 或 Trae

本工程的规则和技能目录对应关系：

| 内容 | Antigravity | Cursor | Trae |
|------|-------------|--------|------|
| 规则 | `.agent/rules/` | `.cursor/rules/` | `.trae/rules/` |
| 技能 | `.agent/skills/` | `~/.cursor/skills/` | `~/.trae/skills/` |
| 自动加载 | `.gemini/GEMINI.md` | `.cursorrules` | `AGENTS.md` |

将对应目录下的文件复制到目标 IDE 的规范路径即可。
