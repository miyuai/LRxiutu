# 清透感 ↔ 胶片感 维度

用户反馈"不够清透"或"太闷"时，沿此表向清透方向滑动。

| 参数 | 胶片感 (默认) | → | 清透感 |
|------|-------------|---|--------|
| Whites2012 | -5 | → | **+20** |
| Dehaze | -8 | → | **-2** |
| Blacks2012 | 15~18 | → | **8** |
| ToneCurve 黑场 y | 12 | → | **3** |
| ToneCurve 高光 y | 248 | → | **255** |
| Clarity2012 | -8 | → | **-5** |
| Contrast2012 | -5 | → | **-2** |
| ColorGrade 阴影 Sat | 15 | → | **3** |
| ColorGrade 阴影 Lum | -5 | → | **+3** |
| Vignette | -12 | → | **-4** |
| Green Sat | -15 | → | **-10** |

> 胶片 = 压白+抬黑+雾+暗角 = 厚重；清透 = 提白+收黑+减雾+减暗角 = 空气感。不能同时拉满。

## 蒙版策略

调色写入目录后打蒙版（`apply_settings` 会清 live 状态，顺序不可逆）。

### 蒙版 1：主体磨皮（必打）

```python
masks.create_ai_mask(mask_type="subject", operation="new")
masks.select_mask(mask_id=0)
masks.set_local_settings({"settings": {
    "local_Texture": -0.8,    # 磨皮
    "local_Clarity": -0.5,    # 柔光
    "local_Exposure": 0.20,   # 面部提亮 (清透→0.22)
    "local_Sharpness": 0.5,   # 眼神光
    "local_Contrast": 0.12,   # 微立体
    "local_Dehaze": -0.25,    # 朦胧感
}})
```

### 蒙版 2：天空压暗（可选）

```python
masks.create_ai_mask(mask_type="sky", operation="add")
masks.set_local_settings({"settings": {
    "local_Highlights": -0.10,   # 清透→-0.10; 胶片→-0.20
    "local_Exposure": -0.05,     # 清透→-0.05; 胶片→-0.15
}})
```

### 蒙版 3：发丝轮廓光（黄金时刻）

全局 Highlights -35 会压掉发丝光，用蒙版刷回：
```python
masks.create_ai_mask(mask_type="subject", operation="add")
masks.set_local_settings({"settings": {
    "local_Highlights": 0.25,
    "local_Whites": 0.15,
    "local_Temperature": 0.08,
    "local_Clarity": 0.10,
}})
```

## 暗角 & 颗粒（清透方向减半）

| 参数 | 值 |
|------|-----|
| PostCropVignetteAmount | -12 (清透→-4) |
| PostCropVignetteMidpoint | 55 |
| PostCropVignetteFeather | 60 |
| GrainAmount | 时段表值 |
| GrainSize | 25 |
| GrainFrequency | 50 |
