---
name: requirement-to-skill
description: 将用户的需求描述转化为可执行的 SKILL.md 施工图。当用户拖入 projects/{project_name}/{用户指定目录}/{需求名}.md 文件，或要求生成施工图时使用。
---

# 需求转化为施工图 (Demand Distillation)

## 目标
将模糊的需求描述转化为下游 AI（Cursor、Trae 等）可 100% 成功执行的 `SKILL.md`。

## 核心逻辑
1. **[上下文检索]**：向上查找 `ARCH.md`。如果没有找到，必须报错停止。
2. **[冲突检查]**：检查新需求是否违反了 `ARCH.md` 里的规范及「用户补充说明」模块。
3. **[任务原子化]**：
    - 任何一个 Task 涉及的代码修改量不应超过 50 行。
    - 如果超过，必须拆分为多个 Task，每个 Task 产出一个独立的 SKILL.md。
4. **[防御性提醒]**：针对当前项目技术栈，列出 3 个最容易出错的坑，写入 `[Blueprint]` 模块。

## 输出格式

产出的 `SKILL.md` 必须以标准 YAML frontmatter 开头：

```yaml
---
name: {需求名，小写加连字符}
description: {一句话说明该技能的用途和触发场景}
---
```

正文必须包含以下三个模块：

```markdown
## [Context]
当前需求背景、涉及的项目模块和文件范围。

## [Blueprint]
方案设计：做什么、为什么这样做、需注意的 3 个易错点。

## [Implementation Steps]
按时序排列的实施步骤，每步需指明：文件路径 + 函数/位置 + 具体操作。
逻辑复杂处附伪代码辅助说明。
```

## 输出路径

```
projects/{project_name}/{用户指定目录}/{需求名}/SKILL.md
```
