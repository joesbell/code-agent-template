---
name: code-agent-template
description: 将本 skill 内置的通用 Agent 规范模板，与 AI 编程 Agent 的指令文件（AGENTS.md / CLAUDE.md / GEMINI.md / agent.md 等）智能合并融合；适用于 Claude Code、Codex、Gemini CLI、Cursor 等工具；模板已内嵌于本 skill，无需任何本地模板文件。当用户要初始化项目 Agent 规范、合并模板进指令文件时使用
---

# /code-agent-template

## 作用

当用户调用本 skill 时，将本 skill 内置的通用模板（见文末 `## 内置模板` 章节）作为基准，与目标 Agent 指令文件进行智能合并融合：

- 目标文件里缺失的模板章节 → 按模板结构补全。
- 目标文件里已有的项目特定内容 → 保留。
- 重叠章节 → 融合，避免简单覆盖或重复堆砌。
- 目标文件不存在 → 按用户当前使用的 Agent 创建新文件（见下文检测规则）。

## 模板来源

模板内容已直接内嵌在本 skill 文末的 `## 内置模板（template.md）` 章节中，**始终以该内置模板为基准**：

- 无需、也不要去查找或读取本地任何模板文件。
- 模板需要更新时，直接修改本 skill 文件中的内置模板章节即可。
- 模板本身与具体 Agent 无关，合并进任何指令文件都适用。

## 目标文件检测

不同 Agent 使用不同指令文件。**存在才处理，不存在则跳过**；检测时注意大小写变体（如 `agents.md`、`claude.md`）：

| Agent | 项目级 | 全局/用户级 |
| --- | --- | --- |
| Codex、Cursor、Zed 等（AGENTS.md 标准） | `<cwd>/AGENTS.md` | `~/.codex/AGENTS.md` |
| Claude Code | `<cwd>/CLAUDE.md`、`<cwd>/.claude/CLAUDE.md` | `~/.claude/CLAUDE.md` |
| Gemini CLI | `<cwd>/GEMINI.md`、`<cwd>/.gemini/GEMINI.md` | `~/.gemini/GEMINI.md` |
| 变体文件 | `<cwd>/agent.md`、`<cwd>/agents.md` | — |

处理顺序与规则：

1. **用户明确指定**目标文件时，只处理用户指定的文件。
2. 未指定时，处理项目中**实际存在**的所有指令文件。
3. 一个都没检测到时，询问用户要为哪个 Agent 初始化；用户未回答时默认创建项目级 `AGENTS.md`（跨工具通用标准，其他工具可再复制或引用）。
4. 若用户附带 `--global` 参数，则优先处理对应 Agent 的全局文件（按上表"全局/用户级"列）。
5. 检测完成后，先向用户汇报将要处理的文件列表，并询问是否正确。

## 合并规则

- **保留优先**：目标文件中已有的项目特定内容（项目概况、技术栈、构建命令、自定义规则、MCP/浏览器验证等已落地配置）优先保留。
- **补全缺失**：模板中有而目标文件中没有的章节，按模板结构补充。
- **智能融合**：同一章节双方都有内容时，以目标文件现有规则为主，用模板填补空白，不直接覆盖已有约定。
- **去重**：合并后若出现完全重复的条目或段落，删除重复项。
- **不主动删除**：除非与新增内容完全重复，否则不删除目标文件中的原有章节。

## 执行流程

1. 直接使用本 skill 内置模板（见文末），不要去读取本地任何模板文件。
2. 按上述规则检测目标指令文件。
3. 向用户汇报检测到的目标文件，询问是否正确。
   - 若用户指明其他路径，按用户路径执行。
4. 读取模板和各目标文件，分析章节结构。
5. 按合并规则生成合并后的新内容。
6. 向用户展示变更摘要：
   - 新增章节
   - 融合/更新章节
   - 跳过的文件
   - 备份路径
7. 询问用户是否确认写入（除非调用时附带 `--yes`）。
8. 确认后，为每个将被覆盖的目标文件创建 `.bak` 备份（例如 `AGENTS.md.bak`、`CLAUDE.md.bak`）。
9. 写入合并后的内容。
10. 报告写入路径、备份路径和变更摘要。

## 注意事项

