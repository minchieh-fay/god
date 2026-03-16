# Sample 示例工程

这是一个演示用的示例工程，展示完整的使用流程。

---

## 使用流程

**Step 1**：在 `projects/` 下新建或找到已有项目目录，如 `projects/my-project/`

**Step 2**：如果是新项目，让 Antigravity 帮你生成 `ARCH.md`（技术档案）；
如果项目已有源码，把源码路径告诉 Antigravity，它会自动分析并生成。
参考本目录下的 `ARCH.md` 了解 ARCH.md 的格式。

**Step 3**：在项目目录下（子目录随意）创建需求文件，参考 `add-mail.md` 的格式书写。

**Step 4**：将需求文件**拖拽到 Antigravity 聊天框**，发送"出施工图"或"分析这个需求"。

**Step 5**：Antigravity 会在需求文件同级的同名目录下生成 `SKILL.md`，参考 `add-mail/SKILL.md` 了解格式。

**Step 6**：将生成的 `{需求名}/SKILL.md` 整个目录复制到**目标工程**的 `.agent/skills/` 下，交给 Cursor / Trae 执行。

---

## 目录结构说明

```
sample/
├── README.md          # 本说明文件
├── ARCH.md            # 项目技术档案（示例）
├── add-mail.md        # 需求文件（示例）
└── add-mail/
    └── SKILL.md       # 产出施工图（示例）
```
