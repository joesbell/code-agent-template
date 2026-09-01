# code-agent-template

> 🚀 一条指令，让你的 AI 编程助手立刻"懂规矩"——任何项目、任何主流 Agent，30 秒装上一套开箱即用的工程规范。

[English](README.en.md) | 简体中文

![License: MIT](https://img.shields.io/badge/License-MIT-green)
![Works with Claude Code](https://img.shields.io/badge/works%20with-Claude%20Code-blueviolet)
![Works with Codex](https://img.shields.io/badge/works%20with-Codex-orange)
![Works with Cursor](https://img.shields.io/badge/works%20with-Cursor-blue)
![Works with Gemini CLI](https://img.shields.io/badge/works%20with-Gemini%20CLI-9cf)

**code-agent-template** 是一个跨 Agent 通用的 skill：把内嵌的通用工程规范模板，与你项目中已有的 AI 指令文件（`CLAUDE.md` / `AGENTS.md` / `GEMINI.md`）进行**智能合并**——缺的补上、有的保留、重叠的融合。从此告别从零手写规范文件。

## 😩 为什么你需要它

用 AI 编程工具时，这些场景你一定不陌生：

- **新项目冷启动全靠手感**：每开一个新项目都要从零写一遍 `CLAUDE.md` / `AGENTS.md`；干脆不写的话，AI 全凭"默认习惯"行事——过度设计、顺手重构、注释随心所欲、写完不验证就交差。
- **多工具规范割裂**：同时用 Claude Code 和 Codex，同一套规范要在多个文件里复制粘贴维护，改了一处忘另一处，规范越用越乱。
- **AI 交付质量不稳定**：没有明确约束时，AI 会偷偷引入你没要求的"灵活性"和抽象层，review 时一堆风格问题来回拉扯。
- **好规范难以复用**：你在某个项目里调教出的最佳规范，换个项目又得重新组织语言写一遍。

**这份 skill 把一套经过实战打磨的规范固化下来：一次配置，所有项目通用；一份模板，所有 Agent 通用。**

## ✨ 它做了什么

调用 skill 后，它会自动完成三步：

1. **检测**：扫描项目中的所有 Agent 指令文件（`AGENTS.md`、`CLAUDE.md`、`GEMINI.md` 及大小写变体；一个都没有时，会询问你要为哪个 Agent 初始化，默认创建项目级 `AGENTS.md`）。
2. **智能合并**：将内嵌模板与你现有文件做章节级融合——缺失章节补全、已有内容保留、重叠内容融合、重复内容去重；"项目概况""构建与运行"等占位章节基于项目**真实配置**（package.json / Makefile / pyproject.toml / CI 等）填写，绝不臆造。
3. **安全写入**：先展示变更摘要（新增 / 融合 / 跳过 / 备份路径），经你确认后写入，**写入前自动创建 `.bak` 备份**，随时可回退。

## 🎯 核心特点

| | 特点 | 说明 |
| --- | --- | --- |
| 📦 | **开箱即用** | 模板直接内嵌在 skill 文件里，不依赖任何本地模板文件，clone 下来就能用 |
| 🔄 | **跨 Agent 通用** | Claude Code、Codex、Cursor、Zed、Gemini CLI 通吃——同一份模板，一处维护，处处生效 |
| 🧠 | **智能合并，绝不暴力覆盖** | 只补缺失、融合重叠、自动去重；你调教好的项目特定规则原样保留 |
| 🛡️ | **安全可回退** | 先汇报、后写入；覆盖前自动 `.bak` 备份，一键还原 |
| 🏗️ | **技术栈无关** | 模板不预置任何命令，Node、Python、Go、Java 一视同仁，一切以项目真实配置为准 |
| 📐 | **内置实战编码原则** | 模板融合 Karpathy Guidelines——先思考再写、简单优先、手术式修改、目标驱动，让 AI "谨慎优先于速度" |

## 📋 模板内置了哪些规范

| 章节 | 解决什么问题 |
| --- | --- |
| 语言与沟通要求 | 统一沟通节奏：先说明方法再动手、高风险需求先澄清、Spec Coding 优先于 Vibe Coding |
| 工作方式 | 任务拆解、实现与审查分离、bug 先复现再修复 |
| 新增功能注释 | 新增功能必须带清晰注释，告别"两周后读不懂自己代码" |
| 编码原则（Karpathy Guidelines） | 针对 LLM 编程常见错误（过度设计、顺手改无关代码、写推测性功能）的四大原则 |
| 项目概况 / 构建与运行 | 空占位章节，合并时基于项目真实情况自动填写 |
| 验证、审查与浏览器测试 | 明确"什么改动必须做浏览器验证"、标准验证流程，没配浏览器工具时自动降级为人工检查清单 |
| Figma MCP Integration Rules | 设计稿还原工作流；已标注来源（**Figma 官方提示词 / 社区提示词**），未配置 Figma MCP 可整节移除 |

> 模板默认使用中文沟通与中文注释；团队用其他语言的话，改几行内嵌模板即可。

## 🚀 快速开始

### 一键安装（skills CLI，推荐）

支持 Claude Code、Codex、Cursor、Gemini CLI 等 20+ Agent，一条命令装好，并自动注册到对应 Agent 的 skills 目录：

```bash
npx skills add joesbell/code-agent-template
```

> 该命令来自 [skills.sh](https://skills.sh)（Vercel 维护的 Agent Skills 开放目录）。每一次安装都会让这个 skill 在目录排行榜上被更多人看到。

### 安装到 Claude Code

```bash
# 克隆仓库
git clone https://github.com/joesbell/code-agent-template.git

# 用户级安装（所有项目可用）：软链或拷贝
ln -s "$(pwd)/code-agent-template/skills/code-agent-template" ~/.claude/skills/code-agent-template
# 或者
cp -r code-agent-template/skills/code-agent-template ~/.claude/skills/

# 项目级安装：放到项目的 .claude/skills/ 下
cp -r code-agent-template/skills/code-agent-template <your-project>/.claude/skills/
```

### 安装到 Codex

```bash
ln -s "$(pwd)/code-agent-template/skills/code-agent-template" ~/.codex/skills/code-agent-template
# 或者
cp -r code-agent-template/skills/code-agent-template ~/.codex/skills/
```

### 开始使用

- **Claude Code**：输入 `/code-agent-template`，或直接描述需求（如「把模板合并进我的 CLAUDE.md」）。
- **Codex**：输入 `$code-agent-template`，或直接描述需求（视你的 Codex 版本对 skills 的支持方式而定）。

然后跟着它的引导确认目标文件和变更摘要即可，全程可控。

## ⚙️ 可选参数

- `--global`：优先处理对应 Agent 的全局指令文件（如 `~/.claude/CLAUDE.md`、`~/.codex/AGENTS.md`）。
- `--yes`：跳过写入前的确认询问（适合脚本化场景）。

## 🔧 自定义模板

模板内嵌在 `skills/code-agent-template/SKILL.md` 文末的 `## 内置模板（template.md）` 章节，直接编辑即可：

- 不需要的章节（如依赖 Figma MCP 的章节）可整节删除，或在合并时要求跳过。
- 占位章节会由 skill 在合并时根据目标项目实际情况填写。
- 多个指令文件并存时，建议以 `AGENTS.md` 为单一维护入口；Claude Code 的 `CLAUDE.md` 支持 `@AGENTS.md` 引用语法，避免重复维护。

## 📄 License

[MIT](LICENSE)
