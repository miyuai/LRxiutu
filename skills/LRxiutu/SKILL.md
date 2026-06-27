---
name: LRxiutu
description: This skill should be used when the user asks to "修图", "调色", "胶片", "肤色美白", "磨皮", "清透", "白红透亮", "通透", "发丝光", "逆光", "黄金时刻", "Lightroom", "LR调色", or requests portrait photo editing. Connects to Lightroom Classic via lightroom-classic MCP server.
argument-hint: [修图指令]
allowed-tools: [Bash, Read]
user-invocable: true
---

# Lightroom 智能人像修图

通过 `lightroom-classic` MCP 控制 LR。**每张照片的参数根据 EXIF 数据推算，不套固定模板。**

## 工作流（顺序不可逆）

```
① 重置(develop.reset_current_photo) → 读 EXIF + 原始色温/锐度
② 读 references/ 参数表 → 时段判断 → 推算参数
③ develop.apply_settings → 写入 catalog（持久化）
④ masks.create_ai_mask + set_local_settings → 蒙版（Live 会话）
```

**apply_settings 会清掉蒙版 live 状态，所以蒙版必须在调色全部完成后打。**

## 参数推算

### Step 1: 读照片数据

```python
photo = catalog.get_active_photo()
# → ISO, aperture, shutter_speed, focal_length, capture_time, camera_model
state = develop.get_settings()
# → Temperature, Tint, Sharpness
```

### Step 2: 时段判断 + 加载参数表

从 `capture_time` 提取小时，匹配时段（黄金/日间/蓝调/夜间）。
加载 `references/time-periods.md` 获取该时段的：色温偏移、高光、白色、鲜艳度、去雾、阴影、橙明度、黄饱、颗粒。

### Step 3: 选择肤色风格

默认 A（暖金肤）。用户说"白红透亮"/"肤色偏黄绿" → 切 B 风格。
加载 `references/hsl-styles.md` 获取 HSL + 联动参数。

### Step 4: 应用参数 + 蒙版

调用 `develop.apply_settings` 写入，然后打蒙版。
蒙版策略和清透感维度见 `references/clarity-dim.md`。

### Step 5: 色彩分级

加载 `references/color-grading.md`，按时段选择分级参数。
注意：ColorGrade Highlight/Shadow 的 Hue+Sat 写只读，`set_param` 返回成功即生效。

## 反馈迭代

单参数微调用 `develop.set_param`（不重置蒙版）。

问用户：
- "曝光太亮还是太暗？"
- "肤色偏黄还是偏冷？"
- "清透感够不够？"

## 关键约束

| 问题 | 对策 |
|------|------|
| apply_settings 清蒙版 | 先调色后蒙版，不可逆 |
| 蒙版不持久化 | Live 会话翻页自动保存 |
| ColorGrade Hue+Sat 写只读 | 不验证，LR 界面确认 |
| 无独立眼/唇 AI 蒙版 | 用 HSL 红/橙通道替代 |
