- CLAUDE.md # 项目配置文件 ：写给 AI 看的项目说明书，核心内容：技术栈 + 编码规范 + 禁止行为
```
# 项目：[项目名称]

## 技术栈
Next.js 16 + TypeScript + Tailwind + shadcn/ui

## 规范
- 禁止 any，严格模式
- PascalCase 命名组件
- 用 pnpm

## 禁止
- 不装新依赖
- 不改 .env

## 常用命令
- 安装依赖：`pnpm install`
- 开发服务器：`pnpm dev`
- 构建：`pnpm build`
- 测试：`pnpm test`
- Lint：`pnpm lint`
- 类型检查：`pnpm typecheck`
```
在对话中使用 # 前缀，Claude Code 会自动将内容写入 CLAUDE.md  
当 CLAUDE.md 变得很长时，可以拆分到 .claude/rules/ 目录, Claude Code 会自动加载 rules/ 目录下的所有 .md 文件，与 CLAUDE.md 合并生效。  
```
.claude/
├── CLAUDE.md              # 项目概述和核心规则
└── rules/
    ├── coding-style.md    # 代码风格规范
    ├── git-workflow.md    # Git 工作流
    ├── testing.md         # 测试要求
    └── security.md        # 安全规则
```
- setting.json # settings.json 是 Claude Code 的系统配置文件，控制权限模式、环境变量、Hooks、MCP 服务器等。  
**配置文件位置：**

| 级别 | 位置| 作用域| 优先级|
| --- | --- | --- | --- |
|项目级|	.claude/settings.json|	当前项目	|高|
|用户级|	~/.claude/settings.json|	所有项目	|低|
|本地级|	.claude/settings.local.json|	本地开发|	最高|



  
