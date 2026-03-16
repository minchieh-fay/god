# Sample 示例工程

这是一个演示用的示例工程，展示完整的使用流程。

---

## 使用流程

**Step 1**：在 `projects/` 下新建一个与你项目同名的目录，如 `projects/my-project/`

**Step 2**：在项目目录下（子目录随意创建）写需求文件，参考 `add-mail.md` 的格式。
如果需求中涉及已有源码，在需求文件里写上源码的本地路径即可（参考 `add-mail.md` 的「源码路径」字段）。

**Step 3**：将需求文件**拖拽到 Antigravity 聊天框**，发送"出施工图"。

**Step 4**：Antigravity 会自动处理以下事项，无需你手动操作：
- 若项目下没有 `ARCH.md`，自动根据需求文件中的源码路径分析生成；源码路径为空则生成模板让你填写
- 分析完成后生成 `SKILL.md` 施工图

**Step 5**：在需求文件同级的同名目录下取回施工图，参考 `add-mail/SKILL.md` 了解格式。

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
