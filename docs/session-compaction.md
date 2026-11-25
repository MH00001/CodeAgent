# 子会话压缩接入

团队扩展通过 `createAgentSession` 为 `scout`、`planner`、`worker`、`reviewer` 分别创建会话。每个角色有自己的系统提示词、工具清单和历史；任务 Worktree 模式还按父会话、任务 ID、上下文版本和角色分隔持久会话目录。

创建成员会话时，扩展传入 `SettingsManager.inMemory({ compaction: { enabled: true } })`，由宿主 SDK 在上下文接近窗口限制时压缩该成员自己的历史。`/team reset` 与 `/solo` 会提升上下文版本，使后续成员从新会话开始。成员之间不会共享完整历史；消息通过信箱传递，主 Agent 只接收任务结果摘要及待办消息。

本仓库实现的是**子会话隔离、压缩启用和结果边界**。切分历史、生成摘要及恢复上下文的压缩算法属于外部 Agent SDK，没有复制到这里。
