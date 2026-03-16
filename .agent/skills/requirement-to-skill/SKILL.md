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
按时序排列的实施步骤。

每个涉及新增或修改的函数，必须提供完整伪代码，格式如下：

~/xxx/xxx.go
func 函数名(参数 类型) 返回值 {
    // step1. 用中文描述这一步做什么
    // step2. 通过 xxx 函数获取 xxx
    // step3. 用 yyy 算法计算 xxx，注意 zzz 边界条件
}

要求：
- 函数名、参数名、返回值类型必须与目标工程的实际命名规范一致
- 步骤描述用中文，简洁说明意图，不写具体代码
- 每个 step 对应一个操作单元，粒度不超过 5 行逻辑
- 若函数内调用了其他新增函数，被调用函数也必须单独列出伪代码
```

## 路径写法规范

`[Implementation Steps]` 中所有文件路径必须使用 `~/` 表示目标工程根目录：
- ✅ `~/package.json`、`~/src/index.ts`、`~/internal/handler/user.go`
- ❌ 任何绝对路径（`/Users/...`）
- ❌ god 工作台自身的路径（`projects/xxx/...`）

## 输出路径

```
projects/{project_name}/{用户指定目录}/{需求名}/SKILL.md
```
