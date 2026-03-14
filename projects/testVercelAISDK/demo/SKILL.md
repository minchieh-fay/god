---
name: demo
description: 开发一个基于Vercel AI SDK的CLI应用，能通过Tool调用获取电脑磁盘空间并由大模型解答
---

## [Context]
当前需求背景是测试 Vercel AI SDK 中关于 LLM、Tools 以及 Skills 的联合调用链路。
涉及的项目模块为基于 Bun + TypeScript 构建的基础命令行（CLI）应用程序。应用需要在用户运行 `bun run dev` 后接收自然语言输入（如：“我这个电脑磁盘空间”），并在不修改代码（不硬编码磁盘数据）的前提下，通过工具自动获取宿主机的真实磁盘信息，交由大模型整理后输出结果。工程内必须包含详细的中文注释。

## [Blueprint]
方案设计：做什么、为什么这样做
- **交互层**：利用 Node.js 核心的 `readline/promises` 模块构建一个简单的终端问答循环（REPL），接收用户输入。
- **AI 引擎层**：引入 Vercel AI SDK (`ai`) 和对应的模型 Provider，通过设置 `maxSteps` 配合 `generateText` 启用 multi-step 工具调用能力。
- **工具层 (Tools)**：定义一个名为 `checkComputerDiskSpace` 的 tool。为避免硬编码，在 tool 内部使用原生 `child_process.exec` 调用 `df -h`（适配 mac OS）来动态读取真实磁盘占用率，并将标准输出返回给大模型。
- **代码规范**：全程使用详细的中文注释解释每一步的函数职责与变量用途。

需注意的 3 个易错点：
1. **Tool 定义格式验证失败**：Vercel AI SDK 强依赖 `zod` 进行工具入参验证。如果未正确使用 `zod` 定义参数 Schema（哪怕不需要参数也要返回特定的空对象或使用 z.object({})），会导致调用失败。
2. **多步执行未开启 (maxSteps)**：由于大模型首先需要判定应该调用 tool，获得 tool 返回后才能生成最终回答。如果没有在配置中设置 `maxSteps: 5`（或其他大于1的数字），调用将在触发 tool 后直接停止，不会把工具结果给到最终输出。
3. **平台兼容与硬编码混淆**：使用 shell 命令读取磁盘时命令执行可能会抛出异常（例如未对标准错误输出 `stderr` 做安全处理），直接抛错会导致整个 CLI 崩溃，需要针对命令执行添加 `try-catch` 捕获以将错误作为安全文本抛给 LLM。

## [Implementation Steps]
1. `package.json` 依赖配置
   - **文件位置**：`/Users/minchiehfay/Desktop/god/projects/testVercelAISDK/package.json` （如果不存在则初始化）
   - **具体操作**：安装所需依赖 `bun add ai @ai-sdk/openai zod`（假设采用 openai 接口，或根据实际 provider 调整）；在 `scripts` 里增加 `"dev": "bun run src/index.ts"`。
   
2. 创建主入口文件与交互循环
   - **文件位置**：`/Users/minchiehfay/Desktop/god/projects/testVercelAISDK/src/index.ts`
   - **具体操作**：引入 `readline/promises`，建立一个持续监听用户输入的 `while` 循环。

3. 实现 Tools 判断与调用
   - **文件位置**：`/Users/minchiehfay/Desktop/god/projects/testVercelAISDK/src/index.ts`
   - **具体操作**：
     导入 `z` (Zod) 和 `tool` (来自 `ai` 包)。定义 `tools` 对象，在其中添加 `checkDiskSpace`。
     在工具的 execute 逻辑中使用 Node.js 的 `util.promisify(require('child_process').exec)` 执行 `df -h /` 命令，并返回输出字符串。注意用中文进行大量注释说明工具用意。禁止写入任何写死空间的假数据。

4. 串联大模型与流式输出
   - **文件位置**：`/Users/minchiehfay/Desktop/god/projects/testVercelAISDK/src/index.ts`
   - **具体操作**：接收用户的终端输入，作为 `prompt` 传递给 `generateText` 函数。配置所需模型参数，并传入上方写好的 `tools` 以及设置 `maxSteps: 3` 以支持代理流。最终将大模型的 `text` 打印在终端，并等待下一轮提问。
