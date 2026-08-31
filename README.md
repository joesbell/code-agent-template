# code-agent-template

跨 Agent 通用的「项目规范模板合并」skill：把一份内嵌的通用 Agent 规范模板，与你项目中已有的 AI 编程 Agent 指令文件智能合并，补全缺失章节、保留项目特定内容。

单一 `SKILL.md` 文件自包含全部模板内容，**无需任何本地模板文件**，Claude Code、Codex 等支持 skills 机制的工具都可以直接使用。

## 特性

- **模板内嵌**：通用模板直接写在 skill 内，不依赖 `CLAUDE.md.template` 之类的本地文件。
- **跨 Agent 目标检测**：自动识别并处理以下指令文件（存在才处理，不存在则跳过）：

  | Agent | 项目级 | 全局/用户级 |
  | --- | --- | --- |
  | Codex、Cursor、Zed 等（AGENTS.md 标准） | `AGENTS.md` | `~/.codex/AGENTS.md` |
  | Claude Code | `CLAUDE.md`、`.claude/CLAUDE.md` | `~/.claude/CLAUDE.md` |
  | Gemini CLI | `GEMINI.md`、`.gemini/GEMINI.md` | `~/.gemini/GEMINI.md` |
  | 变体文件 | `agent.md`、`agents.md` | — |

- **智能合并**：保留优先、补全缺失、重叠融合、自动去重，不会简单覆盖你的已有配置。
- **安全写入**：覆盖前自动创建 `.bak` 备份，并先向你汇报变更摘要、确认后才写入。
- **技术栈无关**：模板中的「项目概况」「构建与运行」为空占位，合并时基于目标项目真实配置（package.json / Makefile / pyproject.toml / README / CI 等）填写，不预置任何具体命令。

## 模板包含的章节

语言与沟通要求 / 工作方式 / 新增功能注释 / 编码原则（Karpathy Guidelines）/ 项目概况 / 构建与运行 / 验证、审查与浏览器测试 / Figma MCP Integration Rules（按需启用）。

> 模板默认使用中文沟通与中文注释。如果你的团队使用其他语言，直接修改 SKILL.md 文末的内嵌模板即可。

## 安装

### Claude Code

```bash
# 克隆仓库
git clone https://github.com/<you>/code-agent-template.git

# 用户级安装（所有项目可用）：软链或拷贝
ln -s "$(pwd)/code-agent-template/skills/code-agent-template" ~/.claude/skills/code-agent-template
# 或者
cp -r code-agent-template/skills/code-agent-template ~/.claude/skills/

# 项目级安装：放到项目的 .claude/skills/ 下
cp -r code-agent-template/skills/code-agent-template <your-project>/.claude/skills/
```

### Codex

```bash
ln -s "$(pwd)/code-agent-template/skills/code-agent-template" ~/.codex/skills/code-agent-template
# 或者
cp -r code-agent-template/skills/code-agent-template ~/.codex/skills/
```

## 使用

- **Claude Code**：输入 `/code-agent-template`，或直接描述需求（如「把模板合并进我的 CLAUDE.md」）。
- **Codex**：输入 `$code-agent-template`，或直接描述需求（视你的 Codex 版本对 skills 的支持方式而定）。

执行流程：检测目标文件 → 向你汇报待处理文件列表 → 读取并分析结构 → 生成合并内容 → 展示变更摘要（新增 / 融合 / 跳过 / 备份路径）→ 确认后创建 `.bak` 并写入。

可选参数：

- `--global`：优先处理对应 Agent 的全局指令文件（如 `~/.claude/CLAUDE.md`、`~/.codex/AGENTS.md`）。
- `--yes`：跳过写入前的确认询问。

项目中一个指令文件都没有时，skill 会先询问你要为哪个 Agent 初始化；未指定则默认创建项目级 `AGENTS.md`（跨工具通用标准，其他工具可复制或引用）。

## 自定义模板

模板内嵌在 `skills/code-agent-template/SKILL.md` 文末的 `## 内置模板（template.md）` 章节，直接编辑该章节即可：

- 不需要的章节（如「Figma MCP Integration Rules」依赖 Figma MCP 配置）可整节删除，或在合并时要求跳过。
- 「项目概况」「构建与运行」等占位章节会由 skill 在合并时根据目标项目实际情况填写。
- 多个指令文件并存时，建议以 `AGENTS.md` 为单一维护入口；Claude Code 的 `CLAUDE.md` 支持 `@AGENTS.md` 引用语法，可避免重复维护。

## License

[MIT](LICENSE)
