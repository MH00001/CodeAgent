# CodeAgent

基于 TypeScript 和 Node.js 的 Coding Agent 协作扩展。仓库只收录我实现的任务路由、团队协作、长期记忆与 Git Worktree 管理代码，以及子会话接入上下文压缩的说明；宿主 Agent SDK 和压缩引擎不在本仓库中。

## 核心实现

- **双层 Agent Loop**：主 Agent 负责理解请求、决定单 Agent 或团队路线，并汇总结果；复杂任务由独立子会话继续模型与工具循环。普通输入自动路由，`/plan` 则先生成结构化计划，经用户批准后执行、复核，失败时可重新规划。
- **多 Agent 协同**：`scout`、`planner`、`worker`、`reviewer` 拥有独立提示词、会话历史和工具权限。主 Agent 通过 `team_run` 分派任务，成员通过受限信箱传递结果；worker 的写入受文件白名单、暂存和冲突检查约束。
- **跨会话长期记忆**：分别管理用户偏好与项目事实，通过“必传核心—分类索引—按需检索”控制上下文长度。写入核对来源与冲突，每次模型调用前重新读取有效记录，使多个窗口能获取最新偏好。
- **Git Worktree 任务隔离**：按任务创建独立分支和工作目录，提供差异查看、检查点、命令验证及受控合入。任务失败时保留现场，合入前检查授权路径和主目录冲突。
- **会话压缩接入**：每个子 Agent 使用独立持久会话并启用宿主 SDK 的上下文压缩；主 Agent 只接收成员结果摘要。压缩引擎本身由宿主 SDK 提供，接入方式见 [会话压缩说明](docs/session-compaction.md)。

## 目录

| 路径 | 内容 |
| --- | --- |
| [`extensions/team/`](extensions/team/) | 路由、计划、角色会话、通信、文件权限与 Worktree 管理 |
| [`extensions/long-term-memory/`](extensions/long-term-memory/) | 记忆写入、检索、冲突处理、项目摘要与持久化 |
| [`docs/session-compaction.md`](docs/session-compaction.md) | 子会话压缩的接入边界与上下文流转 |

## 运行与验证

源码依赖 `@earendil-works/pi-*` Agent SDK 1.4.0；这是扩展源码仓库，不包含宿主 CLI。将两个扩展的 `index.ts` 加载到兼容的宿主环境即可使用。运行测试：

```sh
npm ci --ignore-scripts
npm test
```

本仓库不包含宿主 SDK 的 Agent Loop、会话压缩核心及其他上游模块。
