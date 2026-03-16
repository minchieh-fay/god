---
name: project-analysis
description: 分析一个未知的源代码目录并生成 ARCH.md。当用户要求分析项目、读懂代码结构、或在项目下还没有 ARCH.md 时使用。
---

# 项目深度分析 (Project Analysis)

## 目标
将一个未知的源代码目录转化为结构化的 `ARCH.md`。

## 步骤清单 (Step-by-Step)

1. **[探测]**
   识别核心配置文件（`go.mod`、`package.json`、`Cargo.toml`、`pyproject.toml` 等），确定技术栈和运行时版本。

2. **[目录结构扫描]**
   遍历完整目录树，对每个目录做以下分析：
   - 该目录的职责是什么（入口层 / 业务层 / 数据层 / 工具包 / 配置 / 测试 等）
   - 目录内有哪些典型文件，它们各自负责什么
   - 识别整体分层模式（MVC / Clean Architecture / 插件模式 / 平铺结构 等）

   输出格式示例：
   ```
   ~/
   ├── cmd/main.go          # 程序入口，初始化依赖并启动 HTTP server
   ├── internal/
   │   ├── handler/         # HTTP 处理层，负责请求解析与响应格式化
   │   ├── service/         # 业务逻辑层，核心规则在此
   │   └── repo/            # 数据访问层，封装所有 DB 操作
   ├── pkg/
   │   └── logger/          # 全局日志工具，基于 zap 封装
   └── config/              # 配置文件读取与结构体定义
   ```

3. **[提取]**
   - 找出项目使用的核心第三方库及其用途（如：用 Gorm 还是原生 SQL？用 Axios 还是 Fetch？）
   - 识别错误处理模式（如：Go 的 `if err != nil` 统一返回 / TS 的 try-catch / 自定义 error wrapper）
   - 识别日志记录方式（如：统一用 zap / 混用 fmt.Println）
   - 识别接口返回格式（如：统一 `{code, msg, data}` / 裸 JSON）

4. **[总结]**
   编写 `ARCH.md`，必须包含：
   - 完整目录结构说明（每个目录一行注释，说明职责）
   - 核心依赖库清单
   - 错误处理与日志规范
   - **禁止项**：通过观察代码，识别出作者从未采用的写法，列为禁止项

## 验收标准 (Verification)
- 目录结构说明精确到每个子目录，让没看过代码的人也能理解项目布局
- 生成的 ARCH.md 必须让用户评价："你确实看懂了我的代码"