- 本 skill 会修改用户的指令文件，**写入前必须创建 `.bak` 备份**。
- 合并完成后，建议用户快速复核一遍目标文件，确保项目特定内容未被误改。
- 若模板中的占位章节（如 `## 项目概况`）与目标文件已有内容冲突，以目标文件为准。
- 填写"项目概况""构建与运行"等占位章节时，必须基于目标项目的真实情况（先阅读 package.json / Makefile / pyproject.toml / README / CI 配置等再写），不要预置或臆造具体命令；项目里没有对应内容的子项就留空占位。
- 同一项目存在多个指令文件时，建议以 `AGENTS.md` 为单一维护入口；Claude Code 的 `CLAUDE.md` 支持 `@AGENTS.md` 引用语法，可避免多份内容重复维护（不支持引用的工具再复制内容）。
- 模板中的"浏览器验证""Figma MCP"章节依赖用户环境的 MCP 配置；目标项目没有相关配置时，合并时可将对应章节标注为"按需启用"或按用户意见裁剪。

---

## 内置模板（template.md）

以下内容是合并时使用的基准模板，始终以本章节内容为准。

````markdown
# 通用项目 Agent 规范模板

> 本模板用于为 AI 编程 Agent 统一项目行为规范，适用于 Claude Code、Codex、Gemini CLI、Cursor 等主流工具。
> 请根据实际项目填写 `## 项目概况`、`## 构建与运行` 等占位部分。

---

## 语言与沟通要求

- **回答问题必须使用中文。**
- 在实现前先说明方法。
- 若需求有歧义、风险较高或影响较大，先澄清并等待批准，再开始写代码。
- Plan 只写方案，不写代码。
- 坚持 Spec Coding，不做 Vibe Coding。

---

## 工作方式

- 将任务拆分为低耦合、可独立验证的子任务。
- 实现与审查分离：先完成方案或代码，再独立复核。
- 处理 bug 时，先复现，再修复并验证。

---

## 新增功能注释

- 在代码中新增功能、方法、接口、组件或配置项时，必须为其添加清晰的功能注释(必须是中文注释!)，说明其用途，便于区分和后续维护。

---

## 编码原则（Karpathy Guidelines）

> 这些原则旨在减少 LLM 编程中的常见错误，整体偏向“谨慎而非速度”。
> 对于明显简单的任务，可自行判断，不必教条执行。

### 1. 先思考再写代码

- 明确说出你的假设；不确定时直接提问。
- 存在多种理解时，列出选项，不要悄悄选择其中一个。
- 有更简单方案时指出来；必要时敢于 push back。
- 遇到不清楚的地方，停下来说明困惑点并询问。

### 2. 简单优先

- 只写解决问题的最少代码；不写推测性功能。
- 不为一次性代码做抽象；不增加未要求的“灵活性”或“可配置性”。
- 不写不可能场景的错误处理。
- 如果写了 200 行其实 50 行就能解决，重写。

### 3. 手术式修改

- 只动必须动的地方；不“顺手优化”相邻代码、注释或格式。
- 不重构没坏的东西；保持现有风格，即使你个人风格不同。
- 只清理**你的改动**导致的未使用 import / 变量 / 函数。
- 除非被要求，否则不删除已有的死代码。

### 4. 目标驱动执行

- 把任务转化为可验证的目标，例如：
  - “加校验” → “先写无效输入测试，再让它通过”
  - “修 bug” → “先写复现测试，再让它通过”
  - “重构 X” → “确保前后测试都通过”
- 多步骤任务给出简短计划，每步带验证标准。

---

## 项目概况（按实际项目填写）

- **项目名称**：
- **技术栈**：
- **主要依赖**：
- **项目结构**：

---

## 构建与运行（按实际项目填写）

> 根据项目实际技术栈填写，不要照搬其他项目的命令；以下子项按需增删。

- **安装依赖**：
- **本地开发**：
- **生产构建**：
- **测试**：

---

## 验证、审查与浏览器测试

> 本节是测试验证板块的**最高优先级**规则。

### 通用原则（第一性原理 + 独立复核）

无论是浏览器验证还是代码审查/测试，都必须从**第一性原理**出发：

1. **明确要验证什么**：先定义“验证通过”的标准，而不是机械执行步骤。
2. **实现与复核分离**：把验证或审查当作独立任务完成；若当前环境支持子 Agent / 协作模式，优先委派给子 Agent 执行并要求其报告结论。
3. **先复核、后推进**：复核返回明确的通过 / 不通过 / 需关注结论及证据后，才继续推进；不要绕过复核结论擅自推进。

