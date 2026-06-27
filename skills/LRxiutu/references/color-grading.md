# Color Grading 色彩分级

## 黄金时刻（日落前/日出后）

| 轮 | Hue | Sat | Lum |
|----|-----|-----|-----|
| 高光 | 40 (橙金) | 30 | +10 |
| 中间调 | 45 | 15 | 0 |
| 阴影 | 220 (青蓝) | 15 | -5 |
| 混合 | — | — | 50 |

## 日间硬光

| 轮 | Hue | Sat | Lum |
|----|-----|-----|-----|
| 高光 | 40 | 15 | +5 |
| 中间调 | 45 | 8 | 0 |
| 阴影 | 210 | 10 | 0 |
| 混合 | — | — | 50 |

## 蓝调（日出前/日落后）

| 轮 | Hue | Sat | Lum |
|----|-----|-----|-----|
| 高光 | 35 | 10 | +5 |
| 中间调 | 200 | 12 | 0 |
| 阴影 | 220 | 20 | -8 |
| 混合 | — | — | 50 |

## 夜间/室内

| 轮 | Hue | Sat | Lum |
|----|-----|-----|-----|
| 高光 | 40 | 20 | +5 |
| 中间调 | 45 | 10 | 0 |
| 阴影 | 210 | 12 | -5 |
| 混合 | — | — | 50 |

## 参数名

```
ColorGradeHighlightHue/Sat/Lum
ColorGradeMidtoneHue/Sat/Lum
ColorGradeShadowHue/Sat/Lum
ColorGradeBlending
```

> ⚠️ `ColorGradeHighlightHue/Sat` 和 `ColorGradeShadowHue/Sat` 是 LR SDK 写只读——`set_param` 返回成功但 `get_settings` 不返回。Midtone 和所有 Lum 可正常读写。

## 联动

白红透亮 B 风格：高光 Sat→15, 中间调 Hue→30, Sat→8, Lum→0
清透维度：阴影 Sat→3, Lum→+3
