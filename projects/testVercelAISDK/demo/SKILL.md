---
name: demo
description: 使用 Bun 和 Vercel AI SDK 构建一个能够查询系统磁盘空间的 CLI 工具。当需要测试 LLM 和 Tool (技能) 的联动时使用。
---

## [Context]
当前需求旨在测试 Vercel AI SDK 的核心能力，包括 LLM 的调用以及与外部 Tools (如查询磁盘空间) 的联动。
涉及的项目范围为一个新初始化的 Bun + TypeScript 命令行项目 (CLI/TUI)。根据 `ARCH.md` 规范，代码中禁止硬编码磁盘空间等环境信息，并且必须加入详细的中文注释。

## [Blueprint]
- **方案设计**：
  1. 使用 Bun 自身的构建工具进行项目初始化。
  2. 引入 `ai` 和 `@ai-sdk/openai` 等依赖以接入大模型。
  3. 创建一个 `checkDiskSpace` 的工具 (Tool)，内部使用 Node.js 的 `child_process` 模块动态执行 `df -h` 指令获取当前系统的真实磁盘信息。
  4. 使用 Node.js 的 `readline` 模块提供基本的交互式终端 (CLI/TUI)，接收问题的输入（例如："我这个电脑磁盘空间"）。
  5. 将用户输入传递给 Vercel AI SDK 的 `generateText` 或 `streamText`，让大模型自主决定调用 `checkDiskSpace` 并返回结果。

- **易错点 (3个)**：
  1. **异步 Tool 调用的作用域**：在使用 Vercel AI SDK 时，声明 tool 需要确保返回的是 Promise，且在 CLI 交互循环中，不当的 `await` 可能导致输入流阻塞。
  2. **跨平台兼容性死角**：使用 `child_process.exec('df -h', ...)` 是针对 macOS/Linux 的系统指令。若在 Windows 平台运行将直接报错，此处并未做跨平台降级处理（仅根据 MacOS 的默认场景处理）。
  3. **模型识别 Tool 的上下文限制**：在传递给 LLM 参数时，需要开启 `maxSteps: 5` (或类似设定) 允许多轮调用，否则 LLM 会直接输出需要调用 Tool 而无法拿到执行后的真实结果。

## [Implementation Steps]

**1. 初始化项目与配置文件**
在 `~/` 目录下执行 Bun 的初始化并安装必要的依赖。(如果 `~/package.json` 已存在请跳过此初始化部分，仅安装依赖)：
```bash
# 伪代码执行
bun init -y
bun add ai @ai-sdk/openai dotenv
bun add -d @types/node
```

**2. 编写入口代码与工具定义**
创建或更新文件 `~/index.ts`。在文件顶部引入依赖，并且根据要求**加入详细的中文注释**：
```typescript
// 在 ~/index.ts 中写入以下基础代码框架
import { openai } from '@ai-sdk/openai';
import { generateText, tool } from 'ai';
import * as child_process from 'node:child_process';
import { promisify } from 'node:util';
import * as readline from 'node:readline';
// 配置环境变量读取 (假设有个 .env 文件存放 OPENAI_API_KEY)
import 'dotenv/config';

// 1. 将 exec 转换为 Promise 形式，方便后续使用 async/await
const execAsync = promisify(child_process.exec);
```

**3. 定义 `checkDiskSpace` 工具**
继续在 `~/index.ts` 中，追加 Vercel AI SDK 的 tool 定义：
```typescript
import { z } from 'zod';

// 2. 定义一个给大模型调用的工具: 查询磁盘空间
const checkDiskSpaceTool = tool({
  description: '查询当前系统计算机的磁盘剩余空间',
  parameters: z.object({}), // 不需要参数
  execute: async () => {
    try {
      // 动态执行 df -h，禁止硬编码
      const { stdout } = await execAsync('df -h');
      return stdout;
    } catch (error) {
      return `执行命令失败: ${error}`;
    }
  },
});
```

**4. 编写 CLI 的交互逻辑**
继续在 `~/index.ts` 末尾，追加 Readline 的 CLI 并融合 SDK 调用：
```typescript
// 3. 创建终端读取器，用于不断接收用户的控制台输入
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

const askQuestion = () => {
  rl.question('请输入你的问题 (例如: 我这个电脑磁盘空间): ', async (userInput) => {
    if (!userInput) {
      askQuestion();
      return;
    }

    if (userInput.toLowerCase() === 'exit' || userInput.toLowerCase() === 'quit') {
      rl.close();
      return;
    }

    try {
      console.log('🤖 思考中，可能会调用工具...');
      
      // 4. 调用大模型，允许多轮步骤以执行 tool
      const result = await generateText({
        model: openai('gpt-4o'), // 或配置的其他模型
        prompt: userInput,
        tools: {
          checkDiskSpace: checkDiskSpaceTool
        },
        maxSteps: 3, // 必须允许多轮交互才能得到 tool 返回值再输出
      });

      console.log(`\n🤖 回答:\n${result.text}\n`);
    } catch (err) {
      console.error('\n❌ 发生错误:\n', err);
    }

    // 重新等待提问
    askQuestion();
  });
};

// 启动 CLI
console.log('欢迎使用 Vercel AI SDK Demo CLI。输入 exit 退出。');
askQuestion();
```

**5. 关于 API Key**
在 `~/` 目录下确保存在 `.env` 文件，其中配置 `OPENAI_API_KEY=xxx` 供 SDK 使用。
