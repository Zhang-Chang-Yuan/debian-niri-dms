# Foliate

## 1. 安装

```bash
sudo apt install foliate
```

- Debian trixie 源，3.3.0（版本号显示为 4.~really3.3.0，是上游 epoch 写法）；GTK 电子书阅读器，支持 epub/mobi/azw3/comic 等
- 桌面项 com.github.johnfactotum.Foliate.desktop，图标随包装入 hicolor，装完即出现在启动器
- 阅读进度等配置在 ~/.config/com.github.johnfactotum.Foliate（apt 安装，不走 Flatpak 的 ~/.var）

## 2. 设为默认电子书打开方式（可选）

未默认修改，需要时执行：

```bash
xdg-mime default com.github.johnfactotum.Foliate.desktop application/epub+zip application/x-mobipocket-ebook application/vnd.amazon.ebook
```

## 3. 说明

- GTK 程序，跟随系统 GTK 主题；输入法（fcitx5）在内置搜索框中可用（见 013-fcitx5.md）
