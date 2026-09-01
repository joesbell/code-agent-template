# code-agent-template

> 🚀 One command to make your AI coding agent follow the rules — a battle-tested engineering spec for any project, any mainstream agent, installed in 30 seconds.

[简体中文](README.md) | English

![License: MIT](https://img.shields.io/badge/License-MIT-green)
![Works with Claude Code](https://img.shields.io/badge/works%20with-Claude%20Code-blueviolet)
![Works with Codex](https://img.shields.io/badge/works%20with-Codex-orange)
![Works with Cursor](https://img.shields.io/badge/works%20with-Cursor-blue)
![Works with Gemini CLI](https://img.shields.io/badge/works%20with-Gemini%20CLI-9cf)

**code-agent-template** is a cross-agent skill: it smartly merges its built-in universal engineering spec template into your project's existing AI instruction files (`CLAUDE.md` / `AGENTS.md` / `GEMINI.md`) — fills in what's missing, keeps what you already have, fuses what overlaps. Stop writing spec files from scratch.

> **Note:** the embedded template defaults to Chinese for communication and code comments. If your team works in another language, tweak a few lines of the embedded template (see [Customizing the template](#-customizing-the-template)).

## 😩 Why you need it

If you code with AI assistants, these will sound familiar:

- **Every new project starts from scratch**: you rewrite `CLAUDE.md` / `AGENTS.md` for every repo — or skip it, and the AI runs on its "default habits": over-engineering, drive-by refactors, comments on a whim, no verification before delivery.
- **Fragmented specs across tools**: using Claude Code and Codex side by side means copy-pasting the same rules into multiple files, updating one and forgetting the other, until the spec drifts apart.
- **Unstable AI output quality**: without explicit constraints, the AI quietly adds "flexibility" and abstraction layers you never asked for, and reviews turn into style tug-of-war.
- **Good specs don't travel**: the rules you fine-tuned in one project have to be re-written from memory in the next.

**This skill locks in a battle-tested spec: configure once, use in every project; one template, effective in every agent.**

## ✨ What it does

When invoked, the skill runs three steps:

1. **Detect**: scans the project for all agent instruction files (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, and case variants; if none exist, it asks which agent to initialize for and defaults to a project-level `AGENTS.md`).
2. **Smart merge**: merges the embedded template into your existing files section by section — missing sections are added, existing content is preserved, overlapping sections are fused, duplicates are removed. Placeholder sections like "Project overview" and "Build & run" are filled from your project's **real** configuration (package.json / Makefile / pyproject.toml / CI, etc.) — never invented.
3. **Safe write**: shows a change summary (added / merged / skipped / backup path) and waits for your confirmation; a `.bak` backup is created automatically before writing, so you can always roll back.

## 🎯 Key features

| | Feature | What it means |
| --- | --- | --- |
| 📦 | **Zero setup** | The template is embedded in the skill file itself — no local template files, works right after install |
| 🔄 | **Cross-agent** | Claude Code, Codex, Cursor, Zed, Gemini CLI — one template, maintained once, effective everywhere |
| 🧠 | **Smart merge, never brute-force overwrite** | Only fills gaps, fuses overlaps, removes duplicates; your fine-tuned project-specific rules stay untouched |
| 🛡️ | **Safe and reversible** | Report first, write later; automatic `.bak` backup before overwriting, one step to restore |
| 🏗️ | **Stack-agnostic** | The template hard-codes no commands — Node, Python, Go, Java are treated equally, always deferring to the project's real configuration |
| 📐 | **Battle-tested coding principles built in** | Incorporates Karpathy Guidelines — think before coding, simplicity first, surgical changes, goal-driven — so the AI puts "caution over speed" |

## 📋 What's inside the template

| Section | What problem it solves |
| --- | --- |
| Language & communication | One communication rhythm: state the approach before acting, clarify high-risk requirements first, Spec Coding over Vibe Coding |
| Way of working | Task decomposition, implementation separated from review, reproduce bugs before fixing |
| Comments for new features | New features must ship with clear comments — no more "can't read my own code two weeks later" |
| Coding principles (Karpathy Guidelines) | Four principles targeting common LLM coding failure modes (over-engineering, touching unrelated code, speculative features) |
| Project overview / Build & run | Empty placeholder sections, filled automatically from the real project at merge time |
| Verification, review & browser testing | Defines exactly which changes require browser verification and the standard verification flow; degrades to a manual checklist when no browser tool is configured |
| Figma MCP Integration Rules | Design-to-code workflow; source annotated (**Figma official prompt / community prompt**), remove the whole section if you don't use Figma MCP |

> The template defaults to Chinese for communication and code comments; if your team works in another language, tweak a few lines of the embedded template.

## 🚀 Quick start

### One-line install (skills CLI, recommended)

Works with Claude Code, Codex, Cursor, Gemini CLI and 20+ other agents — one command installs the skill and registers it with each agent's skills directory:

```bash
npx skills add joesbell/code-agent-template
```

> The command comes from [skills.sh](https://skills.sh), the open Agent Skills directory maintained by Vercel. Every install helps this skill climb the leaderboard and reach more people.

### Manual install to Claude Code

```bash
# Clone the repository
git clone https://github.com/joesbell/code-agent-template.git

# User-level install (available in all projects): symlink or copy
ln -s "$(pwd)/code-agent-template/skills/code-agent-template" ~/.claude/skills/code-agent-template
# or
cp -r code-agent-template/skills/code-agent-template ~/.claude/skills/

# Project-level install: put it under the project's .claude/skills/
cp -r code-agent-template/skills/code-agent-template <your-project>/.claude/skills/
```

### Manual install to Codex

```bash
ln -s "$(pwd)/code-agent-template/skills/code-agent-template" ~/.codex/skills/code-agent-template
# or
cp -r code-agent-template/skills/code-agent-template ~/.codex/skills/
```

### Start using

- **Claude Code**: type `/code-agent-template`, or just describe what you want (e.g. "merge the template into my CLAUDE.md").
- **Codex**: type `$code-agent-template`, or describe the task (depending on how your Codex version supports skills).

Then follow its guidance to confirm the target files and the change summary — you stay in control throughout.

## ⚙️ Optional flags

- `--global`: prefer the agent's global instruction file (e.g. `~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`).
- `--yes`: skip the pre-write confirmation (useful in scripted scenarios).

## 🔧 Customizing the template

The template is embedded at the end of `skills/code-agent-template/SKILL.md`, under the `## 内置模板（template.md）` (built-in template) section — edit it directly:

- Sections you don't need (e.g. the Figma MCP section) can be deleted entirely, or skipped at merge time on request.
- Placeholder sections are filled by the skill based on the target project's actual setup at merge time.
- When multiple instruction files coexist, keep `AGENTS.md` as the single source of truth; Claude Code's `CLAUDE.md` supports the `@AGENTS.md` reference syntax to avoid duplicate maintenance.

## 📄 License

[MIT](LICENSE)
