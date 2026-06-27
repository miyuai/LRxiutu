# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Claude Code 插件：Lightroom Classic 智能人像修图。不是传统应用——这是一个 **skill + references** 结构的插件，由 Claude Code 加载执行，通过 `lightroom-classic-mcp` MCP server 间接控制 LR。

## Architecture

```
SKILL.md (50 lines)          ← 入口，始终加载。定义工作流步骤 + 触发词
  └─ references/             ← 按需加载（Claude 在工作流中 Read 对应文件）
       ├── time-periods.md   ← 6时段参数表 + 曝光/色温/锐度规则
       ├── hsl-styles.md     ← HSL A/B 双风格 + 联动参数
       ├── clarity-dim.md    ← 清透感↔胶片感维度 + 蒙版策略
       └── color-grading.md  ← 4时段色彩分级轮策略
```

**设计原则**：SKILL.md 只放工作流骨架（~50行），重参数表拆到 references/。Claude 执行时按需 `Read` 对应文件，避免 313 行全塞上下文。

## Dependency

- **`lightroom-classic-mcp`** (`~/Documents/claude code/lightroom-classic-mcp/`) — 独立的 MCP server 项目。提供 `catalog.*`, `develop.*`, `masks.*` 等 55+ MCP 工具。本插件不包含 MCP 通信逻辑，只负责修图参数推算。

## Key Design Decisions

1. **时段偏移替换基础规则**：黄金时刻色温偏移 -100K 直接替代基础规则的 -200K/-500K/-800K，不叠加。叠加会让肤色失真。
2. **Orange Hue 方向舵**：+8（向黄）= 暖金肤，-8（向红）= 白红透亮。这是亚洲 vs 西方肤色偏好的分叉点。
3. **全局压高光 + 蒙版刷回发丝光**：黄金时刻 Highlights -35 全局压皮肤高光，发丝轮廓光通过蒙版 3 精准刷回。不靠全局高光保留发丝光。
4. **清透感 vs 胶片感是同一组参数的反方向**：不能同时拉满。清透=提白+收黑+减雾+减暗角；胶片=压白+抬黑+雾+暗角。

## Commands

```bash
# 安装（本地开发）
ln -sf "$(pwd)" "$HOME/.claude-plugins/LRxiutu"

# 验证插件结构
# 需要 plugin-validator agent (plugin-dev 提供)

# 重新加载 skill（修改 SKILL.md 后）
# Claude Code 下次启动时自动加载，或 /reload
```

## Known LR SDK Limitations

| 问题 | 影响 |
|------|------|
| `ColorGradeHighlightHue/Sat` + `ColorGradeShadowHue/Sat` 写只读 | `set_param` 返回成功但 `get_settings` 不返回这些值，只能 LR 界面确认 |
| `apply_settings` 会清掉 `LrDevelopController` live 状态 | 蒙版必须在调色后打，顺序不可逆 |
| AI 蒙版无独立眼/唇类型 | `person` 在 LR SDK 应映射为 `people`，插件有 bug。用 HSL 红/橙通道替代眼唇调色 |
| 蒙版不持久化到 catalog | 仅 Live 会话有效，翻页自动保存 |

## File Notes

- `.claude-plugin/plugin.json` — 插件清单。`name` 必须与 `skills/` 目录名和 SKILL.md frontmatter `name` 一致（当前为 `LRxiutu`）。
- `skills/LRxiutu/SKILL.md` — `allowed-tools: [Bash, Read]`，实际修图通过 `lightroom-classic` MCP tools 完成（自动可用，无需在 allowed-tools 声明）。
- `references/*.md` — 纯参数表，被 SKILL.md 引用。修改参数时直接编辑对应文件。
