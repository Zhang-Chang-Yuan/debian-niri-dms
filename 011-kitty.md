# Kitty

kitty 由 niri + DMS 安装流程（010-niri-dms.md）中选择安装，无需单独安装，以下仅补充配置。

## 1. 终端字体

沿用 003-fonts.md 的字体方案：JetBrains Mono（西文）+ Noto CJK（中文回退）。

```bash
mkdir -p ~/.config/kitty

nano ~/.config/kitty/kitty.conf
```

```conf
font_family      JetBrains Mono
font_size        11.0
```

- 汉字等 CJK 字形依赖 fontconfig 回退，需先完成 003-fonts.md（fonts-noto-cjk）
- 可用 `kitten choose-fonts` 交互式预览并微调字体

## 2. 光标尾拖

```bash
nano ~/.config/kitty/kitty.conf
```

```conf
# 光标尾迹触发阈值（毫秒，0 关闭）
# 光标停留超过该毫秒数后再移动，才会出现尾迹
cursor_trail      200

# 尾迹衰减时间：最快 / 最慢（秒），值越小消失越快
cursor_trail_decay 0.1 0.4
```

## 3. 透明度

```bash
nano ~/.config/kitty/kitty.conf
```

```conf
# 背景透明度（0 全透明 ~ 1 不透明）
background_opacity 0.85

# 允许运行时动态调整透明度（increase/decrease_background_opacity）
# 默认关闭，开启后有性能开销
dynamic_background_opacity yes
```

- 透明度需 kitty ≥ 0.34 的 Wayland 支持，可与 niri 的窗口模糊配合
- 壁纸为浅色时，建议将 `background` 颜色调整为接近桌面背景以改善文字渲染
