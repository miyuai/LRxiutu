# lightroom-film

Claude Code 插件：Lightroom Classic 智能人像修图。

## 功能

- **时段感知**：6 时段独立参数（黄金时刻/日间/蓝调/夜间）
- **HSL 双风格**：A 暖金肤 / B 白红透亮，一键切换
- **Color Grading**：4 时段色彩分级轮策略
- **清透感维度**：胶片感 ↔ 清透感 11 参数联动滑杆
- **多蒙版策略**：主体磨皮 + 天空压暗 + 发丝轮廓光

## 前置要求

- Adobe Lightroom Classic 运行中
- `lightroom-classic-mcp` 插件已安装并启用
- MCP bridge 端口文件 `/tmp/lightroom_mcp_custom_ports.json` 就绪

## 安装

```bash
# 链接到 Claude Code 插件目录
ln -s "$HOME/Documents/claude code/lightroom-film" "$HOME/.claude-plugins/lightroom-film"
```

## 使用

在 Claude Code 中说：
- "修一下这张" — 自动读取 LR 选中照片并修图
- "肤色偏黄绿" — 自动切到白红透亮 B 风格
- "不够清透" — 自动沿清透感维度滑动
- "黄金时刻逆光" — 自动匹配时段参数 + 发丝光蒙版

## 文件结构

```
skills/lightroom-film/
├── SKILL.md                    # 入口 + 工作流
└── references/
    ├── time-periods.md         # 6时段参数 + 曝光/色温/锐度规则
    ├── hsl-styles.md           # HSL A/B 双风格
    ├── clarity-dim.md          # 清透感维度 + 蒙版策略
    └── color-grading.md        # 色彩分级表
```
