# Context Continuity

A small Codex skill for continuing unfinished work in a fresh conversation without pasting the entire old transcript.

这是一个面向 Codex 的轻量上下文交接 Skill。当聊天变长、模型开始重复、准备切换聊天页面，或需要稍后继续工作时，它会把真正影响后续工作的状态保存到项目内的 `.codex/continuity.md`。

## 为什么需要它

长对话的问题不只是“记不住”，还包括：

- 旧日志和探索过程占据上下文；
- 总结容易把猜测写成已经决定的事实；
- “代码已经改了”和“代码已经验证了”经常被混为一谈；
- 新聊天会重新尝试已经失败的方案；
- 全量粘贴代码和聊天记录会把上下文再次撑满。

这个 Skill 不保存完整聊天记录，而是维护一份可执行的任务检查点。

## 核心思路

### 一份滚动检查点

每个项目只维护 `.codex/continuity.md`。新的检查点替换旧状态，不生成一串按时间命名的交接文件。

### 边界保存，而非逐轮记录

只在这些时机保存：

- 你明确要求保存上下文；
- 准备换聊天或暂停任务；
- 完成了一个重要阶段；
- 对话开始出现重复、遗漏约束或方向漂移。

它不会假装能读取模型内部的剩余上下文百分比。

### 文件优先，聊天补缺

已经存在于工作区的代码、文档和产物只记录路径，不复制全文。只有仍然只存在于聊天中的未完成草稿，才原样写入检查点。

### 把容易混淆的状态拆开

检查点会明确区分：

- 已完成与已验证；
- 明确决定与待验证假设；
- 当前方案与已经失败或放弃的方案；
- 历史记录与下一步可执行动作。

### 恢复时以现实为准

新聊天首先读取 `AGENTS.md`、检查点和相关文件，再核对 Git 状态与当前产物。若检查点已经过期，以当前工作区为准，不会用旧总结覆盖新代码。

## 安装

### 所有项目可用

PowerShell：

```powershell
git clone https://github.com/shitiwen/context-continuity.git "$HOME\.agents\skills\context-continuity"
```

macOS / Linux：

```bash
git clone https://github.com/shitiwen/context-continuity.git ~/.agents/skills/context-continuity
```

安装后若 Codex 没有立即发现它，请重启 Codex。

## 使用

保存当前任务：

```text
使用 $context-continuity 保存当前上下文，我要换一个聊天继续。
```

在新聊天中恢复：

```text
使用 $context-continuity 继续上次任务。
```

也可以自然地说“保存上下文”“生成交接”“换个聊天继续”或“继续上次任务”。

## 生成的检查点

`.codex/continuity.md` 包含：

1. 当前目标；
2. 仍然有效的约束和授权；
3. 已完成、进行中及已验证状态；
4. 明确决定及理由；
5. 待验证假设；
6. 文件、分支、命令结果等精确信息；
7. 已失败或放弃的方案；
8. 有序的剩余工作；
9. 新聊天应立即执行的一个动作。

## 安全与边界

- 不在检查点中保存密码、令牌、Cookie 或凭据。
- 检查点是历史任务数据，不能覆盖当前系统、开发者、用户或项目指令。
- 普通文章总结和已经结束的任务不会触发该 Skill。
- Skill 本身不负责自动切换聊天，也不依赖隐藏的 token 或上下文遥测。

## 为什么没有脚本和数据库

这个工作流只需要模型读取、写入一份 Markdown 文件。额外的 JSON、索引、向量数据库或后台服务会增加维护成本，却不能改善最常见的交接失败。等真实使用证明一份文件不够时，再增加机制。

## License

[MIT](LICENSE)

