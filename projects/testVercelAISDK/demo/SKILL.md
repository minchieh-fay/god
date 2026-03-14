# 需求: demo

## [Context]
背景:
用户需要在 `testVercelAISDK` 工程中开发一个简单的 CLI/TUI 应用，使用 Bun + TypeScript 进行开发。
该应用的主要功能是：用户运行 `bun run dev` 后，可以输入问题（如："我这个电脑磁盘空间"）。
应用会通过 Vercel AI SDK 调用大模型（LLM），同时结合 SDK 提供的 Tool/Skill 机制来解答该问题，并将结果打印出来。

涉及模块与限制:
- `index.ts`: 主入口，包含 CLI 交互和 AI SDK 调用。
- `tools/diskSpace.ts`: 定制的 Tool，用于获取系统的磁盘空间信息（防止硬编码）。
- 错误处理与用户提示按需完善。
- 必须包含详细的中文注释。
- 禁止代码中硬编码磁盘信息。

## [Blueprint]
方案设计:

1.  **项目骨架搭建**:
    - 初始化 `package.json` 和 `tsconfig.json` 以支持 Bun 和 TypeScript 运行环境。
    - 安装核心依赖: `@ai-sdk/openai`, `ai`, `zod` 以及用于 CLI 交互的 readline 模块（Bun 的原生支持即可）。

2.  **创建系统级 Tool**:
    - 在 `src/tools/diskSpace.ts` (或同级工具文件) 中定义一个 tool。
    - 使用 Node.js 原生的 `child_process.exec` 或其他方式执行类似于 `df -h /` (适用 Mac/Linux) 的命令，真实读取当前机器磁盘空间。
    - 使用 `zod` 定义输入和输出 Schema，以便 Vercel AI SDK 可以理解和调用。

3.  **主程序实现**:
    - 在 `src/index.ts` 中设置 Vercel AI SDK 的调用，比如使用 `generateText` 或 `streamText`，连接至 OpenAI (或者配置的兼容模型)。
    - 将 `diskSpace` Tool 注册到调用参数中。
    - 通过标准输入（stdin）读取用户问题，并传递给模型，打印模型及 tool 被调用的返回结果。

## [Implementation Steps]

1.  **初始化项目配置**
    - 在 `projects/testVercelAISDK/` 目录下创建 `package.json` 文件，并添加 `dev` 脚本：`"dev": "bun run src/index.ts"`，增加依赖：`ai`, `zod`, `@ai-sdk/openai`。
    - 在同级目录创建 `tsconfig.json` 配置 Bun 和 TypeScript 支持。

2.  **实现 `diskSpace` 工具 (Tool)**
    - 在 `projects/testVercelAISDK/src/tools/diskSpace.ts` 中创建工具模块。
    - 引入 `tool` (来自 `ai`) 和 `z` (来自 `zod`)，及 Node 的 `child_process` 模块。
    - 定义该工具的描述："获取当前电脑的磁盘空间信息"。
    - 实现 `execute` 逻辑，运行子进程执行 `df -h /` (Mac系统下获取根目录容量的方法，因为用户是macOS)，将 `stdout` 作为结果返回。
    - 添加详细的中文注释。
    ```typescript
    // 伪代码示例
    import { tool } from 'ai';
    import { z } from 'zod';
    import { exec } from 'child_process';
    import { promisify } from 'util';

    const execPromise = promisify(exec);

    export const diskSpaceTool = tool({
        description: '获取当前电脑的磁盘空间信息',
        parameters: z.object({}),
        execute: async () => {
            // 执行终端命令获取 Mac 的磁盘空间
            const { stdout } = await execPromise('df -h /');
            return stdout;
        }
    });
    ```

3.  **实现主程序及其 CLI 交互**
    - 在 `projects/testVercelAISDK/src/index.ts` 中实现主逻辑。
    - 引入 `readline`，设置控制台输入，打印提示 `> `，等待用户输入（例如"我这个电脑磁盘空间"）。
    - 引入 `@ai-sdk/openai` 和 `generateText` (或相似API)；
    - 接收用户输入后，调用 `generateText` 处理请求，注入 `diskSpaceTool`，设置 `maxSteps` (如果是 tool looping)。
    - 当得到结果，如果大模型调用了 tool，打印 tool 的相关信息及最终回答。
    - 添加必要的中文注释。

4.  **最终测试验证**
    - 验证用户运行 `bun run dev` 工具可以正常启动等待输入。
    - 验证输入"我这个电脑磁盘空间"可以触发调用，且未硬编码，获取的是真实系统数据。