### 浏览器验证

#### 什么时候需要做浏览器验证

**不是所有开发功能都需要浏览器验证**，必须根据以下标准判断：

- **必须做**：前端需求，且涉及页面改动、页面交互改动或大范围 UI 行为调整。
- **建议做**：页面逻辑较复杂、涉及多组件联动、表单校验、权限展示、数据可视化等，即使改动不大，也应评估风险。
- **可跳过**：纯逻辑 / 数据层改动、无页面表现的小改动、仅修改常量 / 文案 / 配置等。
- **用户明确说不需要时**：跳过。

#### 浏览器验证工具与流程

- 需要做浏览器验证时，使用环境中已配置的浏览器自动化 MCP（具体 server 与工具名以当前 MCP 配置为准）。
- 若环境未配置任何浏览器自动化工具，则如实告知用户，并把验证要点整理成人工检查清单交给用户执行。
- 标准流程：
  1. 打开目标 URL。
  2. 等待页面加载完成。
  3. 获取页面结构与可交互元素引用（任何点击 / 输入之前必须先做）。
  4. 用返回的元素引用执行点击 / 输入 / 选择等操作。
  5. 断言结果是否符合预期。
  6. 截图留证。
- 复杂页面可配合等待元素出现、等待文本、滚动定位等能力。

#### 页面改动完成后的验证约定

完成任何涉及页面的开发任务（新增 / 改动页面、优化布局、调整样式或交互、新增组件等）后：

1. 先用中文总结本次实际改动：改动了哪些文件、实现了什么功能或修复了什么问题。
2. 主动询问用户是否需要浏览器验证，并请用户提供验证 URL（本地开发地址或线上地址）。
3. 用户确认并提供 URL 后，使用上面的标准流程重点验证本次开发的功能。
4. 验证完成后如实反馈结果（通过 / 未通过，并附截图证据）；未通过则说明现象并给出排查方向。

### 代码审查与测试

- **代码审查不限于前端**：前端、后端、数据库相关代码都可以进行代码审查和测试。
- 代码审查应关注：
  - 功能正确性
  - 边界条件与异常处理
  - 安全性与性能隐患
  - 与现有代码风格、架构模式的一致性
- 对于非页面型改动（纯逻辑、接口、数据库脚本等），优先通过代码审查和单元 / 集成测试验证，而非浏览器验证。

---

## Figma MCP Integration Rules

> 仅当项目已配置 Figma MCP（如 Figma 官方 Dev Mode MCP server）时启用本节；未配置 Figma MCP 时可整节移除。

These rules define how to translate Figma inputs into code for this project and must be followed for every Figma-driven change.

### Required flow (do not skip)

1. Run `get_design_context` first to fetch the structured representation for the exact node(s).
2. If the response is too large or truncated, run `get_metadata` to get the high‑level node map and then re‑fetch only the required node(s) with `get_design_context`.
3. Run `get_screenshot` for a visual reference of the node variant being implemented.
4. Only after you have both `get_design_context` and `get_screenshot`, download any assets needed and start implementation.
5. Translate the output (usually React + Tailwind) into this project's conventions, styles and framework. Reuse the project's color tokens, components, and typography wherever possible.
6. Validate against Figma for 1:1 look and behavior before marking complete.

### Implementation rules

- Treat the Figma MCP output (React + Tailwind) as a representation of design and behavior, not as final code style.
- Replace Tailwind utility classes with the project's preferred utilities/design‑system tokens when applicable.
- Reuse existing components (e.g., buttons, inputs, typography, icon wrappers) instead of duplicating functionality.
- Use the project's color system, typography scale, and spacing tokens consistently.
- Respect existing routing, state management, and data‑fetch patterns already adopted in the repo.
- Strive for 1:1 visual parity with the Figma design. When conflicts arise, prefer design‑system tokens and adjust spacing or sizes minimally to match visuals.
- Validate the final UI against the Figma screenshot for both look and behavior.

---

## MCP Servers

### Figma MCP server rules

- The Figma MCP server provides an assets endpoint which can serve image and SVG assets
- IMPORTANT: If the Figma MCP server returns a localhost source for an image or an SVG, use that image or SVG source directly
- IMPORTANT: DO NOT import/add new icon packages, all the assets should be in the Figma payload
- IMPORTANT: do NOT use or create placeholders if a localhost source is provided
````
