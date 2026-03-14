---
name: project-analysis
description: 分析一个未知的源代码目录并生成 ARCH.md。当用户要求分析项目、读懂代码结构、或在项目下还没有 ARCH.md 时使用。
---

# 项目深度分析 (Project Analysis)

## 目标
将一个未知的源代码目录转化为结构化的 `ARCH.md`。

## 步骤清单 (Step-by-Step)
1. **[探测]**: 识别核心配置文件（go.mod, package.json, Cargo.toml等），确定技术栈原型。
2. **[建模]**: 扫描目录树，识别出项目的分层模式（如：MVC, Clean Architecture, 还是插件模式）。
3. **[提取]**: 
    - 找出项目常用的第三方库（如：是用 Gorm 还是直接用 SQL？是用 Axios 还是 Fetch？）。
    - 识别错误处理模式（如：Go 的 `if err != nil` 还是 TS 的 Try-Catch）。
4. **[总结]**: 编写 `ARCH.md`。必须包含"禁止项"，即：通过观察代码，发现作者避讳或从未采用的写法。

## 验收标准 (Verification)
- 生成的 ARCH.md 必须让用户评价："你确实看懂了我的代码"。
